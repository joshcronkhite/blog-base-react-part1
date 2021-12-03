# Configure a Base ReactJS App Manually - Part 1

There are many tutorials out there that will get you up and running with ReactJS. Some are more detailed than others, but most never break down the configuration. As long as you copy the config files according to the tutorials, things work for you. But you don't know why. As I try to understand this myself, I thought I'd document my process here in hopes that another might benefit from what I've learned.

## Creating the basic project

First create a directory and name if for your project. I'm going to create one named ```demo``` using my terminal:

```mkdir demo```

Navigate into your new directory:

```cd demo```

Next I'll open VSCode to begin creating the required files and directories. I will use the built-in terminal in VSCode, but most often, I'll use Windows Terminal at work or iTerm2 on my MacBook for other projects. Launch VSCode from within your new directory:

```code .```

Open the the built-in terminal from the keyboard using ```CTRL + ` ```

We're going to need some basic directories for ReactJS, so let's create those next. In the root of your project, create the ```src``` and ```public``` folders. Inside of ```public```, create ```index.html``` which will open in your editor. Type ```html:5``` and you'll see that this is a template for some boilerplate HTML. This is a [Emmet Template](https://www.emmet.io). Hit ```tab``` to accept the template. Add ```<div id="root"></div>``` inside of the ```<body>``` tag. Your file should look similar to this:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="root"></div>
</body>
</html>
```

To wrap up our base setup, we'll initialize npm and git, and install ReactJS.

Initialize npm:

```npm init -y```

This will create a default ```package.json``` file accepting defaults. It should look similar to this:

```
{
  "name": "demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}

```

Now let's take care of git:

```git init```

Then create a ```.gitignore``` file. You can craft this to deal with your project however you'd like as you build things out. For now, my primary concern is to ignore all node modules. Add ```node_modules``` to line 1 of your ignore file and save it. There is much to add later to this ignore file, but we won't be exploring this here.


## Install React

This is a manual setup, so we're obviously not using [CRA](https://create-react-app.dev). While this is a great solution, we want more control over what gets installed and how things are configured. Let's install ReactJS. From the console, run the following:

```npm i react react-dom```

We have our basic packages, but later will need a router and likely other packages depending upon what you desire for your project.

Our app already has the public HTML file we need for ReactJS, but we still need to add at minimum two files to run. In the ```src``` directory, create a file named ```index.js``` and another named ```App.js```.

> I recommend installing the [ES7 React/Redux/GraphQL/React-Native snippets](https://marketplace.visualstudio.com/items?itemName=dsznajder.es7-react-js-snippets) extension for VSCode.

Open ```App.js``` and type ```rfce``` which is snippet for a React Functional Export Component. It will automatically name your component based on the filename. If you would prefer to type the code for yourself, here's what you'll need:

```
import React from 'react'

function App() {
    return (
        <div>
            
        </div>
    )
}

export default App

```

Now open your ```index.js``` file and input the following code:

```
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App'

ReactDOM.render(<App />, document.querySelector('#root'))
```

This code imports the React libraries we need to make our project a ReactJS application. We also import our newly created App component, and using the ```ReactDOM```, render it inside of the ```<div id='root'></div>``` of our HTML file.

## Getting Dev Environment Setup

We have some base files ready to go, but we have no way of building/running the project at the moment. There's a lot to handle beyond even that. Modern web development requires that we handle things like minification, transpilation, chunking and other optimizations. It's not simply a matter of HTML/CSS/JS in a static sense without regard for the user experience. We don't spend their resources as if they're limitless. We must deliver optimized applications that respect our users. We can accomplish this by configuring our dev environment with the right tooling. As developers, we take the hit in terms of tools rather than dumping unoptimized files on our users. I could go on, but suffice it to say that all of this informs our configuration in devopment.

I will be using Webpack at the core of my dev environment. Then I will be plugging in more tools that ultimately work together to provide for what I think is a decent starter environment for ReactJS.

## Webpack & Babel

> There are shortcut commands to use in the Node console so feel free to use those if you wish. The commands shown here may seem overly verbose, but it's how I operate.

Install Webpack:

```npm install --save-dev webpack webpack-cli webpack-dev-server```

Next install the ```HtmlWebpackPlugin``` which is used to create the output HTML files that we'll be serving:

```npm install --save-dev html-webpack-plugin```

Before we configure Webpack, we need to install a JavaScript compiler. We'll use Babel. This allows us to write modern JS while outputting compatible code for browsers that don't support the later versions of JavaScript. Keep in mind we're working with ReactJS and Webpack, so we need to install some supporting packages to accommodate our configuration.

```npm install --save-dev @babel/core @babel/preset-react babel-loader```

Now that these are installed, have a look at your ```package.json``` file's contents. If you've installed your packages in the proper locations (dependencies vs devDependencies), your file should look similar to the following:

```
{
  "name": "demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "react": "^17.0.2",
    "react-dom": "^17.0.2"
  },
  "devDependencies": {
    "@babel/core": "^7.16.0",
    "@babel/preset-react": "^7.16.0",
    "babel-loader": "^8.2.3",
    "html-webpack-plugin": "^5.5.0",
    "webpack": "^5.64.1",
    "webpack-cli": "^4.9.1",
    "webpack-dev-server": "^4.5.0"
  }
}

```

You can see we've setup React to run as a standard dependancy while the rest are installed as developer dependancies. Our dev environment should usually be heavier than what we serve up, so this looks good.

We're almost there, but still need to configure everything we've just installed to work together and serve up our app in our development environment.

## Configuration

That last thing we need to do is configure our base ReactJS app. There are two files we'll need to create: a Babel configuration file and a Webpack configuration file.

Create ```.babelrc``` in the root of your project. Note the ```.``` in front of the filename. We need to add a line to this new file that enables a preset for ReactJS. Essentially, today's browsers do not understand JSX, so this preset allows Babel to convert our code for use by our users. Add the following to your ```.babelrc``` file:

```
{
    "presets": ["@babel/preset-react"]
}
```
That's all we need for the preset configuration. Next we will be working through Webpack's configuration, which requires some explanation.

Let's create our webpack config file. Create a new file in the root of your project named ```webpack.cofnig.js```.

> Check out [CreateApp.dev](https://createapp.dev/webpack/no-library) to explore different configurations.

Here's my config file. Feel free to copy/paste this into your new file.

```
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  devServer: {
    historyApiFallback: true,
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
    chunkFilename: '[id].js',
  },
  resolve: {
    extensions: ['*', '.js', '.jsx', '.css'],
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        include: path.resolve(__dirname, 'src'),
        exclude: /(node_modules|bower_components|build)/,
        use: ['babel-loader'],
      }
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: __dirname + '/public/index.html',
      filename: 'index.html',
      inject: 'body',
    }),
  ],
};

```

So what's in this file? Let's start from the top and break it down in simple terms.

### *entry*
configures our root app file, which in our case is located at ```'./src/index.js'```.

### *devServer*
set to ```true``` when relying on HTML5 History API

### *output* 
* *path* - sets up where our generated files are output. You'll see ```__dirname```, which is a Node.js variable that represents the path of the current module.
* *filename* - the file that will be output

### *resolve*
* *extensions* - an array of file extensions for your project. Webpack maps these extensions to module paths until it finds a match.

### *module*
* *rules* (*loaders* in older versions)
    * *test* - this regex value is how we tell our rule which file types should go through the loader we're using
    *  *include* - the path to the source files
    *  *exclude* - pipe-delimited list of paths the loader should ignore
    *  *use* - the loader we're using, in our case Babel

### *plugins* tells Webpack which plugins we want to use
* *new* ```HtmlWebpackPlugin``` - the name of the plugin
    * *template* - the location of our app's template
    * *filename* - the file to write the HTML to
    * *inject* - injects all assets into the given template. Options are ```true```, ```head```, ```body``` or ```false```.

Finally, we need to edit our ```package.json``` file to enable our local dev server to run in node. In the scripts section, add a comma ```,``` to the end of the line ```"test": "echo \"Error: no test specified\" && exit 1"``` and on the next line input the following:

```"start": "webpack-dev-server --open --hot --mode development"```

Save your ```package.json``` file.

## Run Your App

We should now have a base manually-configured ReactJS application. To give it a spin, run the following command:

```npm run start```

You should see a blank page served to your browser from localhost. This doesn't really prove that all is well. Let's add a ```h1``` to our ```App``` component and watch the page update upon save. Here's my ```App.js``` file after adding the header:

```
import React from 'react'

function App() {
    return (
        <div>
            <h1>Demo</h1>
        </div>
    )
}

export default App

```
This should have loaded your page with the new header automatically thanks to Webpack's hot reload.

That's it for now. This should get you going on a super basic project. If you're new ReactJS, take this opportunity to get familiar with everything here before moving on.

> This is Part 1 in the series *Configure a Base ReactJS App Manually*. Parts 2 through 4 are coming soon and will be as follows:

*Configure a Base ReactJS App Manually - Part 2*: Linting and Formatting

*Configure a Base ReactJS App Manually - Part 3*: CSS in JS using Styled Components

*Configure a Base ReactJS App Manually - Part 4*: Implement a Design System

