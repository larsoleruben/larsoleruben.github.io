---
layout: page
title: Python
permalink: /diary/python
---

### Using weaitress WSGI to launch a flask app:
```cmd
waitress-serve --port=8000 --host=0.0.0.0 --call 'mymodule:create_app'
```
