---
layout: page
title: Python
permalink: /diary/python
---

### Using weaitress WSGI to launch a flask app:
```cmd
waitress-serve --port=8000 --host=0.0.0.0 --call 'mymodule:create_app'
```
### Using List comprehensions to unpack a curser to an array of dicts for each row
```python
results_proxy = cursor.execute('some sql', ['some parfams'])
result = [dict(zip([key[0] for key in cursor.description], row)) for row in results_proxy]
```

