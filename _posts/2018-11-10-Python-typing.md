---
title: Python Typing
key: 20181110
tags: Python
---


# python typing

The most fundamental support consists of the types 
* Any, 
* Union, 
* Tuple, 
* Callable, 
* TypeVar, 
* Generic. 

## Prerequisite knowledge: Callback

```js
var users = ["yo", "yoyo", "yoyoyo"]

function addUser(username, callback){
    setTimeout(function(){
        users.push(username);
        callback();
    }, 200);
}

function getUsers(){
    setTimeout(function(){
        console.log(users);
    }, 100);
}

adduser("hey", getUsers)
```

## type alias & NewType

NewType a.k.a subtype. NewType can be defined based on another NewType.

Type alias declares two types to be equivalent to one another. Doing Alias = Original will make the static type checker treat Alias as being exactly equivalent to Original in all cases. This is useful when you want to simplify complex type signatures.


```python
Vector = List[float]

def scale(scalar: float, vector: Vector) -> Vector:
    print(type(vector[0])) # output is <class 'int'>
    return [scalar * num for num in vector]

# 此处 Vector 的类型：
$ Vector
>>> typing.List[float]

# typing.List 不能被实例化
$ Vector()
>>> TypeError: Type List cannot be instantiated; use list() instead
```

## Callable

It is possible to declare the return type of a callable without specifying the call signature by substituting a literal ellipsis for the list of arguments in the type hint: Callable[..., ReturnType].

```python
def async_query(on_success: Callable[[int], None],
                on_error: Callable[[int, Exception], None]) -> None:
    # Body
```

## Generic

```python
from typing import TypeVar, Iterable, Tuple, Union
S = TypeVar('S')
Response = Union[Iterable[S], int]

# Return type here is same as Union[Iterable[str], int]
def response(query: str) -> Response[str]:
    ...

T = TypeVar('T', int, float, complex)
Vec = Iterable[Tuple[T, T]]

def inproduct(v: Vec[T]) -> T: # Same as Iterable[Tuple[T, T]]
    return sum(x*y for x, y in v)
```