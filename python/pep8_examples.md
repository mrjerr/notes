Pep8 examples
=

## from _ import
```python
from rest_framework.generics import (
  ListCreateAPIView,
  RetrieveUpdateDestroyAPIView
)
```

## long string
```python
my_very_big_string = (
    "For a long time I used to go to bed early. Sometimes, "
    "when I had put out my candle, my eyes would close so quickly "
    "that I had not even time to say “I’m going to sleep.”"
)
```

```python
foo = long_function_name(var_one, var_two,
                         var_three, var_four)
                         
def long_function_name(
        var_one, var_two, var_three,
        var_four):
    print(var_one)
```

## list
```python
my_list = [
    1, 2, 3,
    4, 5, 6,
    ]


my_list = [
  i
  for i in my_list
  if i%2 == 0
  ]
```
