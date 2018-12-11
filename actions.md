# The Action Collection

Action collections are Frescobaldi's main way to organize actions/functionality.
Actions are collected together with their textual representations, icons, and
possibly state and can be referenced from arbitrary GUI elements.

It is not mandatory to provide an action collection to an extension, but it is
hardly conceivable that there are many use cases for extensions without.


## Integration in the Extension

An action collection is integrated in the extension simply by providing its class
as a class variable to the `Extension` object.  Since this is not only accessed
in the `__init__` method the  action collection class has to be defined *before*
the `Extension` class, which is typically done in a separate module:

```python
import extensions
from . import actions
class MyExtension(extensions.Extension):
    _action_collection_class = actions.MyActions
```

Through this simple setting of a class variable an extension can integrate an
action collection.  This is the way extensions can provide and integreate their
optional components.


## Definition of an Action Collection

An extension's action collection must inherit from
`extensions.actions.ExtensionActionCollection`, which itself inherits from
`actioncollection.ActionCollection`.  It provides a number of useful features
and slots, some of which can and some of which have to be implemented.


### Mandatory or Optional Implementation

`configure_menu_actions()`
This can be used to configure which actions are visible in which menus.  By
default all actions are exposed (sorted by their displayed text) in the global
`Tools=>Extensions=><extension-display-name>` menu, and none in any context
menu.  The implementation of this method should consist of one or multiple
calls to `set_menu_action_list()`.

`set_menu_action_list(target, actions)`
Configure the extension menu in one place.  `target` may be one out of

* `tools` (the global tools menu)
* `editor` (the text editor's context menu)
* `musicview` (the music viewer's context menu)
* `manuscriptbiew` (the manuscript viewer's context menu)

`actions` can take various forms.  The special value `None` means that *all*
actions are used, sorted by their display text, while a *list* specifies
explicitly which actions are exposed in the given menu.

An empty list will mute the menu, while a list of actions will simply provide
these actions to the menu in the given order.

Any action may be replaced by a `QMenu` instance, which is the way to provide
custom hierarchical extension menus.

`connect_actions()`
This slot may be implemented to connect actions to slots that are available
throughout the extension's lifetime.  Note that it is up to the extension
developer to decide whether this is the best place to manage action connections
or whether they should better be handled in the main `Extension` object. *NOTE:*
This slot is called during the action collection's `__init__()` method, before
settings have been loaded.

`load_settings()`
This can be implemented to make the initial state of actions depend on saved
settings.

Further methods that are available:

`by_text()`
Return a list of all actions sorted by their display text.  (Mnemenics &
characters are ignored for the sorting.)

`extension()`
A reference to the Extension object.

`menu_actions(target)`
Return the menu actions list for the given target.
Presumbaly has no end-user use case.
