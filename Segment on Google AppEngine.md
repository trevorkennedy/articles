# Analytics for the Google AppEngine

![GAE](https://upload.wikimedia.org/wikipedia/en/3/38/Google_App_Engine_Logo.png)

## Introduction

The Google AppEngine allows a developer to quickly and easily run Python applications in the cloud. Flask is a simple, light weight Python micro-framework for developing web applications. The analytics library for Python will allow us to send usage data such as page views to Segment for later evaluation.

## Prerequesits
This guide assumes that you have already:

- Created a Python/Flask project in the [Google Developers Console](https://appengine.google.com)
- Installed the [Gooogle App Engine SDK](https://cloud.google.com/appengine/downloads)
- Configured a local directory for your project
	
## Install the requirements

You're 'requirements.txt' file should now look like the following:

~~~
Flask==0.10
analytics
python-dateutil
requests
six
~~~

-

Now run the following command in your terminal to install the analytics library and its dependancies into the 'lib' folder:

	pip install -r requirements.txt -t lib/

## Enable SSL support

Because all tracking requests made by analytics library are done over SSL, this support needs to be enabled as well. Open 'app.yaml' and add the flowing to the end of the 'libraries' section:

~~~yaml
- name: ssl 
  version: latest
~~~

-

If you intend to run your app locally using the Google AppEngine Launcher, add the below code to the end of the 'appengine_config.py' file. This procedure simply white lists the _ssl and _socket modules when running in the development environment.

```python
import os 
if os.environ.get('SERVER_SOFTWARE', '').startswith('Development'): 
	import imp 
	import os.path 
	from google.appengine.tools.devappserver2.python import sandbox 
	sandbox._WHITE_LIST_C_MODULES += ['_ssl', '_socket'] 
	psocket = os.path.join(os.path.dirname(os.__file__), 'socket.py') 
	imp.load_source('socket', psocket)
```

## Create the Flask app

Now that all of the prerequisites are out of the way, its time to actually implement the analytics library in Flask. Update 'main.py' to look like so:

```python
from flask import Flask, request
app = Flask(__name__)
app.config['DEBUG'] = True

import analytics
analytics.write_key = 'YOUR-KEY-HERE'

@app.route('/')
def hello():
	event = 'Hello World'
	analytics.track('0', event)
	return event
```
-

This first segment of code is all that is needed to setup Flask:

```python
from flask import Flask, request
app = Flask(__name__)
app.config['DEBUG'] = True
```

The follow configures the Analytics library for first use:

```python
import analytics
analytics.write_key = 'YOUR-KEY-HERE'
```

And the last segment of code defines the index page and records the event:

```python
@app.route('/')
def hello():
	event = 'Hello World'
	analytics.track('0', event)
	return event
```

Note that the above tracks the 'Hello World' event for user id '0'.

## Conclusion

That's it! You can now run your Flask app locally using the AppEngine Launcher or deploy it to Google AppEngine to run it in the cloud. Now log into your Dashboard at [segment.com](//segment.com) to see your page views are now being logged.