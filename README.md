[![Build Status](https://travis-ci.org/JodusNodus/node-syncthing.svg?branch=master)](https://travis-ci.org/JodusNodus/node-syncthing)
[![npm version](https://badge.fury.io/js/node-syncthing.svg)](https://badge.fury.io/js/node-syncthing)

## Install:
`npm i node-syncthing --save`

## Usage:
```js
const syncthing = require('node-syncthing')
//Create an instance
const st = syncthing(options)
```

Options: _object_

* host: `string` _(default: `127.0.0.1`)_
* port: `number` _(default: `8384`)_
* apiKey: `string` _(not necessary if username and password are provided)_
* https: `boolean` _(defaults: true)_
* username: `string`
* password: `string`
* eventListener: `boolean` _(default: `false`)_
* retries: `number` _(default: `0`, how many times to try after connection loss for event listener)_
* events: `string` _([event types](https://docs.syncthing.net/dev/events.html#event-types) to [filter](https://docs.syncthing.net/rest/events-get.html), leave empty to catch the default event types (default))_

### Methods
Using callbacks:

`syncthing.endpoint.method(options, callback);`

Using promises:

`syncthing.endpoint.method(options).then(responseHandler).catch(errorHandler);`

Information about options: [Syncthing API](http://docs.syncthing.net/dev/rest.html)

Endpoints: _endpoint/method (options)_
* System _(system)_
  - ping
  - shutdown
  - restart
  - version
  - status
  - connections
  - getConfig
  - setConfig _(config)_
  - configInSync
  - debug
  - getDiscovery
  - setDiscovery _(device, address)_
  - errors
  - clearErrors
  - logs
  - getUpgrade
  - setUpgrade
  - pause _(device)_
  - resume _(device)_
* Database _(db)_
  - scan _(folder, [subdir])_
  - status _(folder)_
  - browse _(folder, levels, [subdir])_
  - completion _(device, folder)_
  - file _(folder, file)_
  - getIgnores _(folder)_
  - setIgnores _(folder, [ignores])_
  - need _(folder)_
  - prio _(folder, file)_
  - override _(folder)_
* Statistics _(stats)_
  - devices
  - folders
* Misc _(misc)_
  - folders _(device)_
  - lang
  - report

Data and errors can be handled with callbacks or with promises:
* Promises _(Provide no callback function)_
* Callback _(Provide a callback function as the last argument in the method)_
  - Error argument _(returns the error or null if there was none)_
  - Data argument _(api response)_

### Events
* error _(Emmitted on event listener error)_
* [configSaved](http://docs.syncthing.net/events/configsaved.html)
* [deviceConnected](http://docs.syncthing.net/events/deviceconnected.html)
* [deviceDisconnected](http://docs.syncthing.net/events/devicedisconnected.html)
* [deviceDiscovered](http://docs.syncthing.net/events/devicediscovered.html)
* [deviceRejected](http://docs.syncthing.net/events/devicerejected.html)
* [downloadProgress](http://docs.syncthing.net/events/downloadprogress.html)
* [folderCompletion](http://docs.syncthing.net/events/foldercompletion.html)
* [folderErrors](http://docs.syncthing.net/events/foldererrors.html)
* [folderRejected](http://docs.syncthing.net/events/folderrejected.html)
* [folderSummary](http://docs.syncthing.net/events/foldersummary.html)
* [itemFinished](http://docs.syncthing.net/events/itemfinished.html)
* [itemStarted](http://docs.syncthing.net/events/itemstarted.html)
* [localIndexUpdated](http://docs.syncthing.net/events/localindexupdated.html)
* [ping](http://docs.syncthing.net/events/ping.html)
* [remoteIndexUpdated](http://docs.syncthing.net/events/remoteindexupdated.html)
* [starting](http://docs.syncthing.net/events/starting.html)
* [startupComplete](http://docs.syncthing.net/events/startupcomplete.html)
* [stateChanged](http://docs.syncthing.net/events/statechanged.html)
* [More event types in the docs](https://docs.syncthing.net/dev/events.html#event-types)

## Example:
```js
const syncthing = require('node-syncthing')

//Options
const options = {
  host: 'localhost',
  port: 8384,
  apiKey: 'Tj2pqyhhkNFs43r5vQsMgLtwvT94Ah4F',
  eventListener: true,
  retries: 10,
}

const st = syncthing(options)

//With Callback
st.system.ping((err, res) => {
  if (!err) {
    console.log(res.ping) //pong
  }else {
    console.error(err)
  }
})

//With Promises
st.system.ping().then((res) => {
  console.log(res.ping) //pong
}).catch((err) => {
  console.error(err)
})

//Listen to events
st.on('ping', () => {
  console.log('pong')
})

st.on('error', (err) => {
  console.error(err)
})

//Removing event listeners will stop the event polling
st.removeAllListeners('ping')
st.removeAllListeners('error')
```
## Dev
### Build
```
npm i
npm run build
```

### Test
```
cd test
syncthing -home . -no-browser
```

In new terminal window:

`npm test`
