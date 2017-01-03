# Django RESTful framework for building APIs


## Installation and Configuration

### Installation

`pip install djangorestframework`

```python
// settings.py
//...
INSTALLED_APPS = (
	#...
	'rest_framework',
)
//...
REST_FRAMEWORK = (
	'DEFAULT_PERMISSION_CLASSES': [
			'rest_framework.permissions.DjangoModelPermissionsOrAnonReadOnly'
		]
)
```

### Useful configurations

- `DEFAULT_RENDERER_CLASSES`: change default renderer classes


## Defining serializer

### Useful classes

- `Serializer`: Provides serialization for normal Python class instances
	* For data after serialization using `serialized_object.data`

- `ModelSerializer`: Provides serialization form model instances

- `HyperlinkedModelSerialization`: The same with `ModelSerializer` but represents object relationships with links rather than primary keys

### How to use

1. First we need to serialization all models for output JSON

```python
from rest_framework.models import serializers
class ProductSerializer(serializers.ModelSerializer):
	class Meta:
		model = Product
		fields = ('id', 'name', 'slug', 'unit', )
```

2. Then we can use this in the views

```python
from api.serializers import ProductSerializer
def get_product(request, id):
	...
	serializedProduct = ProductSerializer(product)
	return JSONResponse(serailizedProduct.data)
```

## Parsers and Renderers

### Useful classes

- `JSONParser`: convert JSON input (bytes) to Python object
- `JSONRenderer`: render serialized object to output JSON bytes

### How to use
