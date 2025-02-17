# djinntoux
Generic reusable bits for Django projects

## Disclaimer
This package is only intended for my own personal use, install at your own risk

## Rebuild
Assumes `twine` is installed and an API token is stored in a `.pypirc` file:

```bash
rm -rfv dist/ && python -m build && twine upload --verbose dist/*
```

## Installation
```bash
pip install djinntoux
```

## Usage

### dproj

#### settings_reuse
In your own project settings, use like so:
```python
from djinntoux.dproj.settings_reuse import *  # noqa

...

INSTALLED_APPS = BASE_APPS + []
```

This reusable settings module expects `DJANGO_SECRET_KEY` to be set in your environment. For example, if you are just running your project locally, you could use an `.envrc` file for your development environment ...
```bash
...
export 'DJANGO_SECRET_KEY'='django-insecure-321-do-not-use-this!!!'
...
```
... and then use a `systemd` unit file for a mock production environment ...
```ini
...
[Service]
Environment='DJANGO_SECRET_KEY'='django-insecure-321-do-not-use-this!!!'
...
```

#### urls_reuse
In your project root URLconf, use like so:
```python
urlpatterns = [
    path('', include('djinntoux.dproj.urls_reuse')),
    ...
]
```

### Abstract Models
As an example in a `models.py` file:

```python
from djinntoux.abstract import EditLink, Timestamps, UUIDpk7

...

class Account(Timestamps, EditLink):
```

#### EditLink
Set `ADMIN_PATH` in project settings (do not add a leading slash), e.g. to keep the Django default path without using an environment variable:
```python
ADMIN_PATH = 'admin/'
```

Then in templates you can use like so:
```django
<a target="_blank" href="{{ obj.get_edit_path }}">Edit</a>
```

### Custom Users App
In project settings add `djinntoux.users` to `INSTALLED_APPS` and set `AUTH_USER_MODEL = 'zy_users.User'`

The main app files are mostly copied verbatim from [upstream](https://github.com/django/django/tree/main/django/contrib/auth), tweaked to replace the stock `first_name` and `last_name` conventions from upstream with slightly more international options, lightly inspired by the [Cookiecutter Django](https://github.com/cookiecutter/cookiecutter-django/blob/master/{{cookiecutter.project_slug}}/{{cookiecutter.project_slug}}/users/models.py#L27) project.

### Renames
In project settings make sure `INSTALLED_APPS` reflects something akin to this:
```python
    'djinntoux.renames.AuthRenamedConfig',
    # 'django.contrib.auth',

    # https://docs.djangoproject.com/en/dev/ref/contrib/sites/
    # 'django.contrib.sites',
    'djinntoux.renames.SitesRenamedConfig',
```

### Scraping
```python
from djinntoux.scraping import get_host_and_title
```

Then use like so:
```python
    def save(self, *args, **kwargs):
        if not self.link_title:
            self.link_title = get_host_and_title(self)[1]
        super(Link, self).save(*args, **kwargs)
```