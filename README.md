
# HTML Minify And Macro Converter

This project is designed to automate the process of minifying HTML files, making it easier to embed HTML code in projects such as web servers running on ESP32. Additionally, it offers an option to stringfy the minified HTML files into C/C++ macros for embedding directly in source code, thus eliminating the need for manual conversion. Minifying HTML is especially important in embedded systems where memory resources are limited. It is simple to include this tool in the pre-build process of any project to ensure HTML content is always up-to-date.

## Background

While developing a web server with the ESP-IDF, I found that embedding HTML directly in the code by converting it to strings manually was cumbersome and error-prone. I created this tool to automate the process, allowing for easy updates and modifications to the HTML content without manual conversion. This not only saves time but also ensures that the HTML content is always up-to-date and correctly formatted.

## Features

- Minifies HTML files.
- Optionally stringfy the minified HTML files to C/C++ macros.

## Usage (Minify)

In `minify` mode, the provided HTML files will be minified.

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

### Command Line Arguments - Minify Mode

1. **Mode**: The operation mode (`minify`).
2. **File Paths**: Paths to the HTML files to be minified.

In `minify` mode, provide the HTML file paths to be minified. The minified files will be generated in the same directory with the prefix `min_`.

```sh
python html_minify.py minify <html_file_path_1> <html_file_path_2> ...
```

### Example Command Line Usage

```sh
python html_minify.py minify ./index.html ./about.html
```

In this example:
- The HTML files `index.html` and `about.html` will be minified.
- The minified files will be saved as `min_index.html` and `min_about.html` in the same directory, their minified versions are shown shown below.
- The locations of the files are relative to where the Python script is located.

### Example Output of Minified HTML

**min_index.html:**
```html
<!DOCTYPE html><html><head><title>Index Page</title></head><body><h1>About Us</h1><script>console.log("Index Page");</script></body></html>
```

**min_about.html:**
```html
<!DOCTYPE html><html><head><title>About Page</title></head><body><h1>About Us</h1><script>console.log("About Page");</script></body></html>
```




## Usage (Minify to Macro)

In `minify-to-macro` mode, the provided HTML files will be minified and stringified to C/C++ macros.

### Example Preparing File for Macros

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

### Command Line Arguments - Minify-to-Macro Mode

1. **Mode**: The operation mode (`minify-to-macro`).
2. **Macro File Path**: The path to the header file where the macros will be inserted.
3. **HTML File Paths**: Paths to the HTML files to be converted into C/C++ stringified macros.

In `minify-to-macro` mode, provide the path to the output header file followed by the HTML file paths to be converted into macros.

```sh
python html_minify.py minify-to-macro <output_file_path> <html_file_path_1> <html_file_path_2> ...
```

### Example Command Line Usage

```sh
python html_minify.py minify-to-macro ./html_macros.h ./index.html ./about.html
```

In this example:
- The HTML files `index.html` and `about.html` will be minified and stringified to C/C++ macros.
- The macros will be inserted into `./html_macros.h` between the `START HTML MACROS AREA` and `END HTML MACROS AREA` markers.
- The name given to the macro is a all-capitalized version of the file name

### Example Output File Updated with Macros 

```c
#ifndef HTML_MACROS_H_
#define HTML_MACROS_H_

/* START HTML MACROS AREA */

/*
 * Automatically generated macros by 'HTML-Minify-and-Macro-Converter v2.0'
 * containing the HTML code converted to macros.
 * The parameters are:
 *    Output: ./main/html_macros.h
 *    Input[1]: ./html/index.html, Macro: INDEX_HTML
 *    Input[2]: ./html/about.html, Macro: ABOUT_HTML
 *
 * Project Repository: https://github.com/moschiel/html-minify-and-macro-converter
 */

#define INDEX_HTML "<!DOCTYPE html><html><head><title>Index Page</title></head><body><h1>Welcome to the Index Page</h1><script>console.log(\"Index Page\");</script></body></html>"

#define ABOUT_HTML "<!DOCTYPE html><html><head><title>About Page</title></head><body><h1>About Us</h1><script>console.log(\"About Page\");</script></body></html>"

/* END HTML MACROS AREA */

#endif // HTML_MACROS_H_
```

## Important Notes

- If using `minify-to-macro`, the output file must exist beforehand, and it should contain the markers `/* START HTML MACROS AREA */` and `/* END HTML MACROS AREA */` to indicate where the macros should be inserted.

## Error Handling

- If the HTML files do not exist, the tool will report an error.
- In `minify-to-macro`, if the output file does not exist, the tool will report an error.
- In `minify-to-macro`, if the start or end markers are not found, the tool will report an error.

## Contributing

Contributions are welcome! Please feel free to submit a pull request or open an issue to discuss improvements or bug fixes.

## License

This project is licensed under the MIT License.

