# Deploy_Flask_ISS
## How to deploy Flask application on ISS.

## Contents

### Deploying Flask Application
### Introduction
### Other Methods – why they didn’t work
### My Solution
1. Installations
2. Sample Program
3. Config Files
4. Errors


### Introduction
### Other Methods – why they didn’t work
### My Solution
**1. Installations**
- Install Python 3.x  on C:/
-	Add Python to path
-	Install pip
- pip installations: 

```pip install wfastcgi```

```pip install flask```

Other install to the program.

In powershell enable Wfast CGI:

```wfastcgi enable```

**2. Sample Program**

Create a sample Flask program with HTML templates and Static files.

**3. Config Files**

Create a `web.config` in the folder of myapp.py with the following content. For scriptProcessor use the path to the python.exe and
the wfastcgi.py. The "WSGI_HANDLER" should be myapp.app (filename.Flaskappname), and the "PYTHONPATH" should pooint to the directory of the Flask program.

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





