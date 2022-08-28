# ntp-time

This is a version of the [`ntp-time`](https://www.npmjs.com/package/ntp-time) package that can be used in the web browser. It uses _WebSockets_ instead of _UDP_ as communication protocol (because browsers don't speak _UDP_) and has some libraries replaced by browserified versions.

**Note:** everything below this line is not updated yet and still describes the original package!

## Installation

With [npm](https://npmjs.com):

    npm install ntp-time

With [yarn](https://yarnpkg.com):

    yarn add ntp-time

# Methods and usage

## Client

To instantiate an NTP Client you just have to require the client class from the module and then instantiate it inside your code. To get the time you must use the `syncTime` method.

### client.js

```javascript
const NTP = require('ntp-time').Client;
const client = new NTP('a.st1.ntp.br', 123, { timeout: 5000 });

async function sync() {
	try {
		await client.syncTime();
	} catch (err) {
		console.log(err);
	}
}

sync();

// Or using .then

client
	.syncTime()
	.then(time => console.log(time)) // time is the whole NTP packet
	.catch(console.log);
```

## Server

To put a server up, you must require the server class from the `ntp-time` module, pass a request callback to it and use the listen method, with a port and an callback as an argument. Inside the request callback you can manipulate the message the way you want.

### server.js

```javascript
const NTPServer = require('ntp-time').Server;
const server = new NTPServer();

// Define your custom handler for requests
server.handle((message, response) => {
	console.log('Server message:', message);

	message.transmitTimestamp = Math.floor(Date.now() / 1000);

	response(message);
});

// Check if node has the necessary permissions
// to listen on ports less than 1024
// https://stackoverflow.com/questions/413807/is-there-a-way-for-non-root-processes-to-bind-to-privileged-ports-on-linux
server.listen(123, err => {
	if (err) throw err;

	console.log('Server listening');
});
```

For more didatic code, go to the examples page.
