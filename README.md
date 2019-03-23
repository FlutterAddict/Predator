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
- bundling JavaScript modules, transpiling it down to ES5 and minifying it,
- compiling sass, prefixing the css and minifying it,
- minifying HTML.
   
I'll make some notes here.   

### Webpack

#### Install
It is recommended to install Webpack locally for each project. Of course, we'll need Node.js installed.
```
npm install --save-dev webpack webpack-cli
```

#### Run
As it is installed locally, not globally, we can't just run `webpack`:
```
> webpack
'webpack' is not recognized as an internal or external command,
operable program or batch file.
```

##### npm scripts
The `webpack` command is accessible from npm scripts.   
Those are located in `package.json` "script" block:
```
"scripts": {
  "build": "webpack"
}
```
Running `npm run build` will work as expected.

##### npx
You can also run it like that: `npx webpack`.   
This, under the hood will find the local installation of webpack and run its binary version.   


#### Configure
Webpack by default has some configuration that grab the script at `src/index.js` and will generate `dist/main.js` as the output.   

If we don't provide it a configuration file, it will run by the default configuration.   

By default, when we run `npx webpack`, it searches for configuration in the root directory of our project in the file named `webpack.config.js`.

However, we are not limited to one configuration file and one location. We can name configuration files however we want and store them wherever we want.

Let's say that our configuration file is in `webpack/release.js` and we want apply it.   

We can do this by running webpack with `--config` parameter, just like that:
```
> npx webpack --config webpack/release.js
```

##### Loading CSS
To import a CSS file from within a JS module, we need to install and add to module configuration two loaders:
- `css-loader` which interprets `@import` and `url()` like `import/require()` and will resolve them
- `style-loader` which injects the `<style>` tag tag to the HTML DOM

###### Install
```
npm install --save-dev css-loader style-loader
```

###### Add Loaders
Now we need to add the loaders to our module configuration:
```
const path = require('path');

module.exports = {
  entry: './src/js/app.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'app.js'
  },
  module: {
    rules: [
      { test: /\.css$/, use: ['style-loader', 'css-loader'] }
    ]
  }
};
```

Now any file with a name that passes the test, will be server to specified loaders.   
In this case, any files ending with `.css` will be served to `style-loader` and `css-loader`.   

###### Load the CSS
That's the final step. So how do we load it? We gave Webpack only one entry point, which is the `src/js/app.js` file, and there we go.   
We load CSS through the JavaScript:
```
import '../css/app.css';
```
Thanks to `css-loader` we can do this, and `style-loader` will automatically apply the styles to the HTML. Nice!


### Push to gh-pages
```
git subtree push --prefix dist origin gh-pages
```