# How to Create a Package of Custom Python Functions and Use It With Databricks
## (*or* "Just Enough Python to Be Dangerous")

As data science teams tackle more and more complex problems with Databricks,
they often discover the need to share custom Python functions across multiple
notebooks (or even across multiple Databricks workspaces).  An excellent option
for managing these Python functions is to create a custom Python library and
upload that library to Databricks.

While many data scientists use Python to write analytics code, some of them may
not be familiar with the Python packaging tools.  This guide demonstrates the
very basics of creating your first, ultra-simple Python package.  We'll also
show you how to upload that package to Databricks.  This should help you and
your team get started with a custom library of Python functions.

## Example Scenario
Suppose you are a data scientist at Contoso Ltd.  Your team is using Python
in Databricks to analyze large volumes of data.  One of your notebooks
contains some very useful Python functions.  You would like to use
these functions in other notebooks, but you do not want to copy-and-paste the
code to other notebooks because this would create redundant copies of the
functions that would need to be maintained.

For this example scenario, let's suppose your Python functions are doing some
*very* complex analysis, like adding two numbers together or subtracting one
number from another number.  These highly advanced Python functions in your
notebook might look like this:

```python
def AddNumbers(i, j):
    return i + j

def SubtractNumbers(i, j):
    return i - j

def MultiplyNumbers(i, j):
    return i * j
```

We want to put these functions into a Python package so that we can have one
copy of the code that is shared across multiple notebooks on multiple clusters
in multiple workspaces.

Since these are custom Python functions for our company, Contoso Ltd., we want
to name our library `contoso_functions`.

## Step 1:  Install Python
To create a Python package, you will need a Python development environment.
Unfortunately, you cannot use Databricks to build your package.  You will need
to [install Python](https://www.python.org/downloads/) on your local machine or
on a virtual machine.  (The [Azure Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)
is an excellent option if you want a pre-made, cloud-based Python dev environment.)

## Step 2:  Create the Proper Directory Structure
When creating a Python package, the tools expect our files to be structured
into directories.  This structure affects what modules are in your library.

Here is how our files should be structured:

```
    contoso_functions
      |
      |-- contoso_functions
      |   |
      |   |-- __init__.py
      |   |-- functions.py
      |
      |-- setup.py
```

Start by creating a new directory with the name you want to use for your
package.  Note that names must use only lowercase letters and underscores.
(Dashes are not allowed!)

In this directory, you will need to create an empty file called `setup.py`.
(We will add content to this file in the next step.)

You will also create a subdirectory for each module you want in your package.
Since we're creating the most minimal package possible, we will have just
one module, and we will name it the same as our package
(`contoso_functions`).

In this module subdirectory, you will create two empty files: `__init__.py`
and `functions.py`.  Again, we will add content to these files below.

## Step 3:  Define Package Properties in `setup.py`
The `setup.py` file is a very basic Python script that calls a function called
`setup` and passes parameters that describe your package.  This includes the
name of the package, a description, and information about the author. (That's
you!)

Here is the content of the script for our Contoso example:
```python
from setuptools import setup

setup(name='contoso_functions',
        version='0.1',
        description='custom business functions for Contoso, Inc.',
        url='http://contoso.com',
        author='Jeremy Peach',
        author_email='jeremy@contoso.com',
        license='MIT',
        packages=['contoso_functions'])
```

## Step 4:  Fill in the `__init__.py` File
The `__init__.py` file contains Python code that will be executed whenever the
module is loaded in your notebook.  This can be used to initialize variables or
run an initialization function automatically.

However, for a bare minimum package, you just need to put one line of code in
your script that will load all of the functions you define in your
`functions.py` file.
```python
from .functions import *
```

## Step 5:  Write Code!
Now comes the fun part... you get to actually write your Python code.  For this
super basic Python package, we will just pile all of our Python functions into
a single file called `functions.py`.

## Step 6:  Build the Package
We have now provided all of the content that we need for our package.  Now we
use the Python build tools to combine all of our code into a single package
file.  For maximum compatibility with Databricks, we will create our package
using [Python's "wheel" format](https://packaging.python.org/discussions/wheel-vs-egg/).

To build the package, open a console, and switch to the directory where you put
your `setup.py` file.  (In our example, this would be in the top-level
`contoso_functions` directory.)  Then use Python to execute the `setup.py`
script with the following command:
```
python setup.py bdist_wheel
```

This process will create new subdirectories in our package directory that
contain the intermediate artifacts of the build process.  You can ignore the
`build` subdirectory and the `contoso_functions.egg-info` subdirectory.  The
good stuff (*i.e.* our package file) can be found in the `dist` subdirectory.

The `dist` subdirectory should contain one file:
```
contoso_functions-0.1-py3-none-any.whl
```
The file's extra long name contains information about the package version
("0.1"), the version of Python used ("py3"), something called the
"[ABI tag](https://www.python.org/dev/peps/pep-0425/#abi-tag)" value ("none"),
and the platform for which the package was built ("any").

## Step 7:  Upload the Package to Databricks
Now that we've built our package, let's upload it to Databricks so we can use
it in our notebooks.  To do this, we will create a new
[Library](https://docs.databricks.com/user-guide/libraries.html) in our
Databricks workspace.

To create a library, log in to Databricks and open the "Workspace" pane.
Right-click the directory where you want to install the library and select
"Create > Library".

<a href="assets/Create Library.png"><img src="assets/Create Library.png" width="400" /></a>


On the "Create Library" screen, select "Upload" as the library source and select
"Python Whl" as the library type.

<a href="assets/Upload Wheel 1.png"><img src="assets/Upload Wheel 1.png" width="400" /></a>

Upload the .whl file created in the previous step.

<a href="assets/Upload Wheel 2.png"><img src="assets/Upload Wheel 2.png" width="400" /></a>

Once your libary is created in Databricks, you must
[install it on your cluster](https://docs.databricks.com/user-guide/libraries.html#install-a-library-on-a-cluster)
before you can use it in your notebooks.

## Step 8:  Use Your Functions in a Notebook
Once you have [installed your library on your cluster](https://docs.databricks.com/user-guide/libraries.html#install-a-library-on-a-cluster),
you can start using your custom Python functions in your Databricks notebooks.
You only need to import the functions you wish to use from your module.

<a href="assets/Use Functions.png"><img src="assets/Use Functions.png" width="700" /></a>

## Next Steps
Now that you've got a very basic Python library created, you can continue to
grow and refine your collection of custom Python functions.

- **Structure Your Code:**  Instead of dumping all of your functions into just
one file, you can split them into multiple files for easier management.

- **Multiple Modules:**  As the number of files increases, you may want to
organize your functions into multiple modules.  This will give you more
control over which functions get imported into which notebooks.

- **Deploy with Azure DevOps:**  Eventually your simple catalog of Python
functions could grow to become a complex piece of software with multiple
members of your team contributing code and deployments of the library in
multiple Databricks workspaces.  To manage the development and deployment of the
library, you might consider a tool like
[Azure DevOps](https://azure.microsoft.com/services/devops).