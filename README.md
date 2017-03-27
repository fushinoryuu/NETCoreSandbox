# NETCoreSandbox

### Set Up Webpack/Babel/React
1. You'll need Node/NPM installed on your machine for this to work
* Downloading and installing Node will also install NPM
* Get them [here](https://nodejs.org/en/download/)
2. Once you have Node and NPM set up locally (and added to the path), you'll need to setup a package.json file in your project
* Add this code to your package.json (this file is used to tell NPM/Node what modules to install
* This will add Webpack, Babel (ES2015/ES6 JavaScript interpreter/transpiler), React, and the modules the Webpack Dev Middleware will need
``` 
{
  "version": "1.0.0",
  "name": "asp.net",
  "private": true,
  "devDependencies": {
    "aspnet-webpack": "^1.0.28",
    "aspnet-webpack-react": "^1.0.5",
    "babel-core": "^6.24.0",
    "babel-loader": "^6.4.1",
    "babel-preset-es2015": "^6.24.0",
    "babel-preset-react": "^6.23.0",
    "react": "15.4.2",
    "react-dom": "^15.4.2",
    "webpack": "^2.2.1",
    "webpack-dev-server": "^2.4.2",
    "webpack-hot-middleware": "^2.17.1"
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "main": "webpack.config.js",
  "description": ""
} 
```
3. Next, you'll add the Nuget package for Webpack Dev Middleware ([found here](https://www.nuget.org/packages/Webpack/))
* You can also search for it in the build-in Nuget package manager Visual Studio integration
* Steps:
* Right click Dependencies under the project folder in the solution explorer
* Click Manage Nuget Packages
* Click the Browse tab
* Search for "Webpack"
* Click the Webpack item, and then click the Install button on the right-hand side
4. After the Webpack package is installed, you'll need to add this to you Startup.cs file, in the Configure method:
```
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions
{
    HotModuleReplacement = true,
    ReactHotModuleReplacement = true
});
```
* The complete Configure method should look something like this:
```
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    loggerFactory.AddConsole(Configuration.GetSection("Logging"));
    loggerFactory.AddDebug();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseBrowserLink();
        app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions
        {
            HotModuleReplacement = true,
            ReactHotModuleReplacement = true
        });
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```
* This middleware is what will facilitate hot module replacement (live updates to the browser as JavaScript/JSX is edited)
5. The last thing you will need to do, is to set up a webpack.config.js
* Create the webpack.config.js file, and then add this code to it:
```
var path = require('path');
var webpack = require('webpack');

module.exports = {
    entry: {
        main: './Scripts/main'
    },
    output: {
       publicPath: "/js/",
       path: path.join(__dirname, '/wwwroot/js/'),
       filename: 'main.build.js'
    },
    devtool: 'inline-source-map',
    module: {
        loaders: [
            {
                test: /.jsx?$/,
                loader: 'babel-loader',
                exclude: /node_modules/,
                query: {
                    presets: ['es2015', 'react']
                }
            }
        ]
    }
};
```
* The entry section defines the entry file; the file that Webpack will run first
* You should include your initial imports in this JavaScript or JSX file
* The output filename determines the name of the build file (this is the file you will reference in a script tag in an HTML page or your layout)
* The paths of these files are always relative to where the webpack.config.js is located (the config file should be root level of the project)
6. Finally, you should run the command "npm install" from the command line in the project root
* The "npm" command should have been added to your PATH variables on the Node install
* If "npm" is not recognized, you should troubleshoot your Node install
7. Once the config files are in place, and the Webpack Nuget package is installed, the Startup file modified, and "npm install" is run, you should see hot module replacement work on any submodule file changes
* If you check your JavaScript console, you should see "[HMR] connected" displayed on page load if everything is working correctly
* The console will also show info messages as modules/js files are modified and updated
