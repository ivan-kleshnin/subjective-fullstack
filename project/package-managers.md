# Package managers

You need only NPM and Webpack. No Bower, please.
Try `$ npm install font-awesome`, `$ npm install bootstrap`...
Then you `import "bootstrap/dist/css/bootstrap.css"` and Webpack takes the burden of minimizing / prefixing things
on itself as long as you enable appropriate plugins. You can also import minimized version and avoid parsing if you need to – just create two or more different (yet similar) loaders with [according rules](http://stackoverflow.com/questions/33873317/how-to-exclude-files-from-loader).

TODO provide `webpack.config.js` examples
