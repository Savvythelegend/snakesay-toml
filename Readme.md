### A simple Python project that prints a message in a speech bubble inspired by the classic cowsay program.
---

# 1. Project Structure (src layout version)

```
snakesay-project/
│
├── pyproject.toml
├── README.md
├── LICENSE
│
└── src/
    └── snakesay/
        ├── __init__.py
        ├── __main__.py
        └── snake.py
```

---

# 2. pyproject.toml (basic version)

Defines build backend, metadata, and CLI script entry point.

```toml
[build-system]
requires = ["setuptools"]
build-backend = "setuptools.build_meta"

[project]
name = "snakesay"
version = "1.0.0"

[project.scripts]
ssay = "snakesay.__main__:main"

[tool.setuptools.packages.find]
where = ["src"]
```

---

# 3. Running Without Installation

Before installing, you must run the module via `-m`:

```
python -m snakesay Hello
```

because Python needs the package root added to `sys.path`.

---

# 4. Installing the Project (editable)

Install inside a virtualenv:

```
pip install -e .
```

Editable install keeps the package linked to your source code.

Now you can run:

```
ssay "Hello World"
```

---

# 5. CLI Entry Point Mechanism

From pyproject.toml:

```toml
[project.scripts]
ssay = "snakesay.__main__:main"
```

Setuptools generates a script (`ssay`) that calls `main()` in `snakesay/__main__.py`.

---

# 6. **main**.py (entry point)

```python
import sys
from snakesay import snake

def main():
    snake.say(" ".join(sys.argv[1:]))

if __name__ == "__main__":
    main()
```

* Joins CLI arguments
* Calls `say()` from `snake.py`

---

# 7. snake.py (core logic)

Example minimal logic:

```python
def say(message: str):
    print(" _______________")
    print(f"( {message} )")
    print(" ---------------")
    print("  \\")
    print("   \\   (o o)")
    print("        \\_/ ")
```

---

# 8. Managing Dependencies in pyproject.toml

### Direct inside `[project]`:

```toml
[project]
dependencies = ["rich>=13.9.0"]
```

### Or external requirements files:

```
requirements.txt
requirements-dev.txt
```

Then:

```toml
[project]
dynamic = ["dependencies", "optional-dependencies"]

[tool.setuptools.dynamic]
dependencies = { file = ["requirements.txt"] }
optional-dependencies.dev = { file = ["requirements-dev.txt"] }
```

---

# 9. Dynamic Version

Move the version into code:

`snakesay/__init__.py`:

```python
__version__ = "1.0.0"
```

`pyproject.toml`:

```toml
[project]
dynamic = ["version"]

[tool.setuptools.dynamic]
version = { attr = "snakesay.__version__" }
```

---

# 10. Optional Dependencies

```toml
[project.optional-dependencies]
dev = ["black", "isort"]
```

Install with:

```
pip install .[dev]
```

---

# 11. Building Distributions

Install the build tool:

```
pip install build
```

Build the project:

```
python -m build
```

Produces:

```
dist/snakesay-1.0.0.tar.gz   (sdist)
dist/snakesay-1.0.0.whl      (wheel)
```

---

# 12. Uploading to PyPI (or TestPyPI)

```
pip install twine
python -m twine upload dist/*
```

Twine uploads both sdist + wheel.

---

# 13. Full Example pyproject.toml (final clean version)

```toml
[build-system]
requires = ["setuptools>=75.3.0"]
build-backend = "setuptools.build_meta"

[project]
name = "snakesay"
dynamic = ["version"]
dependencies = ["rich>=13.9.0"]
authors = [{ name = "Jin Doe", email = "jindoe@example.com" }]
keywords = ["CLI", "ASCII Art"]
readme = { file = "README.md", content-type = "text/markdown" }
requires-python = ">=3.9"
classifiers = [
    "Development Status :: 3 - Alpha",
    "Intended Audience :: Developers",
    "License :: OSI Approved :: MIT License",
    "Programming Language :: Python :: 3.13",
]

[project.optional-dependencies]
dev = ["black", "isort", "build", "twine"]

[project.scripts]
ssay = "snakesay.__main__:main"

[tool.setuptools.packages.find]
where = ["src"]

[tool.setuptools.dynamic]
version = { attr = "snakesay.__version__" }

[tool.black]
line-length = 88

[tool.isort]
profile = "black"
```