### Node ###

```js
var fs = require('fs');
var file = process.argv[2];
fs.readfile(file, function(err, contents) {
    if(err) {
		return console.log(err);
    }
    console.log(contents.toString().split('\n').length - 1);
})
```

```js
var fs = require('fs');
var path = require('path');

var folder = process.argv[2]
var ext = '.' + process.argv[3]

fs.readdir(folder, function (err, files) {
    if (err) return console.log(err);
    files.forEach (function(file) {
        if(path.extname(file) == ext) {
            console.log(file);
        }
    })
})
```

+ callback & module

+ ```js
  // program.js
  var filterFn = require('./solution_filter.js')
  var dir = process.argv[2]
  var filterStr = process.argv[3]

  filterFn(dir, filterStr, function (err, list) {
      if (err) {
          return console.error('There was an error:', err)
      }
      
      list.forEach(function (file) {
          console.log(file);
      })
  })
  ```

+ ```js
  // solution_filter.js
  var fs = require('fs')
  var path = require('path')

  module.export = function(dir, filterStr, callback) {
      fs.readdir(dir, function (err, list) {
          if(err) {
              return callback(err)
          }
          
          list = list.filter(file => path.extname(file) === '.' + filterStr);
          
          callback(null, list);
      })
  }
  ```

+ Http Client

+ ```js
  var http = require('http')

  http.get(process.argv[2], function (response) {
  	response.setEncoding('utf8');
      response.on('data', console.log)
      response.on('error', console.error)
  }).on('error', console.error)
  ```

+ HTTP collect

+ ```js
  var http = require('http')
  var bl = require('bl')

  http.get(process.argv[2], function (response) {
      response.pipe(bl(function (err, data){
  		if (err) {
          return console.error(err)
      	}
      	data = data.toString()
      	console.log(data.length)
      	console.log(data)
      })) 
  })
  ```

+ Judging async 

+ ```js
  var http = require('http')
  var bl = require('bl')
  var results = []
  var count = 0

  function printResults () {
      for (var i = 0; i < 3; i++) {
          console.log(results[i])
      }
  }

  function httpGet (index) {
      http.get(process.argv[2 + index], function (response) {
          response.pipe(bl(function (err, data) {
              if (err) {
                  return console.error(err)
              }
              
              results[index] = data.toString()
              count++
              
              if (count == 3) {
                  printResults()
              }
          }))
      })
  }

  for (var i = 0; i < 3; i++) {
  	httpGet(i)
  }
  ```

+ TCP time server

+ ```js
  var net = require('net')

  function zeroFill (i) {
      return (i < 10 ? '0' : '') + i
  }

  function now () {
  	var d = new Date()
      return d.getFullYear() + '-'
      + zeroFill(d.getMonth() + 1) + '-'
      + zeroFill(d.getDate()) + ' '
      + zeroFill(d.getHours()) + ':' 
      + zeroFill(d.getMinutes())
  }

  var server = net.createServer (function (socket) {
      socket.end (now() + '\n')
  })

  server.listen(Number(process.argv[2]))
  ```

+ HTTP file server

+ ```js
  var http = require('http')
  var fs = require('fs')

  var server = http.createServer(function (req, res) {
      res.writeHead(200, {
          'content-type': 'text/plain'
      })
      
      fs.createReadStream(process.argv[3]).pipe(res)
  })

  server.listen(Number(process.argv[2]))
  ```

+ HTTP uppercaserer

+ ```js
  var http = require('http')
  var map = require('through2-map')

  var server = http.createServer(function (req, res) {
      if (req.method != 'POST')
          return res.end('send me a POST\n')
      
      req.pipe(map(function (chunk) {
          return chunk.toString().toUpperCase()
      })).pipe(res)
  })

  server.listen(Number(process.argv[2]))
  ```

+ HTTP JSON API server

+ ```js
  var http = require('http')
  var url = require('url')

  function parsetime(time) {
      return {
          hour: time.getHours(),
          minute: time.getMinutes(),
          second: time.getSeconds()
      }
  }

  function unixtime(time) {
      return {
          unixtime: time.getTime()
      }
  }

  var server = http.createServer(function (req, res) {
      var parseUrl = url.parse(req.url, true)
      var time = new Date(parseUrl.query.iso)
      var result;
      
      if (/^\/api\/parsetime/.test(req.url)) {
          result = parsetime(time)
      }
      else if (/^\/api\/unixtime/.test(req.url)) {
          result = unixtime(time)       
      }
      
      if (result) {
          res.writeHead(200, {
              'Content-Type': 'application/json'
          })
          res.end(JSON.stringify(result))
      } else {
          res.writeHead(404)
          res.end()
      }
  })

  server.listen(process.argv[2])
  ```

  ​

