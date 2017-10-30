HoneyPy
=======

A low interaction honeypot with the capability to be more of a medium interaction honeypot. HoneyPy is written in Python and is intended to be easy to: deploy, extend funtionality with plugins, and apply custom configurations. The level of interaction is determined by the functionality of a plugin. Plugins can be created to emulate UDP or TCP based services to provide more interaction. All activity is logged to a file by default, but posting honeypot activity to Twitter or a web service endpoint can be configured as well. Examples:  

Live HoneyPy data posted to 
- Twitter: https://twitter.com/HoneyPyLog
- Web service endpoint and displayed via the HoneyDB web site: https://riskdiscovery.com/honeydb

note: HoneyPy has primarily been tested and run on Debian with Python 2.7.9.

**Pull requests are welcome!** If you would like to create new plugins or improve existing plugins, please do. 

### Documentation

The main documentation can be found at the [HoneyPy Docs](https://honeypy.readthedocs.io/en/latest/) site.

The section below will be moved to the HoneyPy Docs site soon.

#### Creating Custom Service Emulators
Hopefully creating new plugins is easy. HoneyPy takes care of sending/receiving data and logging, all you have to do is write the custom protocol/logic. The service emulators in the plugins directory can be used as templates to create new emulators.

Example:
https://github.com/foospidy/HoneyPy/blob/master/plugins/HashCountRandom/HashCountRandom.py

There are three sections to edit: custom import, custom protocol, and custom functions. To keep the template well organized, you should only make modifciaitons in the designated sections, note the comments that denote each section.

Example of custom import. Import the Python modules you need:
```
### START CUSTOM IMPORT
import time
import os
import md5
### END CUSTOM IMPORT
```

Next, use the custom protocol section to write your logic. use the `self.tx()` function to transfer (send) data to the socket, and use the `self.rx()` function to receive data from the socket.

```
### START CUSTOM PROTOCOL ###########################################################
self.tx('ACCEPT_CONN: ' + str(self.remote_host) + ':' + str(self.remote_port) + '\n')
count = 0

while True:
	count = count + 1
	self.tx(self.md5sum(count) + ':' + str(os.urandom(99)) + '\n')
	self.rx()
	time.sleep(1)

### END CUSTOM PROTOCOL #############################################################
```

Add custom functions as needed at the bottom. All functions must have the first parameter be `self`. When you call custom functions from the custom protocol section you must prefix them with `self.`, for example: `self.md5sum('test')`

```
### START CUSTOM FUNCTIONS ##########################################################
def md5sum(self, data):
	m = md5.new()
	m.update(str(data))
	return m.hexdigest()

### END CUSTOM FUNCTIONS ############################################################
```
