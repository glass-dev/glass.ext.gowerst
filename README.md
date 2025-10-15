Template for GLASS extension packages
=====================================

This repository contains a template for how GLASS extension packages can be
structured to appear as part of the `glass.ext` namespace.

To get started, you can either [use this template] to create a new GitHub
project, or clone the repository to manually set up the new project.

If your extension is to be imported as `import glass.ext.helloworld`, the name
of your extension package should be `glass.ext.helloworld` -- dots and all.
It also makes sense to use that as the name of your repository.

To create a GLASS extension, you do not need to use this template.  The only
requirement is that your project has your extension modules under the
`glass.ext` namespace, meaning in a file such as `glass/ext/helloworld.py`.
Apart from the `ext` folder, the `glass` folder should be empty -- modules
placed there will not be importable, because the core `glass` module is not a
namespace, only `glass.ext` is.

If you are using this template, it is enough to change the project metadata in
[`pyproject.toml`].  Be thorough, and look at the comments.  Then, you need to
rename the Python module from [`glass/ext/template.py`] to your intended module
name.  This should match the project name you set in the metadata.  As usual,
if your module is large and spans multiple files, you can create a subfolder
for it, e.g. `glass/ext/helloworld/`.

Lastly, don't forget to match the author information in [`pyproject.toml`], the
[`LICENSE.txt`] file, and the header of the now-renamed
[`glass/ext/template.py`] module.

[use this template]: https://github.com/glass-dev/glass.ext.template/generate
[`pyproject.toml`]: pyproject.toml
[`glass/ext/template.py`]: glass/ext/template.py
[`LICENSE.txt`]: LICENSE.txt
