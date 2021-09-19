# Django custom User

A custom User on Django (mostly extending and overriding the existing classes and methods).  
This repository or the [`accounts`][accounts] application can be used as a basic start for a project where a custom User based on the AbstractUser is necessary.  


## Settings

Add the [`accounts`][accounts] application to INSTALLED_APPS in [settings.py]  

```python
INSTALLED_APPS = [
	...
    "accounts.apps.AccountsConfig",
]
```

Specify the custom User model in [settings.py]  

```python
AUTH_USER_MODEL = "accounts.User"
```

## Migration

Creating a custom User model is not adviced mid-way of the project. Create custom User at the start of the project and migrate. Read more [here](link_01).  

> Due to limitations of Django’s dynamic dependency feature for swappable models, the model referenced by AUTH_USER_MODEL must be created in the first migration of its app (usually called 0001_initial); otherwise, you’ll have dependency issues.

**Note:** Before applying the initial migrations, create migrations for [`accounts`](accounts) application and migrate.  

```sh
python manage.py makemigrations accounts
python manage.py migrate
```

## Referencing User in applications

Referencing the custom User model can be done by using `django.contrib.auth.get_user_model()`

```python
from django.contrib.auth import get_user_model
User = get_user_model()
```

or by `AUTH_USER_MODEL` project setting.

```python
from django.conf import settings
User = settings.AUTH_USER_MODEL
```

Which and when to use can vary according to the context. Read [this SO post](link02) on get_user_model vs AUTH_USER_MODEL.  

[accounts]: ./accounts/
[settings.py]: ./django_custom_user/settings.py

[link01]: https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#changing-to-a-custom-user-model-mid-project
[link02]: https://stackoverflow.com/questions/24629705/django-using-get-user-model-vs-settings-auth-user-model
