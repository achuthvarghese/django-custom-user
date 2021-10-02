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

## Migrations

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

# Authentication Backends

By default, `AUTHENTICATION_BACKENDS` is set to:

```python
['django.contrib.auth.backends.ModelBackend']
```

If there are number of authentication backends specified in `AUTHENTICATION_BACKENDS`, then Django tries authenticating across all of its authentication backends. If the first authentication method fails, Django tries the second one, and so on, until all backends have been attempted.

The default backend checks if the `username` is provided in `authenticate` method, else takes `USERNAME_FIELD` and uses it to check if a User exists or not. Default `USERNAME_FIELD` being `username`, only username can be used to login. If `USERNAME_FIELD` is set to `email` then, only email can be used to login.

What if login has to be done using either `username` or `email`? Create a custom backend to do so.

## Email or Username Backends

Use the custom backend class `UsernameEmailBackend` in [accounts/backends.py][backends.py] by adding it to `AUTHENTICATION_BACKENDS` in [settings.py]. It is not always necessary to use the default backends.

```python
AUTHENTICATION_BACKENDS = [
    "accounts.backends.UsernameEmailBackend",
    # "django.contrib.auth.backends.ModelBackend",
]
```

With this backends, the `email` field also has to be set to `unique` since there should not be more than one user with the same email address.

```python
email = models.EmailField(_('email address'), unique=True)
```

If there are, `MultipleObjectsReturned` error will be raised when trying to get the `User` object.
![][image_001]

<!-- File Links -->
[accounts]: ./accounts/
[settings.py]: ./django_custom_user/settings.py
[backends.py]: ./accounts/backends.py

<!-- Image Links -->
[image_001]: ./screenshots/image_001.png 

<!-- External Links -->
[link01]: https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#changing-to-a-custom-user-model-mid-project
[link02]: https://stackoverflow.com/questions/24629705/django-using-get-user-model-vs-settings-auth-user-model
