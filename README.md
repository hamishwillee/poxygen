# poxygen

Python script(s) to convert Doxygen XML into Gitbook-markdown.

## Overview

This Python script can be used to generate Gitbook-compatible markdown from Doxygen C++ XML. An example of the rendered output can be seen on gitbook here: [DroneCore](https://docs.dronecore.io/en/api_reference/classdronecore_1_1_telemetry.html).

The location of the XML and output files is specified in the script (relative to DOXYGEN_ROOT_DIR).

The output format currently "largely" follows DOxygen HTML output and is fully linked. 

At time of writing it only supports C++ `classes` and `struct`. Files for other XML types are ignored.

**Alternatives:** A more fully-featured alternative is [moxygen](https://github.com/sourcey/moxygen), which is written in *node*. 
This is also still in "beta" and has some issues with rendering. 
The benefit of *poxygen* over *doxgyen* is that it is written in Python, which means that the code is very easy to grok and modify as needed.

## Testing & Disclaimer

This code was created for the [DroneCore](https://github.com/dronecore/DroneCore/) project and creates API Reference for its public API. This is a relatively small API consisting only of classes. This may well not work on other C++ code bases, or may require modification.

The good news is that the tool reports any files it is ignoring or can't handle, and also any tag types it can't handle. 

## How it works

The script imports XML files from the doxygen /xml directory and maps them to Python objects representing the C++ types. These objects have functions to export their content in markdown in both "overview" and "body" formats. 

- There are Python objects for each C++ type - e.g. cppClass, cppTypdef etc. (cppClass covers both class and struct)
- Each object has a method `import_from_doxygen_class_file()` which takes a Tag (or filename for CPP) and parses in data. 
  - The cpp class takes a file, and creates the sub objects it owns (e.g. methods), calling their import_from_doxygen_class_file() method with a tag.
- Each object (mostly) has a markdown_overview() and markdown() method that renders the markdown from the respective objects
- markdown_any_tag() - 
  - Some objects/values will be empty and others will store tagged information that is only partially parsed from XML (in theory this allows us 
  to change the output rendering, because we store it in generic format). The markdown_any_tag() method can be used to get 
  markdown text or an empty string from any stored attribute. 
  - This can be safely called on any "data" type attributed
  - This will report an error on console if an unsupported tag is detected. The tag will render in output anyway, but may contain odd tags.
- cleanup_markdown_string
  - Just cleans up a little of the markdown.
