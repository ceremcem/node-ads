node-ads [![NPM Version](https://img.shields.io/npm/v/node-ads.svg)](https://www.npmjs.com/package/node-ads) ![node](https://img.shields.io/node/v/node-ads.svg) [![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)
======


> A NodeJS implementation for the Twincat ADS protocol.
> (Twincat and ADS is from Beckhoff &copy;. I'm not affiliated.)

### Changelog

- `debug` module.
- Added aliases: `TOD -> TIME_OF_DAY` and `DT -> DATE_AND_TIME`.
- Code standardized.
- Using `safe-buffer`.
- When we use notification, the notification will blocked to fire if too many notifications are defined
- `multiRead` method and read bug fix (timeout).


Examples
--------

### Hello machine


```javascript
var ads = require('node-ads')

var options = {
    //The IP or hostname of the target machine
    host: "10.0.0.2",
    //The NetId of the target machine
    amsNetIdTarget: "5.1.204.160.1.1",
    //The NetId of the source machine.
    //You can choose anything in the form of x.x.x.x.x.x,
    //but on the target machine this must be added as a route.
    amsNetIdSource: "192.168.137.50.1.1",

    //OPTIONAL: (These are set by default)
    //The tcp destination port
    //port: 48898
    //The ams source port
    //amsPortSource: 32905
    //The ams target port
    //amsPortTarget: 801
}

var client = ads.connect(options, function() {
    this.readDeviceInfo(function(err, result) {
        if (err) console.log(err)
        console.log(result)
        this.end()
    })
})

client.on('error', function(error) {
    console.log(error)
})
```

### Read something

```javascript
var myHandle = {
    //Handle name in twincat
    symname: '.TESTINT',  
    //An ads type object or an array of type objects.
    //You can also specify a number or an array of numbers,
    //the result will then be a buffer object.
    //If not defined, the default will be BOOL.
    bytelength: ads.INT,  
    //The propery name where the value should be written.
    //This can be an array with the same length as the array length of byteLength.
    //If not defined, the default will be 'value'.     
    propname: 'value'      
}

var client = ads.connect(options, function() {
    this.read(myHandle, function(err, handle) {
        if (err) console.log(err)
        //result is the myHandle object with the new properties filled in
        console.log(handle.value)
        //All handles will be released automaticly here
        this.end()
    })
})
```

### Write something

```javascript
var client = ads.connect(options, function() {
    myHandle.value = 5
    this.write(myHandle, function(err) {
        if (err) console.log(err)
        this.read(myHandle, function(err, handle) {
            if (err) console.log(err)
            console.log(handle.value)
            this.end()
        })
    })
})
```

### Get notifications

```javascript
var myHandle = {
    symname: '.CounterTest',       
    bytelength: ads.WORD,  

    //OPTIONAL: (These are set by default)       
    //transmissionMode: ads.NOTIFY.ONCHANGE, (other option is ads.NOTIFY.CYLCIC)
    //maxDelay: 0,  -> Latest time (in ms) after which the event has finished
    //cycleTime: 10 -> Time (in ms) after which the PLC server checks whether the variable has changed
}

var client = ads.connect(options, function() {
    this.notify(myHandle)
})

client.on('notification', function(handle){
    console.log(handle.value)
})

process.on('exit', function () {
    console.log("exit")
})

process.on('SIGINT', function() {
    client.end(function() {
        process.exit()
    })
})
```

### Get symbol list

```javascript
client = ads.connect(options, function() {
    this.getSymbols(function(err, symbols) {
        if (err) console.log(err)
        console.log(symbols)
        this.end()
    })
})
```

License (MIT)
-------------
Copyright (c) 2012 Inando

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
