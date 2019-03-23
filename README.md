# Predator
Fast, featherweight single-web app documentation generator written in native, object-oriented JavaScript (ES6+) - no framework used.   
     
In the previous iterations of this project, Predator read the sidebar structure, labels and paths to content files from a `predator.json` file. You had to create file in the `docs/` directory and then "link it" in the JSON file.   
    
In this version, I'll make it render the sidebar structure, labels etc. directly from the `docs/` directory file structure and it's files, so we don't even have to touch the JSON file.   

## Example
The following file structure in the `docs/` directory:
```
docs/                     <- all the content goes here        
  HOME/                   <- name of the tab
    Setup/                <- first top-level accordion
      Installation.html
      Configuration.md
    Guide/                <- second top-level accordion
      Getting-Started/    <- nested accordion
        Lorem-1.md
        Lorem-2.md
        Lorem-3.html
      Diving-Deeper/      <- nested accordion
        Ipsum-1.html
        Ipsum-2.md
        Ipsum-2.html
```
Should render following sidebar structure in the HOME tab: // explain tabs (ss)
```
Setup             \/ |
  * Installation     |
  * Configuration    |
Guide             \/ |
  Getting Started \/ |
    * Lorem 1        |
    * Lorem 2        |
    * Lorem 3        |
  Diving Deeper   \/ |
    * Ipsum 1        |
    * Ipsum 2        |
    * Ipsum 3        |
```

I am not sure if all the operations can be done from browsers JavaScript, like reading the file structure of the `docs/` folder, or if I'll need to use Node. I'm sure I'll learn a lot while implementing all this though.
   
The files content files in `docs/` can be either markdown or HTML files:
- HTML for custom, possibly interactive pages where you can use your css and javascript, I'll need to inject the css and js from the AJAX request somehow.
- Markdown for simple documents - it's much easier to write.




## Development
I decided to learn Webpack (v4.29.6) and use it for this project.   
   
I'd like not to add any additional tooling like task runners or npm scripts, so I'll try do everything with Webpack:
- bundling JavaScript modules, transpiling it down to ES5 and minifying it
- compiling sass, prefixing the css and minifying it
- minifying HTML

### Install Webpack
It is recommended to install Webpack locally for each project. Of course, we'll need Node.js installed.
```
npm install --save-dev webpack webpack-cli
```

### Push to gh-pages
```
git subtree push --prefix dist origin gh-pages
```