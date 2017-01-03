# Django Utilities include functions and classes

## Shortcuts

### `django.shortcuts.render(request, template_path, params_dict)`

Render an template using request and defined dictionary as a parameters source

### `django.shortcuts.get_object_or_404(model, query_keyword_params)`

Get an object using query or raise an 404 if object not found
