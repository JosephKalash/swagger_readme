
## Add Swagger for a Django project with drf-spectacular


### Installatioin
```
 pip install drf-spectacular
```
Then add drf-spectacular to installed apps in **settings.py**

```python
INSTALLED_APPS = [
    ...
    'drf_spectacular',
]
```

Register our spectacular AutoSchema with DRF.


```python
REST_FRAMEWORK = {
    ...
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}
```
drf-spectacular has settings options. (not required for more settings check [settings](https://drf-spectacular.readthedocs.io/en/latest/settings.html))

Recomended example:
```python
SPECTACULAR_SETTINGS = {
    'TITLE': 'Zcoderz {project title}',
    'DESCRIPTION': 'List of all api schemas for {project name}',
    'VERSION': '1.0.0',
    'SERVE_INCLUDE_SCHEMA': False,
    'SCHEMA_PATH_PREFIX': '/api/v[0-9]',
    "SWAGGER_UI_SETTINGS": {
        "deepLinking": True,
        "persistAuthorization": True,
        # "displayOperationId": True,
        "filter": True,
    }
}
```
Add Swagger-ui url in **urls.py**
```python
from drf_spectacular.views import SpectacularAPIView, SpectacularRedocView, SpectacularSwaggerView
urlpatterns = [
    ...
    path('api/schema/', SpectacularAPIView.as_view(), name='schema'),
    
    path('swagger', SpectacularSwaggerView.as_view(url_name='schema'), name='swagger-ui'),
 ]
```

Now **drf-spectacular** will automatically detect api and their properities and show them in `swagger`.


## Customization
To override detected endpoint options or implement more custome documentation we can use `@extend_schema_view decorator` for `view` and `@extend_schema` for `method`

### Example:

```python
@extend_schema_view(
    get=extend_schema(
        description='Hello world',
        parameters=[
            UserSerializer
        ]
    ),
    post=extend_schema(
        parameters=[
            OpenApiParameter(name='email', description='Email', type=str),
            OpenApiParameter(name='lang', description='language', type=str,location='header'),

        ]
    )
)
class UserViewSet(BaseView):
    ...
```

All Available options for `extend_schema`:
```python
operation_id: str | None = None,
parameters: Sequence[OpenApiParameter | Serializer | Type[Serializer]] | None = None,
request: Any = empty,
responses: Any = empty,
auth: Sequence[str] | None = None,
description: str | None = None,
summary: str | None = None,
deprecated: bool | None = None,
tags: Sequence[str] | None = None,
filters: bool | None = None,
exclude: bool = False,
operation: dict | None = None,
methods: Sequence[str] | None = None,
versions: Sequence[str] | None = None,
examples: Sequence[OpenApiExample] | None = None,
extensions: dict[str, Any] | None = None,
callbacks: Sequence[OpenApiCallback] | None = None,
external_docs: dict[str, str] | str | None = None
```
## Parameters
`OpenApiParameter` is used for [query, path, header, cookie] and has all available options for them like type, location, required, etc.

`parameters` param in `extend_schema_view` accept list of `openApiParameters` or `serializers`

You can pass **`get_swagger_params`** function with serializer and its wanted fields to be query parameters like example below:
```python
@extend_schema_view(
    get=extend_schema(parameters=get_swagger_params(BlogSerializer, ['category', 'tag', 'description']))
)
```

## Response
For detailed responses you can either pass a serializer or dictionary.

```python
responses={
        201: OpenApiResponse(response=MyCustomSerializer,
                                description='Created. New resource in response'),
        400: OpenApiResponse(description='Bad request (something invalid)'),
    },
```
We can use `inline_serializer` func for simple response.

## `extend_schema` with view

We can use `extend_schema` with view to implement its options to all view methods (ex. tags)
```python
@extend_schema(tags=['mfa'])
class ExampleApiView(APIView):
        ...
```

## More decorators
[extend_schema_serializer](https://drf-spectacular.readthedocs.io/en/latest/customization.html#step-4-extend-schema-serializer)

[extend_schema_field](https://drf-spectacular.readthedocs.io/en/latest/customization.html?highlight=extend_schema_field#step-3-extend-schema-field-and-type-hints)

