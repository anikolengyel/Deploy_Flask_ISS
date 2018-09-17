# Deploy_Flask_IIS
## How to deploy Flask App on IIS

## Contents

### Introduction
### Other Methods – why they didn’t work
### My Solution
1. Installations
2. Sample Program
3. Config Files
4. Install and configure IIS
5. Errors


### Introduction
### Other Methods – why they didn’t work
### My Solution
**1. Installations**
I installed Python 3.x and ticked the option "Add Python to PATH".

I started cmd and typed `python` to test if Python is added to the path. Use `exit()` to exit.

```pip install wfastcgi```

After that, I used the following commands to install flask and wfastcgi:

```pip install wfastcgi```

```pip install flask```

In powershell, I enabled Wfast CGI:

```wfastcgi enable```

**2. Sample Program**

I created a sample Flask program with HTML templates and static CSS.

Python program:

```
from flask import Flask, render_template


app = Flask(__name__)


@app.route('/hello')
def say_hello():
    return render_template('hello_template.html')


if __name__ == '__main__':
    app.debug = True
    app.run(host='0.0.0.0', port=8080)
```  

HTML template:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Hello</title>
    <link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
    <h1 class="greeting">Hello World!</h1>
</body>
</html>
```  

CSS file:

```  
.greeting {
    text-align: center;
    padding-top: 4em;
    color: purple;
}
```  


**3. Config Files**

I created a `web.config` in the folder of myapp.py with the following content. For scriptProcessor use the path to the python.exe and
the wfastcgi.py. To make this easies, I simply used the output of wfastcgi.enable command. The "WSGI_HANDLER" should be myapp.app (filename.Flaskappname), and the "PYTHONPATH" should pooint to the directory of the Flask program.

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
 <system.webServer>
   <handlers>
    <remove name="Python34_via_FastCGI" />
    <add name="Python FastCGI"
       path="*"
       verb="*"
       modules="FastCgiModule"
       scriptProcessor="C:\Python3\python.exe|c:\python3\lib\site-packages\wfastcgi.py"

       resourceType="Unspecified"
       requireAccess="Script" />
   </handlers>
 </system.webServer>
 <appSettings>
   <!-- Required settings -->
   <add key="WSGI_HANDLER" value="myapp.app" />
   <add key="PYTHONPATH" value="C:\flask_deploy" />
 </appSettings>
</configuration>
```

Create a `myapp.wsgi` in the same folder with the following code: 

```
from deploy_test import app as application

WSGIRestrictStdout Off
```

For the static files, create a `web.config` file in the static folder:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
 <system.webServer>
   <handlers>
    <clear/>
    <add name="StaticFile"
       path="*"
       verb="*"
       modules="StaticFileModule"
       resourceType="File"
       requireAccess="Read" />
   </handlers>
 </system.webServer>
</configuration>
```

**4. Install and configure IIS**

I opened Turn Windowfeatures On and Off on desktop and searched for Internet Information Services. 

[Screenshot](Path)

In World Wide Web Services, I went into Application Development and made sure that the option CGI is ticked. 

[Screenshot](Path)

I hit OK and installed the changes.

Open IIS Desktop application then go to the Sites option.

[Screenshot](Path)

On the right menubar, I clicked on "Add Website". I filled the form according to the following example. I gave an application name of my choice, the physical path is the path of the application folder. The IP address is Unassigned. I also chose a port.

[Screenshot](Path)

After I saved the configurations, the website started immediately on port 5555. 

**4. Some Errors**


