
task 'bundle', 'Generate the browser bundle for ax.js', (options)->
  fs = require 'fs'
  path = require 'path'
  browserify = require 'browserify'
  _module = path.join __dirname, 'lib', 'ax.js'
  bundle = path.join __dirname, 'lib', 'ax', 'browser', 'ax.js'
  example = path.join __dirname, 'examples', 'javascripts', 'lib', 'ax.js'

  src = browserify({ filter : require('uglify-js') })
    .require(_module)
    .bundle()
  ;

  buffer = new Buffer [
    'var Ax = (function () {'
    src
    '; return require("./ax")'
    '})()'
  ].join '';

  fs.writeFile bundle, buffer, (err) ->
    throw err if err;

    console.log bundle + ' written (' + buffer.length + ' bytes)'

  fs.writeFile example, buffer, (err) ->
    throw err if err;

    console.log example + ' written (' + buffer.length + ' bytes)'

task 'watch', 'Watch lib/ax.js for auto-bundling', (options)->
  invoke 'bundle'

  fs = require 'fs'
  path = require 'path'
  _module = path.join __dirname, 'lib', 'ax.js'

  fs.watchFile _module, (current, previous) ->
    if current.mtime > previous.mtime
      invoke 'bundle'


task 'docs', 'generate the inline documentation', ->
  {spawn, exec} = require 'child_process'

  command = [
    'rm -r docs'
    'node_modules/docco/bin/docco lib/*.js'
  ].join(' && ')

  exec(command, (err) -> throw err if err)

option '-p', '--port [PORT]', 'port to run the server on'
task 'test:server', 'launch a server for the browser tests', (o)->
  browserify = require 'browserify'
  path = require 'path'
  express = require 'express'
  app = express.createServer()
  public = path.join __dirname, 'test', 'browser'
  ax = path.join __dirname, 'lib', 'ax'


  app.configure ->
    app.use express.logger 'dev'
    app.use express.static public

  app.get '/bundle.js', (req, res)->
    res.header 'Content-Type', 'text/javascript'

    src = browserify({
      require: {
        'ax': './lib/ax'
      }
    }).bundle()

    res.end(src);

  app.listen o.port || 8080

task 'examples:server', 'launch a server for the examples', (options)->
  path = require 'path'
  express = require 'express'
  app = express.createServer()
  public = path.join __dirname, 'examples'

  app.configure ->
    app.use express.logger 'dev'
    app.use express.static public

  app.listen options.port || 8080
