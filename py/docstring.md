
# DocString

+ A `string literal` is a sequence of characters written directly in your code like `name = "foo"`
+ A docstring is a special kind of `string literal` used to document a module, function, class, or method
+ A DocString Surrounded by triple quotes
+ A docstring is not a comment
+ Accessible at runtime via `.__doc__`

```py
"""This is a module-level docstring."""

class MyClass:
    """Class-level docstring."""

    def method(self):
        """Method-level docstring."""
        pass
```


## Common styles

1. PEP 257 (Python Standard)
2. Google Style
3. NumPy Style
4. reStructuredText (Sphinx)


#### 1. PEP 257

```py
def divide(a, b):
    """Divide a by b and return the result.     ---> one line explanation

    This function performs a floating-point division of two numbers.    ---> longer explanation

    """
    return a / b
```


#### 2. Google style
```py
def divide(a, b):
    """Divide a by b and return the result.

    Args:
        a (float): Numerator.
        b (float): Denominator.

    Returns:
        float: The result of the division.

    Raises:
        ZeroDivisionError: If b is zero.
    """
    return a / b
```

#### 3. NumPy style

```py
def divide(a, b):
    """
    Divide a by b and return the result.

    Parameters
    ----------
    a : float
        Numerator.
    b : float
        Denominator.

    Returns
    -------
    float
        The result of the division.

    Raises
    ------
    ZeroDivisionError
        If b is zero.
    """
    return a / b
```


#### 4. Sphinx style

```py
def divide(a, b):
    """
    Divide a by b and return the result.

    :param a: Numerator
    :type a: float
    :param b: Denominator
    :type b: float
    :return: Result of the division
    :rtype: float
    :raises ZeroDivisionError: If b is zero
    """
    return a / b
```



























