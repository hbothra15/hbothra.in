---
{"dg-publish":true,"tags":["__name__","python"],"title":"Understanding the __name__ Variable","og:title":"Understanding the __name__ Variable","og:type":"article","og:article:author":"Hemant Bothra","og:article:tag":["__name__","python"],"og:article:section":"Technology","permalink":"/python/understanding-the-name-variable-in-python/","dgPassFrontmatter":true}
---

While learning Python, I came across the `__name__` variable and its significance in script execution. Here's what I learned:
## What is `__name__`?

`__name__` is a special variable in Python used internally by the interpreter. Its value depends on how the script is executed:

- **When the script is run directly:** `__name__` is set to `"__main__"`.
- **When the script is imported as a module, `__name__` is set to the module's name.

This allows developers to control the behaviour of a script when executed directly versus when imported.
## Code Example

### `one.py`

```python
# one.py
def func():
    print("func() ran in one.py")

print("top-level print inside of one.py")

if __name__ == "__main__":
    print("one.py is being run directly")
else:
    print(f"one.py is being imported into another module {__name__}")
```

### `two.py`

```python
# two.py
import one

print("top-level in two.py")

one.func()

if __name__ == "__main__":
    print("two.py is being run directly")
```

### `three.py`
```python
import two
import one

print("top-level in three.py")

if __name__ == "__main__":
	print("three.py is being run directly")
```
---

## Output

### Running `one.py` directly:

```txt
top-level print inside of one.py  
one.py is being run directly  
```

### Running `two.py`:

```txt
top-level print inside of one.py  
one.py is being imported into another module one  
top-level in two.py  
func() ran in one.py  
two.py is being run directly  
```

### Running `three.py`
```txt
top-level print inside of one.py
one.py is being imported into another module one
top-level in two.py
func() ran in one.py
top-level in three.py
three.py is being run directly
```

## Observations

### Behaviour of Code Execution:

1. **When running `one.py` directly:**
    
    - All code at 0 indentation is executed.
    - The block `if __name__ == "__main__":` runs because `__name__` is `"__main__"`.
2. **When running `two.py`:**
    - The 0 indentation code in `one.py` runs because it's imported into `two.py`.
    - Since `__name__` in `one.py` is the module name (`"one"`), the `else` block is executed.
    - Afterwards, the 0 indentation code in `two.py` executes, including calling `func()` from `one.py`.
    - Finally, the `if __name__ == "__main__":` block in `two.py` runs because it is the main program.
3. **When running `three.py`:**
	- The 0 indentation code in `one.py` runs because it’s imported via `two.py`.
    - Since `__name__` in `one.py` is `"one"`, the `else` block executes.
    - The 0 indentation code in `two.py` executes next, including calling `func()` from `one.py`.
    - The 0 indentation code in `three.py` executes last.
    - The `if __name__ == "__main__":` block in `three.py` runs.

## Key Takeaways

- The `__name__` variable helps differentiate between direct execution and import behaviour.
- The `if __name__ == "__main__":` block ensures that code only runs when the script is executed directly.

> [!Note]+  Sequence of Execution
> - Code at 0 indentation runs immediately when a module is imported.
> - Once a module has been loaded, it won’t execute again even if explicitly re-imported.
