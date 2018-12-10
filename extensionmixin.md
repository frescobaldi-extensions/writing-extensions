# ExtensionMixin

The `Extension` class is the core component of an extension, and it is accessible
through `extension()` from many parts of an extension.  However, in some cases this
is not possible, and for these cases the `ExtensionMixin` class is provided as a
secondary base class.  This introduces shortcut attributes to access other extension
components as described below.

If the element to be created is a plain `QWidget` it is not necessary to use the mixin
class, instead the class can be directly derived from `extensions.ExtensionWidget` like

```python
import extensions
class MyWidget(extensions.ExtensionWidget):
    pass
```

If the class should extend arbitrary other classes (typically `QWidget` descendants,
but it works for any class) the mixin class can be used as a secondary base class:

```python
import extensions
class MyExtensionClass(QTabWidget, extensions.ExtensionMixin):
    pass
```

Once a class inherits the mixin it provides the following attributes (refer to the main
`Extension` class for descriptions:

* `extension()`
* `action_collection()`
* `current_document()`
* `mainwindow()`
* `panel()`
* `settings()`

Essentially these are convenience shortcuts but they intend to simplify code used in
extensions.

## Referencing the Main Extension Object

*NOTE:* There is one caveat to the magic of `ExtensionMixin`: the way it is enabled
to access the main extension object.  Basically there are two ways this can be achieved:
through the parent or a class variable.

If the object has a parent that has an `extension()` attribute nothing has to be done
manually.  This is the case with many objects in an extension (see all the relevant
descriptions), but not always.  For example a Tool Panel widget has an `extension()`
attribute, so any widgets created within that panel's main widget apply. But if that
main widget contains a `QTabWidget` its individual tabs are *not* children of the widget.
In these cases a class must provide a class variable `_example_name` that exactly
matches the extension's "name" property (the root directory name).  Failing to provide
this path will result in an informative exception that should clarify the problem.

```python
class MyClass(QObject, extensions.ExtensionMixin):
    _extension_name = "my-extension"
    def __init__(self):
        ...
```
