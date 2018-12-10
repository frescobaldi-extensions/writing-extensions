# The Extension Object

At the core of any Frescobaldi extension is the `Extension` object serving as the entry point
into the Python package.  It has to be derived from `extensions.Extension` and does not
necessarily have to do anything except exist in order to properly load as a Fresocbaldi
extension.

Note that while being located in an arbitrary location on the user's disk the extension
code can always `import` any module or package from within Frescobaldi's
`frescobaldi_app` root.  This has already been taken care of by being loaded from within
Frescobaldi.  Therefore the absolute minimal working example of an extension is:

```python
# <extension-directory>/__init__.py

import extensions
class MyExtension(extensions.Extension):
    pass
```

On the Python side this is sufficient to define an extension, of course without any
useful functionality.  In order to *properly* load there must be a valid metadata file
`extension.cnf` in the extension directory, but we will discuss this in the next chapter.

This will instantiate an object of the `MyExtension` class and add it to the list of
loaded extensions.  Internally this extension will be referred to by a "name" key that
is read from the *directory* the extension lives in.  There can be situations where this
"name" has to be written in the code (see discussion of `ExtensionMixin`), so it is crucial
that this directory has a meaningful and unique name without spaces, and that a remote
Git repository through which the extension might be distributed has that very same name.
An extension also has a "display name", but that one is defined in the metadata file.

The `Extension` base class provides a number of useful functions that are available
everywhere the extension object is accessible (and it is accessible in most places
without further action).  First we will describe the functions that may generally
be of interest calling from within an extension, followed by listing the remaining
items.

`action_collection()`
A reference to the extension's action collection.  If no custom class is defined for
the extension this action collection will be "empty", i.e. not provide any actions.
For more information on action collectsions please refer to
`frescobaldi_app/actioncollection.py`.

`config_widget()`
A reference to the extension's configuration widget if the extension provides one.
If not, this will return None.  The configuration widget will be used in the
Preferences dialog.

`display_name()`  
The extension's human-readable name.  In case of faulty metadata this falls back
to the internal "name" derived from the directory.

`has_icon()`
If the extension has a file `icons/extension.svg` this will be taken as the extension's
main icon and used in the GUI automatically.

`icon()`
Reference to the `QIcon` instance of the extension's icon if available, None otherwise.

`infos()`
A dictionary with the extension's metadata, as parsed from `extension.cnf`.

`load_time()`
The time needed to load the extension in ms.  This is populated upon loading
and automatically displayed in the overview in the Preferences dialog.

`menu(target)`
Returns the menu used for the given *target*.  A menu is a collection of actions that
is specified in the action collection class.  Different menus are available for the
`Tools=>Extensions` submenu and various context menus.  Extension code usually does
have no need to directly access this code.

`name()`
The internal name of the extension.

`panel()`
The extension's Tool Panel, or None if no panel is provided.

`parent()`
A Reference to the global `Èxtensions` object that manages all loaded extensions.
This can also be accessed from anywhere through `app.extensions()`.

`root_directory()`
The absolute path to the extension's root directory.

`settings()`  
The extension's Settings object.  Handling settings has been made even simpler than
Qt's QSettings() objects used elsewhere in Frescobaldi.

`widget()`
The widget in the extension's Tool Panel, if provided. None otherwise.

A few attributes provide shortcuts to elements in Frescobaldi's application. These
might also be accessed through the original means in the code base, but they are made
more accessible from extensions through these direct attributes.

`current_document()`
The document currently open in the editor. *TODO: Check the case when there is only
the empty document there.*
NOTE: Please refer to `document.py` for more information about the "document".

`mainwindow()`
A reference to the currently open main window

`text_cursor()`
The current QTextCursor object in the current document


Two slots are called that can be implemented in subclasses:

`app_settings_changed()`
This is called after the settings have been modified in the Preferences dialog.
An extension should implement this slot if (and only if) it has to respond to
settings changes in *other* areas of the code, or if settings changes in the
extension have to be handled atomically in one common transaction.  In all
default cases the next slot should be used instead.

`settings_changed(key, old, new)`
This is called whenever a setting *within* the extension has been changed.  This
can occur from the configuration widget in the Preferences dialog (it will be called
for each changed setting individually) or from anywhere in the extension when
GUI widgets simply want to remember their state. Implement this slot to have the
GUI respond to such settings changes.


As stated above many elements within an extension provide direct access to an
`extension()` attribute that can be used to quickly access information from other
components.  However, this is not *always* possible, and for these cases an
`ExtensionMixin` class is provided, which is described in the next chapter.
