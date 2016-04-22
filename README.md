# bluestatsd

[![Build Status](https://secure.travis-ci.org/catalint/bluestatsd.png)](http://travis-ci.org/catalint/bluestatsd)
[![Coverage Status](https://coveralls.io/repos/catalint/bluestatsd/badge.png)](https://coveralls.io/r/catalint/bluestatsd)
[![Code Climate](https://codeclimate.com/github/catalint/bluestatsd.png)](https://codeclimate.com/github/catalint/bluestatsd)
[![NPM version](https://badge.fury.io/js/bluestatsd.png)](http://badge.fury.io/js/bluestatsd)
[![Dependency Status](https://david-dm.org/catalint/bluestatsd.png)](https://david-dm.org/catalint/bluestatsd)

[![NPM](https://nodei.co/npm/bluestatsd.png?downloads=true&stars=true)](https://nodei.co/npm/bluestatsd/)

A hapi plugin for sending request round trip metrics to statsd, also exposing statsd client to the server.

## Contributing

This module makes use of a `Makefile` for building/testing purposes. After obtaining a copy of the repo, run the following commands to make sure everything is in working condition before you start your work:

	make install
	make test

Before committing a change to your fork/branch, run the following commands to make sure nothing is broken:

	make test
	make test-cov

Don't forget to bump the version in the `package.json` using the [semver](http://semver.org/spec/v2.0.0.html) spec as a guide for which part to bump. Submit a pull request when your work is complete.

***Notes:***
* Please do your best to ensure the code coverage does not drop. If new unit tests are required to maintain the same level of coverage, please include those in your pull request.
* Please follow the same coding/formatting practices that have been established in the module.

## Installation

	npm install bluestatsd

## Usage

To install this plugin on your Hapi server, do something similar to this:

```js
var Hapi = require('hapi');
var server = new Hapi.Server();

var hapiStatsdConfig = {};

server.register({ register: require('bluestatsd'), options: hapiStatsdConfig }, function(err) {
	if (err) {
		console.log('error', 'Failed loading plugin: bluestatsd');
	}
});
```

## Plugin Options

### `template`

A template to use for the stat names to send to statsd. This can be any string that could include the following tokens that get replaced with their actual values:

* `{path}` - the path that the request was routed to (e.g `'/users/{id}'`)
* `{method}` - the HTTP verb used on the request (e.g. `'GET'`)
* `{statusCode}` - the numerical status code of the response that the server sent back to the client (e.g. `200`)

Defaults to `'{path}.{method}.{statusCode}'`

### `statsdClient`

An instance of a particular statsd client that you prefer to use for sending metrics to statsd. If this is used, then the `statsdHost` and `prefix` options are ignored. Defaults to `null`

### `host`

The host athat an instance of statsd is running on. An instance of the `statsd-client` NPM module will be created and will be configured to use this host. Defaults to `'localhost'`

### `port`

The port that an instance of statsd is listening on. An instance of the `statsd-client` NPM module will be created and will be configured to use this port. Defaults to `8125`

### `prefix`

The prefix to add to every stat collected. Usually used for grouping a set of stats under one hierarchy in graphite. Defaults to `'hapi'`

### `pathSeparator`

A character or set of characters to replace the '/' (forward slash) characters in your URL path since forward slashes cannot be used in stat names. Defaults to `'_'`

### `dotReplacer`
A character or set of characters to replace the '.' (dot) characters in your URL path since dots have other meanings. Defaults to `'-'`


## Example

A Hapi route configured like this:

```js
server.route({
	method: 'GET',
	path: '/test/{param}',
	handler: function(request, reply) {
		reply('Success!');
	}
});
```

would send an increment and timing stat to statsd with the following stat name (assuming all options are set to their defaults):

	hapi.test_{param}.GET.200

As the [statsd client](https://github.com/msiebuhr/node-statsd-client) is also exposed to the hapi server, you can use any of its methods, e.g.:

```js
server.statsd.increment('systemname.subsystem.value');
server.statsd.gauge('what.you.gauge', 100);
server.statsd.set('your.set', 200);
```

## Version Compatibility

### Currently compatible with: Hapi 13.x.x (Node v4)

* 5.x.x - Hapi 11.x.x
* 5.x.x - Hapi 12.x.x
* 5.x.x - Hapi 13.x.x

# License

The MIT License (MIT)

Copyright (c) 2013 Mac Angell
Copyright (c) 2016 Catalin Tanasescu

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

