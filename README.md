
# HTML to Macro String Converter

This project is designed to automate the process of converting HTML files into C/C++ macros, making it easier to embed HTML code in projects such as web servers running on ESP32. This tool reads HTML files and converts them into string macros that can be directly used in C/C++ code, thus eliminating the need for manual conversion. It is simple to include this tool in the pre-build process of any project, such as ESP-IDF, to ensure HTML content is always up-to-date.

## Background

While developing a web server with the ESP_IDF, I found that embedding HTML directly in the code by converting it to strings manually was cumbersome and error-prone. I created this tool to automate the process, allowing for easy updates and modifications to the HTML content without manual conversion. This not only saves time but also ensures that the HTML content is always up-to-date and correctly formatted as macros.

I could have placed the HTML inside a storage partition with SPIFFS, avoiding the use of macros. However, I prefer to keep the HTML in the source code because when performing OTA (Over-the-Air) firmware updates, everything updates at once. If I had placed the HTML in the SPIFFS partition, I would have had to manage the OTA for that partition as well, which I wanted to avoid.

## Features

- Converts HTML files to C/C++ macros.
- Supports optional HTML minification.
- Inserts generated macros into a specified file (for example a header file (.h)).
- Handles both absolute and relative file paths.
- Preserves and minifies JavaScript content within `<script>` tags.

## Usage

### Example Preparing Output File

**html_macros.h:**

```c
#ifndef HTML_MACROS_H_
#define HTML_MACROS_H_

/* START HTML MACROS AREA */

/* END HTML MACROS AREA */

#endif // HTML_MACROS_H_
```

The tool will insert the generated macros between the `START HTML MACROS AREA` and `END HTML MACROS AREA` markers, replacing any existing content in that section.
Anything outside of the markers will not be modified, so feel free to add anything to the outside area.

### Example HTML Content

**index.html:**

```html
<!DOCTYPE html>
<html>
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Welcome to the Index Page</h1>
    <script>
        console.log("Index Page");
    </script>
</body>
</html>
```

**about.html:**

```html
<!DOCTYPE html>
<html>
<head>
    <title>About Page</title>
</head>
<body>
    <h1>About Us</h1>
    <script>
        console.log("About Page");
    </script>
</body>
</html>
```

### Command Line Arguments

1. **Minify**: Boolean value indicating whether the HTML should be minified (`true` or `false`).
2. **Output File Path**: The path to the file where the macros will be inserted.
3. **HTML File Path**: The path to the HTML file to be converted.
4. **Macro Name**: The name of the macro to be created from the HTML file.

These arguments can be repeated for multiple HTML files.

### Example Command Line Usage

```bat
python html_to_macro.py true ".\output\html_macros.h" ".\html\index.html" "INDEX_HTML" ".\html\about.html" "ABOUT_HTML"
```

In this example:
- HTML will be minified.
- The locations of the files are relative to where the Python script is located.
- The output file is located at `.\output\html_macros.h`.
- Two HTML files (`index.html` and `about.html`) are located at `.\html\` which will be converted to macros named `INDEX_HTML` and `ABOUT_HTML`, respectively.


### Example Output File Updated (Minify = true)

Assuming the parameter `minify` is set to `true`, the output file (`html_macros.h`) will look like this:

```c
#ifndef HTML_MACROS_H_
#define HTML_MACROS_H_

/* START HTML MACROS AREA */

/*
 * Automatically generated code by 'HTML-to-Macro-String-Converter'
 * containing the HTML code converted to macros.
 * The parameters are:
 *    Minify: true
 *    Output: .\output\html_macros.h
 *    Input[1]: .\html\index.html, Macro: INDEX_HTML
 *    Input[2]: .\html\about.html, Macro: ABOUT_HTML
 *
 * Project Repository: https://github.com/moschiel/html-to-macro-string
 */

#define INDEX_HTML "<!DOCTYPE html><html><head><title>Index Page</title></head><body><h1>Welcome to the Index Page</h1><script>console.log('Index Page');</script></body></html>"

#define ABOUT_HTML "<!DOCTYPE html><html><head><title>About Page</title></head><body><h1>About Us</h1><script>console.log('About Page');</script></body></html>"

/* END HTML MACROS AREA */

#endif // HTML_MACROS_H_
```

### Example Output File Updated (Minify = false)

Assuming the parameter `minify` is set to `false`, the output file (`html_macros.h`) will look like this:

```c
#ifndef HTML_MACROS_H_
#define HTML_MACROS_H_

/* START HTML MACROS AREA */

/*
 * Automatically generated code by 'HTML-to-Macro-String-Converter'
 * containing the HTML code converted to macros.
 * The parameters are:
 *    Minify: false
 *    Output: .\output\html_macros.h
 *    Input[1]: .\html\index.html, Macro: INDEX_HTML
 *    Input[2]: .\html\about.html, Macro: ABOUT_HTML
 *
 * Project Repository: https://github.com/moschiel/html-to-macro-string
 */

#define INDEX_HTML \
"<!DOCTYPE html>\n"\
"<html>\n"\
"<head>\n"\
"    <title>Index Page</title>\n"\
"</head>\n"\
"<body>\n"\
"    <h1>Welcome to the Index Page</h1>\n"\
"    <script>\n"\
"        console.log(\"Index Page\");\n"\
"    </script>\n"\
"</body>\n"\
"</html>\n"\
""

#define ABOUT_HTML \
"<!DOCTYPE html>\n"\
"<html>\n"\
"<head>\n"\
"    <title>About Page</title>\n"\
"</head>\n"\
"<body>\n"\
"    <h1>About Us</h1>\n"\
"    <script>\n"\
"        console.log(\"About Page\");\n"\
"    </script>\n"\
"</body>\n"\
"</html>\n"\
""

/* END HTML MACROS AREA */

#endif // HTML_MACROS_H_
```

## Automated Pre-Build for ESP-IDF

When developing a web server with the ESP-IDF, if not using SPIFFS to store HTML files, it's common to embed HTML content directly into the firmware. Manually converting HTML files to C/C++ string literals is tedious and error-prone. This tool automates the conversion process, making it easy to update and manage HTML content within your ESP-IDF projects.

### Example Configuration for ESP-IDF

1. **Place the Script**: Ensure `html_to_macro.py` is located at the root of your ESP-IDF project directory.
   
2. **Place the Output and HTML Files**: Ensure the output and html files are located at './main' and './html' folders at the root of your ESP-IDF project directory.

3. **Ensure the Output File Contains the Makers**: Make sure your output header file (e.g., `html_macros.h`) contains the start and end markers.

4. **Modify CMakeLists.txt**: Add a custom target to run the `HTML to Macro String Converter` before the build process. Below is an example of how to modify your `CMakeLists.txt`:

    ```cmake
    cmake_minimum_required(VERSION 3.5)

    include($ENV{IDF_PATH}/tools/cmake/project.cmake)
    project(my_project)

    # Add this section to run your HTML to Macro String Converter
    add_custom_target(
        run_html_to_macro
        COMMAND ${CMAKE_COMMAND} -E echo "Running HTML to Macro String Converter"
        COMMAND ${CMAKE_COMMAND} -E chdir ${CMAKE_SOURCE_DIR} ${PYTHON} html_to_macro.py true "./main/html_macros.h" "./html/index.html" "INDEX_HTML" "./html/about.html" "ABOUT_HTML"
        WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
        COMMENT "Executing HTML to Macro String Converter before building"
    )

    add_dependencies(app run_html_to_macro)
    ```


### Building the Project

Run the following command to build your project:

```sh
idf.py build
```

This command will execute the HTML to Macro String Converter before compiling the ESP-IDF project, ensuring that your HTML content is always up-to-date and correctly formatted as C/C++ macros.

## Important Notes

- If a file path starts with `.\`, it is treated as a relative path to the script's directory.
- The output file must exist beforehand, and it should contain the markers `/* START HTML MACROS AREA */` and `/* END HTML MACROS AREA */` to indicate where the macros should be inserted.

## Error Handling

- If the output file ot the html files does not exist, the tool will report an error.
- If the start or end markers are not found, the tool will report an error.

## Contributing

Contributions are welcome
