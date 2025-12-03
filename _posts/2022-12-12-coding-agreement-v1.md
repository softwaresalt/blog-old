---
title: "Coding Agreement: Python"
date: 2022-12-12
layout: single
categories:
  - Technical
tags:
  - Teams
  - Process
---

## Coding Agreement: Python

## Purpose of this document

This document sets a baseline to ensure code quality during our engagement. It provides guidelines on Python coding best practices.
You will also find insights on responsibilities expected for a developer before opening a Pull Request, and for a reviewer to ensure a good and constructive review.

## Code review

[Code review](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/inclusion-in-code-review/) is an important part of our job as software engineers. It helps other to grow while improving code quality and share understanding between developers.

### Cheat sheets

Whether you are a junior software engineer or an experienced one, [anyone can have valuable insights](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/inclusion-in-code-review/#dealing-with-the-impostor-phenomenon)!

Here are below two cheat sheets to follow as a PR author and as PR reviewer.

#### Author cheat sheet

* [Is the size acceptable?](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/pull-requests/#size-guidance)
* [Is it well described?](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/pull-requests/#pull-request-description)
* [Is the check list complete?](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/pull-request-template/pull-request-template/#pr-checklist)
* [Is the language specific guidance respected?](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/recipes/)
* [Are relevant reviewers (if not enforced by policy) added?](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/process-guidance/author-guidance/#add-relevant-reviewers)

When a review is conducted [be open to feedback](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/process-guidance/author-guidance/#be-open-to-receive-feedback).

You can find more detailed guidance as an author on the [CSE Code With Engineering Playbook](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/process-guidance/author-guidance/).

To go further, we can also look at the [Azure SDK coding guidance](https://azure.github.io/azure-sdk/general_introduction.html) that can be a great source of inspiration, depending on the project we are working on.

#### Reviewer cheat sheet

* [Understand the code you are reviewing and take your time](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/process-guidance/reviewer-guidance/#general-guidance)
* [Be inclusive, and foster a positive code review culture](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/process-guidance/reviewer-guidance/#foster-a-positive-code-review-culture)
* [Ensure code quality](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/process-guidance/reviewer-guidance/#code-quality-pass)
* Ask for a walkthrough from the author if necessary (do not use walkthrough to hide a PR's complexity, but rather as a way to onboard quickly on it)

You can find more detailed guidance as a reviewer on the [CSE Code With Engineering Playbook](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/process-guidance/reviewer-guidance/).

## Tooling used

To enforce these rules, a set of tools will be used. Decisions on tools to use can be found in the corresponding [ADR](../adr/022_coding_tools.md).

## Coding guidelines

We are going to use Python as development language. Here are below a set of best practices to respect while developing. These guidelines need to be followed by developers. Reviewers will also need to validate these guidelines during PR review.

### Code Layout [MUST]

* `import`: first standard libraries, then third party and finally local libraries. All group alphabetically sorted.
* `blank lines`: two blank lines surrounding classes and top-level functions. Methods inside functions are surrounded by a single line.
* `indentation`: use 4 spaces (most IDEs will convert tab into 4 spaces by default).
* `line length`: maximum 88 chars.

```python
# Standard libs
import std_lib_1
import std_lib_2

# Third party libs
import third_party_lib_1
import third_party_lib_2

# Local lib
from local_lib import class_1, function_1


# After two blank lines
def top_level_function(args: int) -> str:
    # Body
```

### String quotes, whitespaces [MUST]

* `whitepace`: avoid extra white spaces, use single white spaces around both sides of an operator, one after comma and none inside opening or closing parenthesis.
* `quotes`: use double quotes. Single quotes can be used for strings if for instance you need to encapsulate json (which contains double quotes). But the rule is double quotes everywhere else.

```python
x = "Hello world"
y = 15

print(x)
```

### Naming convention [MUST]

* `language, spelling`: Class name, function names and variables are written in English. Use grammatically correct names.
* `class name`: name should start with an uppercase and follow the camlCase convention if more than two words.
* `function name`:
  * lowercase, words separated by an underscore.
  * add `self` argument at first position if the method is a class's method.
  * if the function's name clashes with a reserved word, append underscore.
  * use 2 underscores at the beginning for private class's methods.
  * use 1 underscore at the beginning of a private field.
  * specify the type of your input parameters
  * always provide a return type (use 'None' if 'void').

```python
class CatalogInformation:
    def __init__(self, name: str) -> None:
        # Body constructor

    def get_metadata_count(self) -> int:
        # Body method
        return 1

    def __internal_check(self) -> bool:
        # Body private method
        return True
```

### Comments / documentation [MUST]

Use comments, provide explanation on complex algorithms.

For the documentation, use the [Sphinx style](https://www.sphinx-doc.org/en/master/) (official Python standard).

```python
def is_valid(a: int, b: str) -> bool:
    """Explain what the function does

    :param a: what a is
    :type a: int
    :param b: what b is
    :type b: str

    :rtype: bool
    :return: what is the output of the function
    """
```

### Tuples, Lists, Dictionaries [MUST]

Use tuples when data is non-changeable, dictionaries when you need to map things, and lists if your data can change later.

Functions can return multiple values, no need for a list:

```python
def get_info(self) -> str, int:
    """Get the info. No need to return a List/Dict

    :rtype: str, int
    :return: what the str and the int are
    """
    return "hello world", 30
```

### Use context managers [MUST]

Context managers are tool to use in situation where you need to run some code that has preconditions and postconditions.

For instance, when you read the content of a file, you need to ensure that you close the handle regardless of the success or the failure of the operation. With the `with` keyword you can achieve this:

```python
with open(filename) as fd:
    process_file(fd)

# Note, that parenthesis are supported in Python 3.10 for context manager,
# useful when you have many 'with'
with (
    CtxManager1() as example1,
    CtxManager2() as example2,
    CtxManager3() as example3,
):
    ...
```

You can implement your own context manager may you need to execute actions in a certain order. Consider the case where you want to update a service configuration. You need first to stop the service, update the configuration then start the service again:

```python
class ServiceHandler:
    def __enter__(self) -> ServiceHandler:
        run("systemctl stop my.service")
        return self

    def __exit__(self, exc_type: str, ex_value: str, ex_traceback: str) -> None:
        run("systemctl start my.service")

def update_service_conf() -> None:
    # Body to update service's configuration

if (__name__ == '__main__'):
    with ServiceHandler():
        update_service_conf()
```

### Use getters/setters for class attribute [SHOULD]

To add validation when getting or setting an attribute, let's use getter / setter decorators for class's attributes. This will also avoid accessing directly private class's fields.

```python
class BoxSize:
    def __init__(self, width: float, length: float) -> None:
        self._width = self._length = None
        self.width = width
        self.length = length

    @property
    def width(self) -> float:
        return self._width

    @width.setter
    def width(self, width_value: float) -> None:
        if (width_value < 0):
            raise ValueError(f"{width_value} is an invalid value for width")
        self._width = width_value

    @property
    def length(self) -> float:
        return self._length

    @length.setter
    def length(self, length_value: float) -> None:
        if (length_value < 0):
            raise ValueError(f"{length_value} is an invalid value for length")
        self._length = length_value

if (__name__ == '__main__'):
    boxSize = BoxSize(-32, 32)
    print(boxSize.width)
    boxSize.length = -45
    print(boxSize.length)
```

If a class is created to only host attributes (to describe something for instance), the class attribute `dataclass` can be used to simplify the writting:

```python
# Without the decorator
class Foo:
    def __init__(self, a: int, b: str, c: MyType):
        self.a = a
        self.b = b
        self.c = c

# could be rewritten as:
from dataclasses import dataclass


@dataclass
class Foo:
    a: int
    b: str
    c: MyType
```

### Exceptions handling [MUST]

Hiding an exception or not properly anticipating potential errors (accessing an API for instance, network issues *can* arise) can lead to unexpected behaviors. Although a class or a function can affect default values to input parameters, not ensuring their validity, or not notifying the caller could lead to wrong results and difficulties to debug.

Each function has a logic, this logic must be followed by exceptions raised. For instance, if you have a function that get some data from an API, exceptions raised by this function should be logical: connection error, timeout etc. The exception must be raised at the right level of abstraction.

If you choose to propagate the exception to the caller, ensure that you do not expose sensitive information. Tracebacks of exception can contain sensitive details leading to exposing intellectual property.

Do not use exceptions as a `go-to` logic, meaning catching an exception and from the `except` calling other business code, the flow of the program will become harder to read. Exceptions are *usually* to notify the caller that something unexpected occured.

Finally, [observability](https://microsoft.github.io/code-with-engineering-playbook/observability/) is an important engineering fundamental. Properly handling exceptions and managing observability (with AppInsight for instance) will lead to a more robust application and easy to debug when something not expected occurs.

```python
# Never do ...
try:
    process_data()
except:
    pass

# Encapsulate orginal exception trace
def process_data() -> None:
    try:
        do_something()
    except KeyError as e:
        # Raise a specific exception from do_something,
        # encapsulate trace to a custom exception
        raise MyApplicationException("Item not present") from e
```

### Dependency injection [SHOULD]

Dependency injection is a technique where an object receives other objects that it depends on (instead of creating this dependency inside the object that needs it). This approach has several advantages:

1. it helps managing complexity of codebase,
1. it helps testing and maintenance,
1. it is supported by many languages, including Python.

Consider the following example without dependency injection: the class `MyConverter` reads a json file from an Azure Blob storage using the [SDK](https://pypi.org/project/azure-storage-blob/) and converts it into another format:

```python
# This code is a snippet no everything is implemented (exceptions for instance)
from azure.storage.blob import BlobClient

# Class that reads a blob using the Azure SDK
class AzureBlobReader:
    def __init__(self, connection: str):
        self.connection = connection

    def get_json(self, container: str, blob: str) -> str:
        client = BlobClient.from_connection_string(
            conn_str=self.connection,
            container_name=container,
            blob_name=blob
        )

        blob_raw = client.download_blob()

        return json.loads(blob_raw.readall())

# Class responsible of converting the blob into another format
class MyConverter:
    def convert_payload(self) -> str:
        blob_reader = AzureBlobReader("connection_str")
        input_json = blob_reader.get_json("my_container", "my_blob")

        return self.transform(input_json)

if (__name__ == '__main__'):
    converter = MyConverter()
    output = converter.convert_payload()
```

The class `MyConverter` is tightly coupled to `AzureBlobReader`. Implementing dependency injection in the code above will result into:

```python
# This code is a snippet no everything is implemented (exceptions for instance)
from azure.storage.blob import BlobClient

# Class that reads a blob using the Azure SDK
class AzureBlobReader:
    def __init__(self, connection: str):
        self.connection = connection

    def get_json(self, container: str, blob: str) -> str:
        client = BlobClient.from_connection_string(
            conn_str=self.connection,
            container_name=container,
            blob_name=blob
        )

        blob_raw = client.download_blob()

        return json.loads(blob_raw.readall())

# Class responsible of converting the blob into another format
class MyConverter:
    def __init__(self, azure_blob_reader: AzureBlobReader):
        self.azure_blob_reader = azure_blob_reader

    def convert_payload(self) -> str:
        input_json = self.azure_blob_reader.get_json("my_container", "my_blob")

        return self.transform(input_json)

if (__name__ == '__main__'):
    converter = MyConverter(
        azure_blob_reader = AzureBlobReader("connection_str")
    )
    output = converter.convert_payload()
```

By decoupling these classes, code is easier to maintain and to read. It will also help building unit tests for the class `MyConverter` as we will be able to easily mock `AzureBlobReader`.

### Unit tests [MUST]

Unit testing is a core tool for developers. They help us test our code, encourage good design practices, and reduce chances to have bugs hitting production. Unit tests can improve developer efficiency.

Unit tests should be:

* Reliable: should be 100% reliable so failures indicate a bug in the code.
* Fast: should run in milliseconds.
* Isolated: removing all external dependencies ensures reliability and speed (Dependency injection for instance) for details).

Without entering into too many details, `pytest` provides a set of features helpful. Below some examples:

```python
# Execute this test 3 times, with a, b, and c as input_value
@pytest.mark.parametrize("input_value", ["a", "b", "c"])
def test_(
    input_value
):
    # Test body

# Define a fixed baseline, executed when needed
@pytest.fixture
def items() -> List[str]:
    return ["item 1", "item 2"]

# 'items' will be execute, hence, will be a List[str] with 2 items
def test_items_count(items):
    assert len(items) == 2

```

Please refer to [pytest site](https://docs.pytest.org/en/6.2.x/reference.html) for more useful patterns.
