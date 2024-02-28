
## Add Swagger for a Django project with drf-spectacular


### Installatioin
```
 pip install drf-spectacular
```
Add drf-spectacular to installed apps in **settings.py**

```python
INSTALLED_APPS = [
    ...
    'drf_spectacular',
]
```

registering the drf-spectacular's AutoSchema as the default schema class for DRF for generating the OpenAPI schema, enabling Swagger documentation

```python
REST_FRAMEWORK = {
    ...
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}
```
drf-spectacular has settings options but not required. (for more settings check [settings](https://drf-spectacular.readthedocs.io/en/latest/settings.html))

Recomended example:
```python
SPECTACULAR_SETTINGS = {
    'TITLE': '{project title}',
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

Now **drf-spectacular** will automatically detect most of endpoints and their properities and show them in `swagger` page.


## Customization
To customize detected endpoint options or add more tailored documentation if the package couldn't detect your endpoint as you want, the `@extend_schema_view` decorator can be applied to `views`, and the `@extend_schema` decorator can be used for individual `methods`.

### Example:

```python
@extend_schema_view(
    get=extend_schema(
        description='get user info',
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
## Parameters option
`OpenApiParameter` is utilized for specifying parameters in [query, path, header, cookie], encompassing all necessary options such as type, location, and required option, etc.

`parameters` param in `extend_schema_view` accept list of `openApiParameters` or `serializers`

## Response
To achieve a more detailed response, you have the option to pass either a serializer or a dictionary.
```python
responses={
        201: OpenApiResponse(response=MyCustomSerializer,
                                description='Created. New resource in response'),
        400: OpenApiResponse(description='Bad request (something invalid)'),
    },
```
We can use `inline_serializer` function for simple response. This lets you conveniently define the endpoint’s schema inline without actually writing a serializer class.

## `extend_schema` with view

You can use `extend_schema` with a view class to apply its options to all view methods (ex. tags that categorize this view class's endpoints under a single label on the Swagger page.)
```python
@extend_schema(tags=['mfa'])
class ExampleApiView(APIView):
        ...
```

## More decorators
[extend_schema_serializer](https://drf-spectacular.readthedocs.io/en/latest/customization.html#step-4-extend-schema-serializer)

[extend_schema_field](https://drf-spectacular.readthedocs.io/en/latest/customization.html?highlight=extend_schema_field#step-3-extend-schema-field-and-type-hints)

## offical documentation for spectacular-drf package
[Docs](https://drf-spectacular.readthedocs.io/en/latest/)


