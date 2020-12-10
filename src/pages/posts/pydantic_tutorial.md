---
title: Pydantic Tutorial
excerpt: >-
  Tutorial on building a schema through Pydantic
template: post
---
Introduction to Pydantic
==============================
[Pydantic](https://pydantic-docs.helpmanual.io/) is a python package designed to expand on the concept of typing in python. It is used to enforce type hints in python, allowing objects to be validated at runtime.


Typing in Python
==============================
Python does not inherently enforce typing of functions or variables at runtime. Instead, python allows for type hints to be specified in order to inform users and other developers what is expected by a function and what is provided.

For primitive types, that is `str`, `int`, `float`, and `bool` , this simply takes the form of an annotation.
```python
def name_typing(name: str) -> str:
    return name
```

The variable `name` is annotate with a `:` followed by the type, in this case, we expect a name to be a `str`. The function's return type is also hinted at. After the parameters, but before the `:` we use an `->` to denote there is a type hint, followed by the type. In this case we are using a string as well.

Here we test the function with a couple of variables.
```python
name_str = 'John'
name_num = 5

print(name_typing(name_str))
print(name_typing(name_num))
```
We get the following output:
```output
John
5
```
Python does not enforce the type, so the function will return the variable and print both out correctly.

If we want to have more complex types, such as `List`, `Dict`, `Union`, or `Optional` types, we need to utilize python's `typing` library.

```python
from typing import List, Dict, Union, Optional
```

We can then utilize them in type hints.
```python
registrant_names: List[str]
birthday: Optional[str]
```

Pydantic Model
==================================
COVER: Base Pydantic model, and why it is used.

Each component has two schema, an Input Schema and an Output Schema. Depending on the component the two schema may be the same or different. To define a schema we utilize a pydantic model to create a set of variables with specific types to ensure that a component can run for a given input as long as it conforms to the Input Schema.

The model used by Pydantic is the BaseModel. We can import this from pydantic and then inherit it when we create a schema.
```python
from pydantic import BaseModel

class Schema(BaseModel):
```
The benefits of inhertiting the BaseModel is the validation that comes with it. When and instance of an object is created from a schema, the values assigned to each variable are validated against their assigned type.

```python
class SchemaExample(BaseModel):
    var1: int
    var2: str
    var3: Optional[float]
```

To provide further information about each variable, we can utilize the Field method from pydantic.
```python
class SchemaExample(BaseModel):
    var1: int = Field(..., description="This is the first variable for the example schema.")
    var2: str = Field(..., description="This is the second variable for the example schema.")
    var3: Optional[float]
```
The first argument in the `Field` method is a positional argument for the default value of the variable. Entering `...` for the default value will let pydantic know the variable has no default value. There are a number of keyword arguments for the `Field` method that can be found in the [Pydantic](https://pydantic-docs.helpmanual.io/) documentation, but we will mention `description` as a useful one to include, as it allows for more detailed documentation for each variable.

For a more concrete example, we can look at the `DockingInput` schema.
```python
from .base import SimBase
from typing import List, Optional, Tuple, Union
from mmelemental.models.molecule.mm_molecule import Molecule
from pydantic import Field

class DockingInput(SimBase):
    ligand: Molecule = Field(
        ..., description="Molecule model for a candidate ligand (e.g. drug)."
    )
    receptor: Molecule = Field(
        ..., description="Molecule model for a receptor (e.g. protein)."
    )
    searchSpace: Optional[List[Tuple[float, float, float, float, float, float]]] = Field(
        None,
        description="A 3D box defined by (xmin, xmax, ymin, ymax, zmin, zmax)."
        "The search space effectively restricts where the movable atoms, including those in the flexible side chains, should lie.",
    )
```
The first thing to notice is that we did not inherit from Pydantic's `BaseModel` class. MMIC utilize an extension of the `BaseModel`, and the `DockingInput` inherits a further extension, the `SimBase`, which is a basic simulation schema. Since our inheritance tree includes the `BaseModel` we will enforce typing on the variables within the schema. Similar to normal inheritance, we inherit all of the typed variables from our parent class.

Therefore, the `DockingInput` contains all the typed variables from the `SimBase` schema, and adds three new variables, `ligand` which is of type `Molecule`, `receptor` which is also of type `Molecule` and `searchSpace` which is an optional `Tuple` of `floats` that defines a 3D box to search within.


Validation
=================================
Cover basic validation being performed on all the variables in the schema.
cover how to add custom validators to a variable in a schema.

Pydantic performs validation on all the variables in a schema. By default, this means if you specify a type for a variable and pass it a value of a different type, it will provide you an error message with useful information. Note that we use typed schema variables as keyword arguments, not positional arguments.

```python
example = SchemaExample(var1="Hello", var2=5)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "pydantic/main.py", line 362, in pydantic.main.BaseModel.__init__
pydantic.error_wrappers.ValidationError: 1 validation error for SchemaExample
var1
  value is not a valid integer (type=type_error.integer)
```
We can see that it reported a single validation error. Pydantic will try and perturb primitive types if they have a simple conversion. In this case, since var2 is a string type, and an integer, `5` can easily be converted losslessly by python, it will assume `5` was meant to be passed as a string. However, since the first variable is looking for an `int` and it was passed a string, it rovides information on the variable that had an error, and what the expected type was.

As schema become more complicated, simple assignment validation may no longer be sufficient. In addition, you may wish to assign additional conditions to the values of variables outside of simply their type. Pydantic supports custom validators to cover these cases.

To write a custom validator, you must import a few additional things from pydantic.
```python
from pydantic import validator, ValidationError
```
This will import the decorator `@validator` which is used to specify a method is used for validation, and `ValidationError` can be used to check if an error was thrown during validation.

```python
from pydantic import BaseModel, validator, ValidationError
class SchemaExample(BaseModel):
    var1: int = Field(..., description="This is the first variable for the example schema.")
    var2: str = Field(..., description="This is the second variable for the example schema.")
    var3: Optional[float]
    
    @validator('var2')
    def var2_alphanumeric(cls, v):
        assert v.isalnum(), 'var2 must be alphanumberic'
        return v
```
We have added a custom validator to ensure that `var2` is only made of alphanumeric characters. We use the decorator `@validator('var2')` to specify that the following function should be used to perform validation on the variable `var2`. We pass the `cls` since this method is used on the `SchemaExample` class and not on an instanced object and by pydantic convention use `v` for the value that is being assigned to the variable.

Here we can test the validator to ensure it is working.
```python
example = SchemaExample(var1=5, var2='hellos12345_@#$')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "pydantic/main.py", line 362, in pydantic.main.BaseModel.__init__
pydantic.error_wrappers.ValidationError: 1 validation error for SchemaExample
var2
  var2 must be alphanumberic (type=assertion_error)
```

A more thorough description of validators and how to write custom ones can be found within the [pydantic documentation](https://pydantic-docs.helpmanual.io/usage/validators/).

MMIComponent ProtoModel
=================================

For the MMIC poject, we utilize an extension of the pydantic BaseModel. This was originally developed for the QCArchive project and can be found within the [QCElemental](https://github.com/MolSSI/QCElemental/blob/master/qcelemental/models/basemodels.py) package.

Any schema based on this `ProtoModel` gains a few additional features.
It covers a few ways to parse in a model. First a model object can be parsed in from a raw string or bytes using the `parse_raw()` method. This is useful if the values for a model are contained in a serialized form, such as `json` or `msgpack-ext`. A model can also be parsed from a file using the `parse_file()` method. Current supported encodings are `json`, `msgpack-ext`,  and `pickle` files.

Similarly, a model can be serialized into `json`, `json-ext`, or `msgpack-ext`. This allows the model to be shipped around as a set of bytes or as a string.

Finally, it allows for the comparison of instanced objects. The `compare()` method can be used to compare the current object to a given object recursively.
