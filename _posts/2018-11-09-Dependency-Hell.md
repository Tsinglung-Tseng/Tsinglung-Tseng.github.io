---
title: Dependency Hell
key: 20181109
tags: Python
---


Dependency hell in Python often happens because pip does not have a dependency resolver and because all dependencies are shared across a project.

These strategies assume that you’re using a dependency management setup similar to what we use at Knewton, which includes using pip, virtualenv, and good practices for your requirements.txt and install_requires. 

## Detecting dependency hell

If you just run pip install -r requirements.txt, pip will happily ignore any conflicting versions of libraries. 

## Stable strategy: pip-conflict-checker and pipdeptree

```bash
$ pipdeptree

Warning!!! Possibly conflicting dependencies found:
* Keras==2.2.2
- keras-applications [required: ==1.0.4, installed: 1.0.5]
- keras-preprocessing [required: ==1.0.2, installed: 1.0.3]
------------------------------------------------------------------------
Warning!! Cyclic dependencies found:
* Keras => keras-applications => Keras
* Keras => keras-preprocessing => Keras
* Keras-Preprocessing => keras => Keras-Preprocessing
* Keras-Applications => keras => Keras-Applications
* jfs => doufo => jfs
* doufo => jfs => doufo
------------------------------------------------------------------------
```

## Avoid version conflicts with Virtual Environments

```python
# Virtual Environments ("virtualenvs") keep
# your project dependencies separated.
# They help you avoid version conflicts
# between packages and different versions
# of the Python runtime.

# Before creating & activating a virtualenv:
# `python` and `pip` map to the system
# version of the Python interpreter
# (e.g. Python 2.7)
$ which python
/usr/local/bin/python

# Let's create a fresh virtualenv using
# another version of Python (Python 3):
$ python3 -m venv ./venv

# A virtualenv is just a "Python
# environment in a folder":
$ ls ./venv
bin      include    lib      pyvenv.cfg

# Activating a virtualenv configures the
# current shell session to use the python
# (and pip) commands from the virtualenv
# folder instead of the global environment:
$ source ./venv/bin/activate

# Note how activating a virtualenv modifies
# your shell prompt with a little note
# showing the name of the virtualenv folder:
(venv) $ echo "wee!"

# With an active virtualenv, the `python`
# command maps to the interpreter binary
# *inside the active virtualenv*:
(venv) $ which python
/Users/dan/my-project/venv/bin/python3

# Installing new libraries and frameworks
# with `pip` now installs them *into the
# virtualenv sandbox*, leaving your global
# environment (and any other virtualenvs)
# completely unmodified:
(venv) $ pip install requests

# To get back to the global Python
# environment, run the following command:
(venv) $ deactivate

# (See how the prompt changed back
# to "normal" again?)
$ echo "yay!"

# Deactivating the virtualenv flipped the
# `python` and `pip` commands back to
# the global environment:
$ which python
/usr/local/bin/python
```