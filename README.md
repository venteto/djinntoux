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

### Abstract Models
As an example in a `models.py` file:

```python
from djinntoux.abstract import EditLink, Timestamps, UUIDpk7
```

#### EditLink
Set `ADMIN_PATH` in project settings (do not add a leading slash), e.g. to keep the Django default path without using an environment variable:
```python
ADMIN_PATH = 'admin/'
```

Then in templates you can use like so:
```jinja
<a target="_blank" href="{{ obj.get_edit_path }}">Edit</a>
```

### Custom Users App
In project settings add `djinntoux.users` to `INSTALLED_APPS` and set `AUTH_USER_MODEL = 'zy_users.User'`

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