# Annotations

## Examples

```python
from typing import List

def main(args: List[str]) -> None:
    a = 42  # type: int
    a_py36: int = 42
    b = "hello"  # type: str
    b_py36: str = "hello"

# Py 3.6
class Foo:
    def foo(self) -> 'Foo':
        return self

# Py 3.7
from __future__ import annotations
class Foo:
    def foo(self) -> Foo:
        return self 
```

## Overload

```python
from typing import overload

@overload
def hello(s: int) -> int:
    ...
 
@overload
def hello(s: str) -> str:
    ...
 
def hello(s):
    if isinstance(s, int):
        return "Got an integer!"
    if isinstance(s, str):
        return "Got a string"
    raise ValueError('You must pass either int or str')

if __name__ == '__main__':
    print(hello(1))
    a = hello(1) + 1
    b = hello(1) + 'a'
```

## typing.Type

```python
from typing import Type, TypeVar

class BaseObject: pass
class Product(BaseObject): pass

def make_object(obj_class: Type[BaseObject]) -> BaseObject:
    return obj_class()

o1: BaseObject = make_object(BaseObject)
o2: Product = make_object(Product) # mypy: Incompatible types in

assignment (expression has type "BaseObject", variable has type "Product")

T = TypeVar('T', bound=BaseObject)

def make_object2(obj_class: Type[T]) -> T:
 return obj_class()

o3: BaseObject = make_object2(BaseObject)
o4: Product = make_object2(Product)
```


## typing.TypeVar
```python
from typing import TypeVar, List, Union

T = TypeVar("T")  # Любой тип
A = TypeVar("A", str, bytes)  # str или bytes

def repeat(x: T, n: int) -> List[T]:
    return [x] * n

def longest(x: A, y: A) -> A:
    return x if len(x) >= len(y) else y


def parse_int(value: Union[str, int]) -> int:
    return int(value)
```

## typing.Any

+ Отключает проверку типов
  * Любой тип совместим с Any
  * Any совместим с любым типом
+ Если тип не указан - то это Any
+ В качестве альтернативы - object
  * Любой тип совместим с object
  * object несовместим с другими типами

```python
from typing import Any

a: Any = None
a = []  # OK
a = 2  # OK
s: str = ""
s = a  # OK


def foo(item: Any) -> int:
    # Typechecks; 'item' может быть любого типа
    # и поэтому допускается обращение к .bar()
    item.bar()
    return 42
```
```python
def hash_a(item: object) -> int:
    # Fails; an object does not have a 'magic' method.
    item.magic()
    ...


def hash_b(item: Any) -> int:
    # Typechecks
    item.magic()
    ...


# Typechecks, int и str наследники object
hash_a(42)
hash_a("foo")
# Typechecks, любой тип совместим с Any
hash_b(42)
hash_b("foo")
```


## mypy & Gitlab CI

```yml
image: python:3.7

test:
 before_script:
 - python -V
 - pip install -U mypy
 script:
 - mypy main.py
```
