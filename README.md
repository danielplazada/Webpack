# Webpack
Webpack Initial Setup

## Install Webpack and the command line instructions (CLI) tool using npm:

    npm install webpack webpack-cli 
    npm init -y

## In package.json, verify if webpack and webpack-cli are added to the "dependencies" as:

    "dependencies": {
    "express": "^4.17.1",
    "webpack": "^4.43.0",
    "webpack-cli": "^3.3.11"
    },

## In package.json, add a build npm script as:

    "scripts": {
    "build": "webpack"
    },

## Lastly, verify the dependency for the development in     "devDependencies" block as:

    "devDependencies":{
    "webpack-dev-server": "^3.11.0",
    },

## Create a webpack.config.js file in the root location of your project, and add the necessary require statements, and a blank module.exports code as:

    const path = require("path")
    const webpack = require("webpack")
    module.exports = {
    }

## Add Webpack Entry on webpack.config.js file: 

    module.exports = {
    entry: './src/client/index.js'
    }

## For building the app, you can use either npm run build.
## After running the build command successfully, verify that a dist directory is created in the root, containing the bundled file main.js.
    
    npm run build

## For now, hardcode a reference to your dist javascript into index.html (in the views folder of client)

    <script type="text/javascript" src="../../../dist/main.js"></script>

## Install Babel via npm. Babel

    npm i -D @babel/core @babel/preset-env babel-loader

## Create a .babelre file in root of the project, and use the following code:

    { ‘presets’: ['@babel/preset-env'] }
    
## Specify the “output” of our webpack config:

    module.exports = {
        output: {
        path: path.resolve(__dirname, "build")
        }
    };

## Loaders are what we will circle back around to in a second, but for now, add this to your webpack config.
    module: {
        rules: [
            {
            test: '/\.js$/',
            exclude: /node_modules/,
            loader: "babel-loader"
            }
        ]
    }

## Wepack Plugins
## Install the html webpack plugin

    npm i -D html-webpack-plugin

## Require the plugin at the top of your webpack config

    const HtmlWebPackPlugin = require('html-webpack-plugin')

## Add a plugins list to the webpack config and instantiate the plugin
        
    plugins: [
        new HtmlWebPackPlugin({
        template: "./src/client/views/index.html",
        filename: "./index.html",
        })
    ]
## Run webpack and observe the new dist folder output. Update your server file. Change the home route to use the index file from dist.
    
    app.get('/', function (req, res) {
    res.sendFile('dist/index.html')
    })

## Update your server file to look for asset files in the dist instead of client.
 
    from -> app.use(express.static('src/client'))
    to -> app.use(express.static('dist'))

## Mode Production vs. Development Environments

## Create a copy of the webpack.config.js, and rename it as webpack.prod.js. This file should have mode: 'production' statement in module.exports.

    mode: 'production'

## Now, rename the webpack.config.js to webpack.dev.js. This file should have the following statements in module.exports

    mode: 'development'

## Changes proposed for package.json. The statements added to the package.json for the configuration files of production and development modes separately in the "script" block are:

    "scripts": {
    "build-prod": "webpack --config webpack.prod.js",
    "build-dev": "webpack-dev-server  --config webpack.dev.js --open"
    },

## Note that you should remove the "build": "webpack" script now from package.json, and only have the two related to build-dev and build-prod. This also means when you build your app with npm, you should use the correct script, e.g.

    npm run build-dev

## Install Webpack Dev Server

    npm i -D clean-webpack-plugin

## Add a require statement to the top of the webpack config file:

    const { CleanWebpackPlugin } = require('clean-webpack-plugin');

## SASS
## Install all the tools we’ll need:

    npm i -D style-loader node-sass css-loader sass-loader

## Then add this test case to the rules array in your dev webpack config.

    {
        test: /\.scss$/,
        use: [ 'style-loader', 'css-loader', 'sass-loader' ]
    }

## Make sure to import all scss files into client/index.js

    import './styles/resets.scss'
    import './styles/base.scss'

## Load Images 

## Install file-loader and url-loader

    npm install url-loader file-loader --save-dev

## Configure url-loader in webpack.config, add below to modules.rules array in webpack.config:

    module:{
    rules:[
        {
            test:/\.(s*)css$/,
            use: ExtractTextPlugin.extract({ 
                fallback: 'style-loader',
                use: ['css-loader','sass-loader']
            })
        },
        {
            test: /\.(png|jp(e*)g|svg)$/,  
            use: [{
                loader: 'url-loader',
                options: { 
                    limit: 8000, // Convert images < 8kb to base64 strings
                    name: 'images/[hash]-[name].[ext]'
                } 
            }]
        }
    ]
    },

## Now, we will add couple of small-sized images for , lets say a Home Icon and a Settings Icon of our application. Click on the links to download the images. Both of these are less than the 8kb limit that we’ve specified for the url-loader.)

1. add a folder 'images' under our ‘src’ folder
2. download the two images(Home Icon, Settings Icon) and save those under under 'src/images'

## Next, we will modify ./src/index.html template file slightly to add an <img> tag as shown below:

./src/index.html

    <div style='display:inline-block;padding:10px;'>
        <img id="home" height='75px' width='75px' /> 
    </div>

## Now, for Webpack to see the home image as a dependency and then process it using url-loader, we need to import that image in our javascript.

./src/scripts/app.js

    import homeIcon from '../images/home.png';

    var homeImg = document.getElementById('home');
    homeImg.src = homeIcon;

## Run npm start command again to restart webpack with the new settings:

    npm start

## As we will start the Express server on a different port, we have to make familiar changes in the webpack.prod.js. Add the rule for sass files, like below:

    {
        test: /\.scss$/,
        use: [ 'style-loader', 'css-loader', 'sass-loader' ]
    }
    
## In webpack.prod.js as well as webpack.dev.js, add a new section in module.exports as:

    output: {
        libraryTarget: 'var',
        library: 'Client'
    },

## In client/index.js, add the export statement:

    export {
        checkForName,
        handleSubmit
    }

## In client/views/index.html, confirm that the custom handleSubmit() function references the newly created Client library, as:
    <section>
        <form class="" onsubmit="return Client.handleSubmit(event)">
            <input id="name" type="text" name="input" value="" onblur="onBlur()" placeholder="Name">
            <input type="submit" name="" value="submit" onclick="return Client.handleSubmit(event)" onsubmit="return handleSubmit(event)">
        </form>
    <section>
## In client/js directory, confirm that the javascript function calls refer to the Client library. In formHandler.js:Client.checkForName(formText) Change the port number in the fetch request in the formHandler.js to 8081:

    fetch('http://localhost:8081/test')
    .then(res => {
        return res.json()
    })
    .then(function(data) {
        document.getElementById('results').innerHTML = data.message
    })
    
## Change the port number in server/index.js to 8081 as well:
    // designates what port the app will listen to for incoming requests
    app.listen(8081, function () {
    console.log('Example app listening on port 8081!')
    })
## Open two terminal, one for the webpack dev server, and another for Express in production mode. In terminal 1, run the following commands:

# If you have switched to a new branch
    npm install
# Optional installation for development mode
    npm i -D @babel/core @babel/preset-env babel-loader
    npm i -D style-loader node-sass css-loader sass-loader
    npm i -D clean-webpack-plugin
    npm i -D html-webpack-plugin
# Build and start the webpack dev server at port 8080
    npm run build-dev
    In terminal 2, run the following commands:

# generate a `dist` folder for prod
    npm run build-prod
# Run the Express server on port 8081
    npm start 

## Service Workers 
## In webpack.prod.js config file,

## Require the plugin, by appending the new plugin statement

    const WorkboxPlugin = require('workbox-webpack-plugin');

## Instantiate the new plugin in the plugin list:

    new WorkboxPlugin.GenerateSW()

## On the terminal, install the plugin using npm install workbox-webpack-plugin --save-dev
## If you follow along with the Workbox Service Worker documentation, there’s one more step. We need to register a Service Worker with our app. To do this, we will add a script to our /src/client/views/index.html file and call the register service worker function if the browser supports service workers.

## Add this code to the bottom of your html file, just above the closing body tag.

    <script>
    if ('serviceWorker' in navigator) {
        // Use the window load event to keep the page load performant
        window.addEventListener('load', () => {
            navigator.serviceWorker.register('/service-worker.js');
        });
    }
    </script>