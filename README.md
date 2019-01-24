[![CircleCI](https://circleci.com/gh/django-beam/django-beam.svg?style=svg)](https://circleci.com/gh/django-beam/django-beam)
[![ReadTheDocs](https://readthedocs.org/projects/django-beam/badge/)](https://django-beam.readthedocs.io/en/latest/)

# django-beam

## This project is currently in alpha and not ready for use in production

django-beam provides you with a set of views, templates and integrations for the most common CRUD
applications.

The goal is having the functionality provided by django's own admin, but in a way that integrates
with your other frontend code.

## Features
- CRUD operations based on class based views
- Default templates with multiple themes (bootstrap 4, ...)
- Extensions for common use cases, e.g. export as csv, ...
- Support for related models (autocompletion, linking, ...)
- Familiar interfaces

## Documentation
See https://django-beam.readthedocs.io/en/latest/

## Example
```
# people/models.py
class Group(models.Model):
    name = models.TextField()


class Person(models.Model):
    name = models.TextField()
    email = models.EmailField()

    groups = models.ManyToManyField(Group)


# people/views.py
import beam

class PersonViewSet(beam.ViewSet):
    fields = ['name', 'groups']


class GroupViewSet(beam.ViewSet):
    fields = ['name']


# urls.py
urlpatterns += [
    path('person/', include(PersonViewSet().get_urls())),
    path('group/', include(GroupViewSet().get_urls())),
]


# settings.py
INSTALLED_APPS += [
    "beam",
    "beam.themes.bootstrap4",  # or choose any theme you like
]
```

## Themes
We currently ship only one theme.
* `beam.themes.bootstrap4`
  Using default Bootstrap v4 markup and include a basic Bootstrap CSS file.

  In order to use the bootstrap4 theme you have to install the optional dependency
  `django-crispy-forms` and add it to your INSTALLED_APPS in settings.py:
  ```
  INSTALLED_APPS = (..., 'crispy_forms')
  ```


## beam.contrib

We include a `beam.contrib` package that provides integration with several third party django apps.


### beam.contrib.autocomplete_light

Provides a viewset mixin for integration with `django-autocomplete-light`.

#### Usage

Add the mixin to your viewset, then use `django-autocomplete-light` as per the projects docs, for
example by overriding the widget dicts.

```python
# views.py
import beam
from beam.contrib.autocomplete_light import AutocompleteMixin

class GroupViewSet(AutocompleteMixin, beam.ViewSet):
    fields = ['name']
    autocomplete_search_fields = ["name"]

# forms.py
from django import forms
from people.models import Person

from dal import autocomplete

class PersonForm(forms.ModelForm):
    class Meta:
        model = Person
        fields = ["name", "email", "groups"]
        widgets = {
            "groups": autocomplete.ModelSelect2Multiple(
                url="people_group_autocomplete"
            ),
        }
```

