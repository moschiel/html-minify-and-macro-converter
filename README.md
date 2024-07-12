
# HTML Minify And Macro Converter

This project is designed to automate the process of minifying HTML files, making it easier to embed HTML code in projects such as web servers running on ESP32. Additionally, it offers an option to stringfy the minified HTML files into C/C++ macros for embedding directly in source code, thus eliminating the need for manual conversion. Minifying HTML is especially important in embedded systems where memory resources are limited. It is simple to include this tool in the pre-build process of any project to ensure the minified HTML content is always up-to-date.

## Background

While developing a web server with the ESP-IDF, I found that embedding HTML directly in the code by converting it to strings manually was cumbersome and error-prone. I created this tool to automate the process, allowing for easy updates and modifications to the HTML content embedded on my firmware.

## Features

- Minifies HTML files.
- Optionally stringfy the minified HTML files to C/C++ macros.
- Option to keep or remove line breaks in the minified output.

## Usage

### Example HTML Content to be Minified

These are the HTML files that will be minified in this tutorial

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

### Example Preparing File for Macros

If using `minify-to-macro` mode, the file where the macros will be stored must exist beforehand.
The tool will insert the generated macros between the `START HTML MACROS AREA` and `END HTML MACROS AREA` markers, replacing any existing content in that section.
Anything outside of the markers will not be modified, so feel free to add anything to the outside area.

This is the file that will be used to place de macros in this tutorial.

**html_macros.h:**

```c
#ifndef HTML_MACROS_H_
#define HTML_MACROS_H_

/* START HTML MACROS AREA */

/* END HTML MACROS AREA */

#endif // HTML_MACROS_H_
```



### Command Line Usage Examples

In the following examples:
- The HTML files `index.html` and `about.html` will be minified.
- In `minify` mode, the minified files will be saved with the prefix  `min_` (`min_index.html` and `min_about.html`) in the same directory
- In `minify-to-macro` mode, the provided HTML files will be minified and stringified to C/C++ macros, the output is placed in `html_macros.h` between the `START HTML MACROS AREA` and `END HTML MACROS AREA` markers.

#### Example 1 - Minify:
```sh
python html_minify.py minify ./index.html ./about.html
```
##### Expected Output:
**min_index.html:**
```html
<!DOCTYPE html><html><head><title>Index Page</title></head><body><h1>Welcome to the Index Page</h1><script>console.log("Index Page");</script></body></html>
```
**min_about.html:**
```html
<!DOCTYPE html><html><head><title>About Page</title></head><body><h1>About Us</h1><script>console.log("About Page");</script></body></html>
```

#### Example 2 - Minify and preserve line breaks:
```sh
python html_minify.py minify keep-line-break ./index.html ./about.html
```
##### Expected Output:
**min_index.html:**
```html
<!DOCTYPE html>
<html>
<head><title>Index Page</title></head>
<body>
<h1>Welcome to the Index Page</h1>
<script>console.log("Index Page");</script>
</body>
</html>
```
**min_about.html:**
```html
<!DOCTYPE html>
<html>
<head><title>About Page</title></head>
<body>
<h1>About Us</h1>
<script>console.log("About Page");</script>
</body>
</html>
```

#### Example 3 - Minify and convert to macros:
```sh
python html_minify.py minify-to-macro ./html_macros.h ./index.html ./about.html
```
##### Expected Output:
**html_macros.h:**
```c
#ifndef HTML_MACROS_H_
#define HTML_MACROS_H_

/* START HTML MACROS AREA */

/*
 * Automatically generated macros by 'HTML-Minify-and-Macro-Converter v2.0'
 * containing the HTML code converted to macros.
 * The parameters are:
 *    Output: ./html_macros.h
 *    Input[1]: ./index.html, Macro: INDEX_HTML
 *    Input[2]: ./about.html, Macro: ABOUT_HTML
 *
 * Project Repository: https://github.com/moschiel/html-minify-and-macro-converter
 */

#define INDEX_HTML "<!DOCTYPE html><html><head><title>Index Page</title></head><body><h1>Welcome to the Index Page</h1><script>console.log(\"Index Page\");</script></body></html>"

#define ABOUT_HTML "<!DOCTYPE html><html><head><title>About Page</title></head><body><h1>About Us</h1><script>console.log(\"About Page\");</script></body></html>"

/* END HTML MACROS AREA */

#endif // HTML_MACROS_H_
```

#### Example 4 - Minify and convert to macro while preserving line breaks:
```sh
python html_minify.py minify-to-macro keep-line-break ./html_macros.h ./index.html ./about.html
```
##### Expected Output:
**html_macros.h:**
```c
#ifndef HTML_MACROS_H_
#define HTML_MACROS_H_

/* START HTML MACROS AREA */

/*
 * Automatically generated macros by 'HTML-Minify-and-Macro-Converter v2.0'
 * containing the HTML code converted to macros.
 * The parameters are:
 *    Output: ./html_macros.h
 *    Input[1]: ./index.html, Macro: INDEX_HTML
 *    Input[2]: ./about.html, Macro: ABOUT_HTML
 *
 * Project Repository: https://github.com/moschiel/html-minify-and-macro-converter
 */

#define INDEX_HTML "<!DOCTYPE html>" \
"<html>" \
"<head>" \
"<title>Index Page</title>" \
"</head>" \
"<body>" \
"<h1>Welcome to the Index Page</h1>" \
"<script>console.log(\"Index Page\");</script>" \
"</body>" \
"</html>"

#define ABOUT_HTML "<!DOCTYPE html>" \
"<html>" \
"<head>" \
"<title>About Page</title>" \
"</head>" \
"<body>" \
"<h1>About Us</h1>" \
"<script>console.log(\"About Page\");</script>" \
"</body>" \
"</html>"

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

