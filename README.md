# Joomla connector module for Node

This module assumes you have an existing Joomla installation and wish to connect to it. The constructor takes either a path to the root of the Joomla installation, or a JavaScript object literal with the configuration details. When the module connects to the database, it executes the callback function you pass in.

## Installation

Using `npm`:

	npm install joomla

You can also clone this repository into your `node_modules` directory.

## Examples

### Path style

	var joomla = require('joomla');

	joomla('/path/to/joomla/site');


### Object literal style

	var joomla = require('joomla');

	joomla({
		db: 'test_joomla_node',
		user: 'root',
		host: 'localhost',
		password: ''
	});

## Authorization & Queries example

Once connected to the site, you can authenticate against the Joomla database using the request cookies. Here's an example using Connect, assuming a pre-connected `joomla` object:
```
var joomla = require('joomla');
joomla('/var/www/joomla');

var express = require('express')
var cookieParser = require('cookie-parser')

var app = express()
app.use(cookieParser())

app.get('/', function (req, res) {
  // Cookies that have not been signed
  console.log('Cookies: ', req.cookies)

  // Cookies that have been signed
  console.log('Signed Cookies: ', req.signedCookies)

  joomla.auth_cookies(req.cookies, function  (result) {
    res.writeHead(200, {'Content-Type': 'text/plain'});

    if (result !== undefined) {
      res.write(result.username + ' ' + result.userid);
 
 //You can also use the db.query function to make queries against the database: 
  joomla.db.query('SELECT * FROM #__users', function  (results) {
                //res.writeHead(200, {'Content-Type': 'text/plain'});
                for (var i=0; i < results.length; i++) {
                var row = results[i];
                res.write(row.username + "\n");
                }
        });

  res.end();
    } else {
      res.end('no session');
    }
  });
})

app.listen(8082)
```
The `#__` prefix is automatically converted to the prefix set in your Joomla configuration.
