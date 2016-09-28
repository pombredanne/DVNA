## HTTP Parameter Pollution (HPP)
HTTP Parameter Pollution pollutes the HTTP parameters of a web application in order to perform or achieve a specific malicious task/attack different from the intended behavior of the web application. HPP injects encoded query string delimiters in existing or other HTTP parameters (i.e. GET/POST/Cookie), which make it feasible to supersede parameter values that already exist to inject a new parameter or exploit variables from direct access. This attack affects all web technologies, whether running client-side or server-side. Generally, an attacker can use HPP vulnerabilities to:

- Prioritize injected parameters
- Alter or modify the intended application behavior
- Access and potentially exploit variables that are not handled properly
- Bypass WAFs rules or input validation mechanisms

Some web technologies parse the first or the last occurrence of the parameter, some concatenate all the inputs and others will create an array of parameters. Below is a list showing how each web technology might parse different values of the same parameters at the server-side.

- All occurrences of the specific parameter eg. p1=v1,v2
- Last occurrence eg. p1=v1
- First occurrence eg. p1=v1
- Turns values provided into an array eg. ARRAY(0x8b9059c)

Client-side HPP can be used to inject additional parameters to the URL links or other src attributes. Stored HPP can manipulate all tags with data, src, or href attributes and POST action forms.

**Prevent HPP and stop possible Uncaught Exceptions**

- RFC3986 does not define a standard for handling multiple parameters. Most frameworks treat values with the same name as an array. Express treats a single name by returning a String, when multiple values with the same name are passed the type is changed to Array. If you don't account for this in query handling, an application will emit an UncaughtException even that can bring the application down resulting in a denial of service if not properly handled.

### Defenses
- Implement an extensive and proper input validation scheme. This is going to vary by language and stack.
- Pay attention to how the framework you are using handles parameter triggering
- An awareness that such attacks will be performed against your web application

### Vulnerable Code View
```
// Express expects the name key to be a String and uses .toUpperCase() on it
// the code assumes that req.query.name is a String but since there are two arguments 
// with the same name it returns the result as an Array:['node','node']. This will
// throw an Error and crash the application.

app.get('/endpoint', function(req, res){  
  if(req.query.name){
    res.status(200).send('Hi ' + req.query.name.toUpperCase())
  } else {
    res.status(200).send('Hi');
  }
});

```

### Patched Code
```
// ...
```

### Exploit Code
```
curl http://example.com:8080/endpoint?name=DVNA&name=DVNA
```