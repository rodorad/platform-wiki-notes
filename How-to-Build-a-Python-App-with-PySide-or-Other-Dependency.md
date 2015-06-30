# Buildpack

You need to use a special Python buildpack that allows the preinstallation of wheel packages for dependencies.
Check the existing buildpack to se if _python_buildpack-v1.3.5-whls.zip_ already exists.


## App preparation

1. Follow build instructions for PySide (or other) and create a wheel package.

    http://pyside.readthedocs.org/en/latest/building/linux.html

1. Create a directory called _vendor-whl_ inside the App tree, and move a copy of the wheel package built in the previous step to this location.

1. Push an app with our custom python buildpack.