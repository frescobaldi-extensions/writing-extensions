# Writing Extensions for Frescobaldi

With the release of version 3.1 [Frescobaldi](http://frescobaldi.org), the [LilyPond](http://lilypond.org)
files IDE provides an API to load extensions.  These extensions may be used to provide arbitrary functionality
that would otherwise not be suitable for inclusion in the core application.  Also, this makes it possible for
third parties to add *custom* functionality for their own purposes.

Extensions are tightly integrated with the application and have access to both Frescobaldi's whole
existing code base and functionality, and to anything that can be done with Python and PyQt. So there
are virtually no limits to what can be achieved with extensions.  It would even be possible to include
a Tetris game or a video chat client in the Frescobaldi user interface - essentially anything one might
want to do without having to leave the integrated working environment.  At the same time the API provides
a very clean and simple interface to writing and integrating extensions.

Some of the most natural use cases for extensions are:

* Tools for code manipulation, e.g. generating or editing special notation with custom code commands
* GUI editors, for example a customized header editor
* Tools to handle custom code structure, e.g. to modify an existing score set-up
* Librarians to manage collections of scores
* Project management tools to manage and navigate score libraries, generating templates
  for new scores, and managing production chains

The "official" Frescobaldi extensions are available from https://github.com/frescobaldi-extensions,
but as extensions are completely self-contained it is perfectly possible to develop or publish
extensions in arbitrary places.  One thing to keep in mind, though, is that with the power of
extensions comes great responsibility: for the developer to be cautious and not to produce
harmful code (be it through bad programming skills or evil intent), and for the user to be conscious
about only loading extensions from trustworthy sources.

This documentation refers to *writing* extensions, not their *use*, users should never have the need
to look into these documents.  With the exception of a few examples the documentation is limited to
the *integration* of extensions in the Frescobaldi code, while explaining how to add actual
*functionality* and to make use of the existing code is way beyond the scope of this document.

*This documentation refers to version 0.5.0 of the Extension API.*

## The Big Picture

Creating an extension is in many respects quite similar to adding arbitrary functionality right into
Frescobaldi itself.  However, the basic steps are significantly simpler because one does not have to
identify half a dozen places to add code just for the *integration* of the new functionality.  Instead
an extension has a well-defined interface requiring to provide just a few classes to start adding
"real" functionality.  This section describes the main components of an extension from bird's eye
perspective.


### Overall Architecture

A Frescobaldi Extension is in essence a Python package with class inheriting from a specific base
class that serves as an entry point.  The user will have to place that package within a dedicated
extension directory they have specified in the Frescobaldi Preferences, and from there it will
automatically be loaded upon program start.  Extensions can be deactivated globally or per
extension, and great care has been taken to prevent faulty extensions to disturb Frescobaldi's
execution.  However, as the extension may add arbitrary code there is no failsafe way to do
so.

The `extensions` package in Frescobaldi (in the `frescobaldi_app/extensions` directory) provides
a number of base classes and utility functions an extension has to or can make use of.  For
reference beyond this manual it is recommendable to inspect the files in this directory, plus
`frescobaldi_app/preferences/extensions.py`.

The core components of an extension are:

* A core `Extension` class, the "main entity".
* An action collection to centrally manage available functionality
* Optionally a Tool Panel
* Optionally a configuration widget

We will go through them one by one, documenting the mandatory and optional componenty as well
as the API functions that are available.  However, there are two complementing repositories
available: https://github.com/frescobaldi-extensions/sample,
and https://github.com/frescobaldi-extensions/boilerplate.  The former is a heavily commented
minimal extension explaining in quite detail the basic steps to create an extension, while
the latter is rather short on comments but implements all the relevant basic steps and may
serve as a copy to build new extensions upon.

The following two chapters will deal with the mandatory `Extension` object, and the equally
mandatory metadata file `extension.cfg`.
