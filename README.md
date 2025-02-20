# 乾淨程式碼-python

[![Build Status](https://travis-ci.com/zedr/clean-code-python.svg?branch=master)](https://travis-ci.com/zedr/clean-code-python)
[![](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/download/releases/3.8.3/)

## 內容列表
  1. [Introduction 前言](#introduction-前言)
  2. [Variables 變數](#variables-變數)
  3. [Functions 函式](#functions-函式)
  5. [Classes 類別](#classes-類別)
     1. [S: Single Responsibility Principle (SRP) 單一功能原則](#single-responsibility-principle-srp-單一功能原則)
     2. [O: Open/Closed Principle (OCP) 開閉原則](#openclosed-principle-ocp-開閉原則)
     3. [L: Liskov Substitution Principle (LSP) 里氏替換原則](#liskov-substitution-principle-lsp-里氏替換原則)
     4. [I: Interface Segregation Principle (ISP) 介面隔離原則](#interface-segregation-principle-isp-介面隔離原則)
     5. [D: Dependency Inversion Principle (DIP) 依賴反轉原則](#dependency-inversion-principle-dip-依賴反轉原則)
  6. [Don't repeat yourself (DRY) 別再重複做一次](#dont-repeat-yourself-dry-別再重複做一次)
  7. [Translation 翻譯](#translations-翻譯)

## Introduction 前言

軟體工程原理，來自Robert C. Martin的《
[*Clean Code 乾淨原始碼*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)》書中,
且適用於Python。這並不是一個風格指南。這是一個指南用於幫助你產出更易讀、更易用，和更易重構的Python軟體。

並非每一個原理都必需嚴格遵守，甚至部分的原理並不是那麼普遍地被大家認同。這只是指南而已，但他們都由《*Clean Code 乾淨程式碼*》的作者
多年來的經驗搜集並編撰而成。

啟發自 [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)

使用 Python 3.7+版本

## **Variables 變數**
### 使用有意義且可發音的變數名稱
**不恰當:**

```python
import datetime


ymdstr = datetime.date.today().strftime("%y-%m-%d")
```

此外，變數名稱沒必要加入它自己本身的型態名稱(str)

**恰當**:

```python
import datetime


current_date: str = datetime.date.today().strftime("%y-%m-%d")
```
**[⬆ 返回頂部](#內容列表)**

### 對於相同類型的變數，使用相同單字

**不恰當:**
這是我們對於相同意思的實體，所使用的三種不同單字名稱

```python
def get_user_info(): pass
def get_client_data(): pass
def get_customer_record(): pass
```

**恰當**:

如果是相同的實體，你應該在函式裡持續地使用相同的名稱

```python
def get_user_info(): pass
def get_user_data(): pass
def get_user_record(): pass
```

**這樣更好**
Python同時也是個物件導向程式語言。如果這樣做是合乎情理，可以將這些函式(functions)和實體實作的程式碼打包在一起，例如：實體屬性、屬性方法(property methods)，或是方法：

```python
from typing import Union, Dict


class Record:
    pass


class User:
    info : str

    @property
    def data(self) -> Dict[str, str]:
        return {}

    def get_record(self) -> Union[Record, None]:
        return Record()
```

**[⬆ 返回頂部](#內容列表)**

### 使用易查詢的名字
我們讀到的程式碼將會比此生寫過的還要更多，寫出易讀且易查詢的程式碼是非常重要的。
如果*不命名*變數，會使想瞭解我們專案的讀者感到毫無意義，會傷害到我們的讀者。
讓你的變數名字更易於查詢。

**不恰當:**

```python
import time


# What is the number 86400 for again?
time.sleep(86400)
```

**恰當**:

```python
import time


# Declare them in the global namespace for the module.
SECONDS_IN_A_DAY = 60 * 60 * 24
time.sleep(SECONDS_IN_A_DAY)
```
**[⬆ 返回頂部](#內容列表)**

### 使用帶有解釋性意義的變數
**不恰當:**

```python
import re


address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$"

matches = re.match(city_zip_code_regex, address)
if matches:
    print(f"{matches[1]}: {matches[2]}")
```

**還不錯**:

這算是還不錯，但我們在regex上仍然有很重的依賴性

```python
import re


address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$"
matches = re.match(city_zip_code_regex, address)

if matches:
    city, zip_code = matches.groups()
    print(f"{city}: {zip_code}")
```

**恰當**:

將小型的行為模式透過命名方式降低整體依賴性

```python
import re


address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(?P<city>.+?)\s*(?P<zip_code>\d{5})?$"

matches = re.match(city_zip_code_regex, address)
if matches:
    print(f"{matches['city']}, {matches['zip_code']}")
```
**[⬆ 返回頂部](#內容列表)**

### 避免心理地圖
不要強迫閱讀你的程式碼的人自行去解讀變數名稱的意思
明確表達永遠比含糊的好很多

**不恰當:**

```python
seq = ("Austin", "New York", "San Francisco")

for item in seq:
    #do_stuff()
    #do_some_other_stuff()

    # Wait, what's `item` again?
    print(item)
```

**恰當**:

```python
locations = ("Austin", "New York", "San Francisco")

for location in locations:
    #do_stuff()
    #do_some_other_stuff()
    # ...
    print(location)
```
**[⬆ 返回頂部](#內容列表)**

### 別加入不需要的內容
如果你的類別(class)或物件(object)已經表示什麼含意，就別在你的變數名稱中重複表達相同含義

**不恰當:**

```python
class Car:
    car_make: str
    car_model: str
    car_color: str
```

**恰當**:

```python
class Car:
    make: str
    model: str
    color: str
```

**[⬆ 返回頂部](#內容列表)**

### 使用預設的參數(arguments)，別用簡短的邏輯(circuiting)表達或條件(conditionals)表達

**小技巧**

如何寫:

```python
import hashlib


def create_micro_brewery(name):
    name = "Hipster Brew Co." if name is None else name
    slug = hashlib.sha1(name.encode()).hexdigest()
    # etc.
```

... 什麼時機你可以使用預設參數(default argument)代替? 當你期望將一個字串(string)視為參數(argument)時，
這會使整體意思更清晰。

**恰當**:

```python
import hashlib


def create_micro_brewery(name: str = "Hipster Brew Co."):
    slug = hashlib.sha1(name.encode()).hexdigest()
    # etc.
```

**[⬆ 返回頂部](#內容列表)**

## **Functions 函式**
### 函式參數(2個或更少較為理想)
限制函數的參數數量是極為重要的，因為它會使你測試自己的函式更為方便。
使用三個以上參數的爆炸組合會讓你必須測試一大堆不同的測試案例與不同參數。

最佳的案例是沒有參數。一個或兩個參數算是還可以，然後三個參數就應該避免。
除此之外，如果再更多的參數就應該合併起來。通常，如果你使用三個以上的參數，
代表你的函式嘗試做太多事情了。在沒有其他替代方案情況下，大多數使用一個高層級的物件(higher-level object)
作為參數就足以滿足情況。

**不恰當:**

```python
def create_menu(title, body, button_text, cancellable):
    pass
```

**Java-風格**:

```python
class Menu:
    def __init__(self, config: dict):
        self.title = config["title"]
        self.body = config["body"]
        # ...

menu = Menu(
    {
        "title": "My Menu",
        "body": "Something about my menu",
        "button_text": "OK",
        "cancellable": False
    }
)
```

**這也不錯**

```python
class MenuConfig:
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: str
    body: str
    button_text: str
    cancellable: bool = False


def create_menu(config: MenuConfig) -> None:
    title = config.title
    body = config.body
    # ...


config = MenuConfig()
config.title = "My delicious menu"
config.body = "A description of the various items on the menu"
config.button_text = "Order now!"
# The instance attribute overrides the default class attribute.
config.cancellable = True

create_menu(config)
```

**優等**

```python
from typing import NamedTuple


class MenuConfig(NamedTuple):
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: str
    body: str
    button_text: str
    cancellable: bool = False


def create_menu(config: MenuConfig):
    title, body, button_text, cancellable = config
    # ...


create_menu(
    MenuConfig(
        title="My delicious menu",
        body="A description of the various items on the menu",
        button_text="Order now!"
    )
)
```

**更高級**

```python
from dataclasses import astuple, dataclass


@dataclass
class MenuConfig:
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: str
    body: str
    button_text: str
    cancellable: bool = False

def create_menu(config: MenuConfig):
    title, body, button_text, cancellable = astuple(config)
    # ...


create_menu(
    MenuConfig(
        title="My delicious menu",
        body="A description of the various items on the menu",
        button_text="Order now!"
    )
)
```

**超高級用法，限定 Python3.8+ **

```python
from typing import TypedDict


class MenuConfig(TypedDict):
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: str
    body: str
    button_text: str
    cancellable: bool


def create_menu(config: MenuConfig):
    title = config["title"]
    # ...


create_menu(
    # You need to supply all the parameters
    MenuConfig(
        title="My delicious menu",
        body="A description of the various items on the menu",
        button_text="Order now!",
        cancellable=True
    )
)
```
**[⬆ 返回頂部](#內容列表)**

### Functions should do one thing
This is by far the most important rule in software engineering. When functions do more
than one thing, they are harder to compose, test, and reason about. When you can isolate
a function to just one action, they can be refactored easily and your code will read much
cleaner. If you take nothing else away from this guide other than this, you'll be ahead
of many developers.

**Bad:**

```python
from typing import List


class Client:
    active: bool


def email(client: Client) -> None:
    pass


def email_clients(clients: List[Client]) -> None:
    """Filter active clients and send them an email.
    """
    for client in clients:
        if client.active:
            email(client)
```

**Good**:

```python
from typing import List


class Client:
    active: bool


def email(client: Client) -> None:
    pass


def get_active_clients(clients: List[Client]) -> List[Client]:
    """Filter active clients.
    """
    return [client for client in clients if client.active]


def email_clients(clients: List[Client]) -> None:
    """Send an email to a given list of clients.
    """
    for client in get_active_clients(clients):
        email(client)
```

Do you see an opportunity for using generators now?

**Even better**

```python
from typing import Generator, Iterator


class Client:
    active: bool


def email(client: Client):
    pass


def active_clients(clients: Iterator[Client]) -> Generator[Client, None, None]:
    """Only active clients"""
    return (client for client in clients if client.active)


def email_client(clients: Iterator[Client]) -> None:
    """Send an email to a given list of clients.
    """
    for client in active_clients(clients):
        email(client)
```


**[⬆ back to top](#table-of-contents)**

### Function names should say what they do

**Bad:**

```python
class Email:
    def handle(self) -> None:
        pass

message = Email()
# What is this supposed to do again?
message.handle()
```

**Good:**

```python
class Email:
    def send(self) -> None:
        """Send this message"""

message = Email()
message.send()
```

**[⬆ back to top](#table-of-contents)**

### Functions should only be one level of abstraction

When you have more than one level of abstraction, your function is usually doing too
much. Splitting up functions leads to reusability and easier testing.

**Bad:**

```python
# type: ignore

def parse_better_js_alternative(code: str) -> None:
    regexes = [
        # ...
    ]

    statements = code.split('\n')
    tokens = []
    for regex in regexes:
        for statement in statements:
            pass

    ast = []
    for token in tokens:
        pass

    for node in ast:
        pass
```

**Good:**

```python
from typing import Tuple, List, Dict


REGEXES: Tuple = (
   # ...
)


def parse_better_js_alternative(code: str) -> None:
    tokens: List = tokenize(code)
    syntax_tree: List = parse(tokens)

    for node in syntax_tree:
        pass


def tokenize(code: str) -> List:
    statements = code.split()
    tokens: List[Dict] = []
    for regex in REGEXES:
        for statement in statements:
            pass

    return tokens


def parse(tokens: List) -> List:
    syntax_tree: List[Dict] = []
    for token in tokens:
        pass

    return syntax_tree
```

**[⬆ back to top](#table-of-contents)**

### Don't use flags as function parameters

Flags tell your user that this function does more than one thing. Functions
should do one thing. Split your functions if they are following different code
paths based on a boolean.

**Bad:**

```python
from tempfile import gettempdir
from pathlib import Path


def create_file(name: str, temp: bool) -> None:
    if temp:
        (Path(gettempdir()) / name).touch()
    else:
        Path(name).touch()
```

**Good:**

```python
from tempfile import gettempdir
from pathlib import Path


def create_file(name: str) -> None:
    Path(name).touch()


def create_temp_file(name: str) -> None:
    (Path(gettempdir()) / name).touch()
```

**[⬆ back to top](#table-of-contents)**

### Avoid side effects

A function produces a side effect if it does anything other than take a value in
and return another value or values. For example, a side effect could be writing
to a file, modifying some global variable, or accidentally wiring all your money
to a stranger.

Now, you do need to have side effects in a program on occasion - for example, like
in the previous example, you might need to write to a file. In these cases, you
should centralize and indicate where you are incorporating side effects. Don't have
several functions and classes that write to a particular file - rather, have one
(and only one) service that does it.

The main point is to avoid common pitfalls like sharing state between objects
without any structure, using mutable data types that can be written to by anything,
or using an instance of a class, and not centralizing where your side effects occur.
If you can do this, you will be happier than the vast majority of other programmers.

**Bad:**

```python
# type: ignore

# This is a module-level name.
# It's good practice to define these as immutable values, such as a string.
# However...
fullname = "Ryan McDermott"

def split_into_first_and_last_name() -> None:
    # The use of the global keyword here is changing the meaning of the
    # the following line. This function is now mutating the module-level
    # state and introducing a side-effect!
    global fullname
    fullname = fullname.split()

split_into_first_and_last_name()

# MyPy will spot the problem, complaining about 'Incompatible types in
# assignment: (expression has type "List[str]", variable has type "str")'
print(fullname)  # ["Ryan", "McDermott"]

# OK. It worked the first time, but what will happen if we call the
# function again?
```

**Good:**

```python
from typing import List, AnyStr


def split_into_first_and_last_name(name: AnyStr) -> List[AnyStr]:
    return name.split()

fullname = "Ryan McDermott"
name, surname = split_into_first_and_last_name(fullname)

print(name, surname)  # => Ryan McDermott
```

**Also good**

```python
from dataclasses import dataclass


@dataclass
class Person:
    name: str

    @property
    def name_as_first_and_last(self) -> list:
        return self.name.split()


# The reason why we create instances of classes is to manage state!
person = Person("Ryan McDermott")
print(person.name)  # => "Ryan McDermott"
print(person.name_as_first_and_last)  # => ["Ryan", "McDermott"]
```

**[⬆ back to top](#table-of-contents)**

## **Classes 類別**

### **Single Responsibility Principle (SRP) 單一功能原則**

Robert C. Martin writes:

> A class should have only one reason to change.

"Reasons to change" are, in essence, the responsibilities managed by a class or
function.

In the following example, we create an HTML element that represents a comment with
the version of the document:

**Bad**
```python
from importlib import metadata


class VersionCommentElement:
   """An element that renders an HTML comment with the program's version number
   """

   def get_version(self) -> str:
       """Get the package version"""
       return metadata.version("pip")
   
   def render(self) -> None:
       print(f'<!-- Version: {self.get_version()} -->')


VersionCommentElement().render()
```
This class has two responsibilities:

 - Retrieve the version number of the Python package
 - Render itself as an HTML element

Any change to one or the other carries the risk of impacting the other.

We can rewrite the class and decouple these responsibilities: 

**Good**
```python
from importlib import metadata


def get_version(pkg_name:str) -> str:
    """Retrieve the version of a given package"""
    return metadata.version(pkg_name)


class VersionCommentElement:
   """An element that renders an HTML comment with the program's version number
   """

   def __init__(self, version: str):
       self.version = version
   
   def render(self) -> None:
       print(f'<!-- Version: {self.version} -->')


VersionCommentElement(get_version("pip")).render()
```

The result is that the class only needs to take care of rendering itself. It
receives the version text during instantiation and this text is generated by
calling a separate function, `get_version()`. Changing the class has no
impact on the other, and vice-versa, as long as the contract between them does
not change, i.e. the function provides a string and the class `__init__` method
accepts a string.

As an added bonus, the `get_version()` is now reusable elsewhere.

### **Open/Closed Principle (OCP) 開閉原則**

> “Incorporate new features  by extending the system, not by making
> modifications (to it)”,
> Uncle Bob.

Objects should be open for extension, but closed to modification. It should be
possible to augment the functionality provided by an object (for example, a class)
without changing its internal contracts. An object can enable this when it
is designed to be extended cleanly.

In the following example, we try to implement a simple web framework that
handles HTTP requests and returns responses. The `View` class has a single
method `.get()` that will be called when the HTTP server will receive a GET
request from a client.

`View` is intentionally simple and returns `text/plain` responses. We would
also like to return HTML responses based on a template file, so we subclass it
using the `TemplateView` class.

**Bad**
```python
from dataclasses import dataclass


@dataclass
class Response:
    """An HTTP response"""

    status: int
    content_type: str
    body: str


class View:
    """A simple view that returns plain text responses"""

    def get(self, request) -> Response:
        """Handle a GET request and return a message in the response"""
        return Response(
            status=200,
            content_type='text/plain',
            body="Welcome to my web site"
        )


class TemplateView(View):
    """A view that returns HTML responses based on a template file."""

    def get(self, request) -> Response:
        """Handle a GET request and return an HTML document in the response"""
        with open("index.html") as fd:
            return Response(
                status=200,
                content_type='text/html',
                body=fd.read()
            )

```

The `TemplateView` class has modified the internal behaviour of its parent
class in order to enable the more advanced functionality. In doing so,
it now relies on the `View` to not change the implementation of the `.get()`
method, which now needs to be frozen in time. We cannot introduce, for example,
some additional checks in all our `View`-derived classes because the behaviour
is overridden in at least one subtype and we will need to update it.

Let's redesign our classes to fix this problem and let the `View` class be
extended (not modified) cleanly:

**Good**
```python
from dataclasses import dataclass


@dataclass
class Response:
    """An HTTP response"""

    status: int
    content_type: str
    body: str


class View:
    """A simple view that returns plain text responses"""

    content_type = "text/plain"

    def render_body(self) -> str:
        """Render the message body of the response"""
        return "Welcome to my web site"

    def get(self, request) -> Response:
        """Handle a GET request and return a message in the response"""
        return Response(
            status=200,
            content_type=self.content_type,
            body=self.render_body()
        )


class TemplateView(View):
    """A view that returns HTML responses based on a template file."""

    content_type = "text/html"
    template_file = "index.html"

    def render_body(self) -> str:
        """Render the message body as HTML"""
        with open(self.template_file) as fd:
            return fd.read()


```

Note that we did need to override the `render_body()` in order to change the
source of the body, but this method has a single, well defined responsibility
that **invites subtypes to override it**. It is designed to be extended by its
subtypes.

Another good way to use the strengths of both object inheritance and object
composition is to use [Mixins](https://docs.djangoproject.com/en/4.1/topics/class-based-views/mixins/).

Mixins are bare-bones classes that are meant to be used exclusively with other
related classes. They are "mixed-in" with the target class using multiple
inheritance, in order to change the target's behaviour.

A few rules:
 - Mixins should always inherit from `object`
 - Mixins always come before the target class, e.g. `class Foo(MixinA, MixinB, TargetClass): ...`

**Also good**
```python
from dataclasses import dataclass, field
from typing import Protocol


@dataclass
class Response:
    """An HTTP response"""

    status: int
    content_type: str
    body: str
    headers: dict = field(default_factory=dict)


class View:
    """A simple view that returns plain text responses"""

    content_type = "text/plain"

    def render_body(self) -> str:
        """Render the message body of the response"""
        return "Welcome to my web site"

    def get(self, request) -> Response:
        """Handle a GET request and return a message in the response"""
        return Response(
            status=200,
            content_type=self.content_type,
            body=self.render_body()
        )


class TemplateRenderMixin:
    """A mixin class for views that render HTML documents using a template file

    Not to be used by itself!
    """
    template_file: str = ""

    def render_body(self) -> str:
        """Render the message body as HTML"""
        if not self.template_file:
            raise ValueError("The path to a template file must be given.")

        with open(self.template_file) as fd:
            return fd.read()


class ContentLengthMixin:
    """A mixin class for views that injects a Content-Length header in the
    response

    Not to be used by itself!
    """

    def get(self, request) -> Response:
        """Introspect and amend the response to inject the new header"""
        response = super().get(request) # type: ignore
        response.headers['Content-Length'] = len(response.body)
        return response


class TemplateView(TemplateRenderMixin, ContentLengthMixin, View):
    """A view that returns HTML responses based on a template file."""

    content_type = "text/html"
    template_file = "index.html"

```

As you can see, Mixins make object composition easier by packaging together
related functionality into a highly reusable class with a single responsibility,
allowing clean decoupling. Class extension is achieved by "mixing-in" the
additional classes.

The popular Django project makes heavy use of Mixins to compose its class-based
views.

FIXME: re-enable typechecking for the line above once it's clear how to use
`typing.Protocol` to make the type checker work with Mixins.

### **Liskov Substitution Principle (LSP) 里氏替換原則**

> “Functions that use pointers or references to base classes
> must be able to use objects of derived classes without knowing it”,
> Uncle Bob.

This principle is named after Barbara Liskov, who collaborated with fellow
computer scientist Jeannette Wing on the seminal paper
*"A behavioral notion of subtyping" (1994). A core tenet of the paper is that
"a subtype (must) preserve the behaviour of the supertype methods and also all
invariant and history properties of its supertype".

In essence, a function accepting a supertype should also accept all its subtypes
with no modification.

Can you spot the problem with the following code?

**Bad**
```python
from dataclasses import dataclass


@dataclass
class Response:
    """An HTTP response"""

    status: int
    content_type: str
    body: str


class View:
    """A simple view that returns plain text responses"""

    content_type = "text/plain"

    def render_body(self) -> str:
        """Render the message body of the response"""
        return "Welcome to my web site"

    def get(self, request) -> Response:
        """Handle a GET request and return a message in the response"""
        return Response(
            status=200,
            content_type=self.content_type,
            body=self.render_body()
        )


class TemplateView(View):
    """A view that returns HTML responses based on a template file."""

    content_type = "text/html"

    def get(self, request, template_file: str) -> Response: # type: ignore
        """Render the message body as HTML"""
        with open(template_file) as fd:
            return Response(
                status=200,
                content_type=self.content_type,
                body=fd.read()
            )


def render(view: View, request) -> Response:
    """Render a View"""
    return view.get(request)

```

The expectation is that `render()` function will be able to work with `View` and its
subtype `TemplateView`, but the latter has broken compatibility by modifying
the signature of the `.get()` method. The function will raise a `TypeError`
exception when used with `TemplateView`.

If we want the `render()` function to work with any subtype of `View`, we
must pay attention not to break its public-facing protocol. But how do we know
what constitutes it for a given class? Type hinters like *mypy* will raise
an error when it detects mistakes like this:

```
error: Signature of "get" incompatible with supertype "View"
<string>:36: note:      Superclass:
<string>:36: note:          def get(self, request: Any) -> Response
<string>:36: note:      Subclass:
<string>:36: note:          def get(self, request: Any, template_file: str) -> Response
```

### **Interface Segregation Principle (ISP) 介面隔離原則**

> “Keep interfaces small
> so that users don’t end up depending on things they don’t need.”,
> Uncle Bob.

Several well known object oriented programming languages, like Java and Go,
have a concept called interfaces. An interface defines the public methods and
properties of an object without implementing them. They are useful when we don't
want to couple the signature of a function to a concrete object; we'd rather
say "I don't care what object you give me, as long as it has certain methods
and attributes I expect to make use of".

Python does not have interfaces. We have Abstract Base Classes instead, which
are a little different, but can serve the same purpose.

**Good**
```python

from abc import ABCMeta, abstractmethod


# Define the Abstract Class for a generic Greeter object
class Greeter(metaclass=ABCMeta):
    """An object that can perform a greeting action."""

    @staticmethod
    @abstractmethod
    def greet(name: str) -> None:
        """Display a greeting for the user with the given name"""


class FriendlyActor(Greeter):
    """An actor that greets the user with a friendly salutation"""

    @staticmethod
    def greet(name: str) -> None:
        """Greet a person by name"""
        print(f"Hello {name}!")


def welcome_user(user_name: str, actor: Greeter):
    """Welcome a user with a given name using the provided actor"""
    actor.greet(user_name)


welcome_user("Barbara", FriendlyActor())
```

Now imagine the following scenario: we have a certain number of PDF documents
that we author and want to serve to our web site visitors. We are using a
Python web framework and we might be tempted to design a class to manage these
documents, so we go ahead and design a comprehensive abstract base class for
our document.

**Error**
```python
import abc


class Persistable(metaclass=abc.ABCMeta):
    """Serialize a file to data and back"""

    @property
    @abc.abstractmethod
    def data(self) -> bytes:
        """The raw data of the file"""

    @classmethod
    @abc.abstractmethod
    def load(cls, name: str):
        """Load the file from disk"""

    @abc.abstractmethod
    def save(self) -> None:
        """Save the file to disk"""


# We just want to serve the documents, so our concrete PDF document
# implementation just needs to implement the `.load()` method and have
# a public attribute named `data`.

class PDFDocument(Persistable):
    """A PDF document"""

    @property
    def data(self) -> bytes:
        """The raw bytes of the PDF document"""
        ... # Code goes here - omitted for brevity

    @classmethod
    def load(cls, name: str):
        """Load the file from the local filesystem"""
        ... # Code goes here - omitted for brevity


def view(request):
    """A web view that handles a GET request for a document"""
    requested_name = request.qs['name'] # We want to validate this!
    return PDFDocument.load(requested_name).data

```

But we can't! If we don't implement the `.save()` method, an exception will be
raised:

```
Can't instantiate abstract class PDFDocument with abstract method save.
```

That's annoying. We don't really need to implement `.save()` here. We could
implement a dummy method that does nothing or raises `NotImplementedError`,
but that's useless code that we will need to maintain.

At the same time, if we remove `.save()` from the abstract class now we will
need to add it back when we will later implement a way for users to submit
their documents, bringing us back to the same situation as before.

The problem is that we have written an *interface* that has features we don't
need right now as we are not using them.

The solution is to decompose the interface into smaller and composable interfaces
that segregate each feature.

**Good**
```python
import abc


class DataCarrier(metaclass=abc.ABCMeta):
    """Carries a data payload"""
    @property
    def data(self):
        ...

class Loadable(DataCarrier):
    """Can load data from storage by name"""
    @classmethod
    @abc.abstractmethod
    def load(cls, name: str):
        ...

class Saveable(DataCarrier):
    """Can save data to storage"""
    @abc.abstractmethod
    def save(self) -> None:
        ...


class PDFDocument(Loadable):
    """A PDF document"""

    @property
    def data(self) -> bytes:
        """The raw bytes of the PDF document"""
        ... # Code goes here - omitted for brevity

    @classmethod
    def load(cls, name: str):
        """Load the file from the local filesystem"""
        ... # Code goes here - omitted for brevity


def view(request):
    """A web view that handles a GET request for a document"""
    requested_name = request.qs['name'] # We want to validate this!
    return PDFDocument.load(requested_name).data

```

### **Dependency Inversion Principle (DIP) 依賴反轉原則**

> “Depend upon abstractions, not concrete details”,
> Uncle Bob.

Imagine we wanted to write a web view that returns an HTTP response that
streams rows of a CSV file we create on the fly. We want to use the CSV writer
that is provided by the standard library.

**Bad**
```python
import csv
from io import StringIO


class StreamingHttpResponse:
    """A streaming HTTP response"""
    ... # implementation code goes here


def some_view(request):
   rows = (
       ['First row', 'Foo', 'Bar', 'Baz'],
       ['Second row', 'A', 'B', 'C', '"Testing"', "Here's a quote"]
   )
   # Define a generator to stream data directly to the client
   def stream():
       buffer_ = StringIO()
       writer = csv.writer(buffer_, delimiter=';', quotechar='"')
       for row in rows:
           writer.writerow(row)
           buffer_.seek(0)
           data = buffer_.read()
           buffer_.seek(0)
           buffer_.truncate()
           yield data

   # Create the streaming response  object with the appropriate CSV header.
   response = StreamingHttpResponse(stream(), content_type='text/csv')
   response['Content-Disposition'] = 'attachment; filename="somefilename.csv"'

   return response

```

Our first implementation works around the CSV's writer interface by manipulating
a `StringIO` object (which is file-like) and performing several low level
operations in order to farm out the rows from the writer. It's a lot of work
and not very elegant.

A better way is to leverage the fact that the writer just needs an object with
a `.write()` method to do our bidding. Why not pass it a dummy object that
immediately returns the newly assembled row, so that the `StreamingHttpResponse`
class can immediate stream it back to the client?

**Good**
```python
import csv


class Echo:
   """An object that implements just the write method of the file-like
   interface.
   """
   def write(self, value):
       """Write the value by returning it, instead of storing in a buffer."""
       return value

def some_streaming_csv_view(request):
   """A view that streams a large CSV file."""
   rows = (
       ['First row', 'Foo', 'Bar', 'Baz'],
       ['Second row', 'A', 'B', 'C', '"Testing"', "Here's a quote"]
   )
   writer = csv.writer(Echo(), delimiter=';', quotechar='"')
   return StreamingHttpResponse(
       (writer.writerow(row) for row in rows),
       content_type="text/csv",
       headers={'Content-Disposition': 'attachment; filename="somefilename.csv"'},
   )

```

Much better, and it works like a charm! The reason it's superior to the previous
implementation should be obvious: less code (and more performant) to achieve
the same result. We decided to leverage the fact that the writer class depends
on the `.write()` abstraction of the object it receives, without caring about
the low level, concrete details of what the method actually does.

This example was taken from
[a submission made to the Django documentation](https://code.djangoproject.com/ticket/21179)
by this author.

**[⬆ back to top](#table-of-contents)**

## **Don't repeat yourself (DRY) 別再重複做一次**

Try to observe the [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) principle.

Do your absolute best to avoid duplicate code. Duplicate code is bad because
it means that there's more than one place to alter something if you need to
change some logic.

Imagine if you run a restaurant and you keep track of your inventory: all your
tomatoes, onions, garlic, spices, etc. If you have multiple lists that
you keep this on, then all have to be updated when you serve a dish with
tomatoes in them. If you only have one list, there's only one place to update!

Often you have duplicate code because you have two or more slightly
different things, that share a lot in common, but their differences force you
to have two or more separate functions that do much of the same things. Removing
duplicate code means creating an abstraction that can handle this set of different
things with just one function/module/class.

Getting the abstraction right is critical. Bad abstractions can be
worse than duplicate code, so be careful! Having said this, if you can make
a good abstraction, do it! Don't repeat yourself, otherwise you'll find yourself
updating multiple places any time you want to change one thing.

**Bad:**

```python
from typing import List, Dict
from dataclasses import dataclass

@dataclass
class Developer:
    def __init__(self, experience: float, github_link: str) -> None:
        self._experience = experience
        self._github_link = github_link
        
    @property
    def experience(self) -> float:
        return self._experience
    
    @property
    def github_link(self) -> str:
        return self._github_link
    
@dataclass
class Manager:
    def __init__(self, experience: float, github_link: str) -> None:
        self._experience = experience
        self._github_link = github_link
        
    @property
    def experience(self) -> float:
        return self._experience
    
    @property
    def github_link(self) -> str:
        return self._github_link
    

def get_developer_list(developers: List[Developer]) -> List[Dict]:
    developers_list = []
    for developer in developers:
        developers_list.append({
        'experience' : developer.experience,
        'github_link' : developer.github_link
            })
    return developers_list

def get_manager_list(managers: List[Manager]) -> List[Dict]:
    managers_list = []
    for manager in managers:
        managers_list.append({
        'experience' : manager.experience,
        'github_link' : manager.github_link
            })
    return managers_list

## create list objects of developers
company_developers = [
    Developer(experience=2.5, github_link='https://github.com/1'),
    Developer(experience=1.5, github_link='https://github.com/2')
]
company_developers_list = get_developer_list(developers=company_developers)

## create list objects of managers
company_managers = [
    Manager(experience=4.5, github_link='https://github.com/3'),
    Manager(experience=5.7, github_link='https://github.com/4')
]
company_managers_list = get_manager_list(managers=company_managers)
```

**Good:**

```python
from typing import List, Dict
from dataclasses import dataclass

@dataclass
class Employee:
    def __init__(self, experience: float, github_link: str) -> None:
        self._experience = experience
        self._github_link = github_link
        
    @property
    def experience(self) -> float:
        return self._experience
    
    @property
    def github_link(self) -> str:
        return self._github_link
    


def get_employee_list(employees: List[Employee]) -> List[Dict]:
    employees_list = []
    for employee in employees:
        employees_list.append({
        'experience' : employee.experience,
        'github_link' : employee.github_link
            })
    return employees_list

## create list objects of developers
company_developers = [
    Employee(experience=2.5, github_link='https://github.com/1'),
    Employee(experience=1.5, github_link='https://github.com/2')
]
company_developers_list = get_employee_list(employees=company_developers)

## create list objects of managers
company_managers = [
    Employee(experience=4.5, github_link='https://github.com/3'),
    Employee(experience=5.7, github_link='https://github.com/4')
]
company_managers_list = get_employee_list(employees=company_managers)
```



**[⬆ back to top](#table-of-contents)**

## **Translations 翻譯**

This document is also available in other languages:

- :pt: :br: **Portugese** [fredsonchaves07/clean-code-python](https://github.com/fredsonchaves07/clean-code-python)
- :iran: **Persian:** [https://github.com/SepehrRasouli/clean-code-python](https://github.com/SepehrRasouli/clean-code-python)
- :taiwan: **Traditional Chinese 繁體中文:** [https://github.com/as209099/clean-code-python](https://github.com/as209099/clean-code-python)

**[⬆ back to top](#table-of-contents)**
