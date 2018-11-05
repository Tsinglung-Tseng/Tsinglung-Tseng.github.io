---
title: python tricks
key: 20181029
tags: Learning
---

### Python rest-flask API

request  data  header

```python
import requests

url = "http://localhost:23300/api/v1/tasks"
headers = {
    'Content-Type': "application/json"
    }

response = requests.post(url, data=tk.to_json(), headers=headers)

print(response.text)
```

### Story of import

```python

__import__('pkg_resources').declare_namespace(__name__)

"""
__import__ is a Python function that will import a package using a string as the name of the package. 
It returns a new object that represents the imported package. 

So foo = __import__('bar') will import a package named bar and store a reference to its objects in a local object 
variable foo.
"""

"""
From setup utils 'pkg_resources' documentation, declare_namespace() "Declare[s] that the dotted package name __name__
is a "namespace package" whose contained packages and modules may be spread across multiple distributions."
"""

```
