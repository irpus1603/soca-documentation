# SOCA — Cython Source Protection Guide

**Purpose**: Protect soca-dashboard and soca-engine Python source code for commercial client distribution by compiling `.py` files into native binary extensions using Cython.

---

## How It Works

Cython compiles `.py` source files into C code, which is then compiled into a native binary extension:

- **Linux / macOS** → `.so` file (e.g. `views.cpython-311-x86_64-linux-gnu.so`)
- **Windows** → `.pyd` file (e.g. `views.cp311-win_amd64.pyd`)

Python imports these binaries exactly like normal `.py` modules — no code changes are needed in the application. The original source is not shipped to the client and cannot be practically recovered from the compiled binary.

---

## What Is Protected vs. What Is Not

| Protected ✓ | Not Protected ✗ |
|-------------|-----------------|
| All `.py` logic (views, models, workers, core) | Django HTML templates (`templates/`) |
| Business rules and algorithms | `.env` / `config.json` / `mediamtx.yml` |
| API endpoint definitions | `app/migrations/*.py` — must stay plain (Django reads them as text) |
| ML inference pipeline | `__init__.py` files — must stay for Python package resolution |
| Detection worker logic | Static files, media, model weights (`.pt`) |

---

## Prerequisites

Install build tools on the build machine:

```bash
pip install cython setuptools
```

On **Windows**, also install the **Microsoft C++ Build Tools** (MSVC):
- Download from: https://visualstudio.microsoft.com/visual-cpp-build-tools/
- Select "Desktop development with C++"

On **Linux**, install gcc:
```bash
sudo apt install gcc python3-dev   # Debian/Ubuntu
sudo yum install gcc python3-devel # RHEL/CentOS
```

---

## Build Script — soca-engine

Create `setup.py` in the `soca-engine/` root:

```python
from setuptools import setup, Extension
from Cython.Build import cythonize
import glob
import os

EXCLUDE = {"migrations", "__init__", "tests", "setup"}

def collect_modules():
    extensions = []
    for path in glob.glob("**/*.py", recursive=True):
        parts = path.replace("\\", "/").split("/")
        name = os.path.splitext(parts[-1])[0]
        if any(x in parts for x in EXCLUDE) or name in EXCLUDE:
            continue
        module_name = path.replace("/", ".").replace("\\", ".")[:-3]
        extensions.append(Extension(module_name, sources=[path]))
    return extensions

setup(
    ext_modules=cythonize(
        collect_modules(),
        compiler_directives={"language_level": "3"},
        nthreads=4,
    )
)
```

Run the build:
```bash
cd soca-engine
python setup.py build_ext --inplace
```

---

## Build Script — soca-dashboard

Create `setup.py` in the `soca-dashboard/` root:

```python
from setuptools import setup, Extension
from Cython.Build import cythonize
import glob
import os

# Migrations must stay as plain .py — Django reads them as source text
EXCLUDE_DIRS  = {"migrations", "tests", "__pycache__"}
EXCLUDE_FILES = {"setup", "manage"}

def collect_modules():
    extensions = []
    for path in glob.glob("**/*.py", recursive=True):
        parts = path.replace("\\", "/").split("/")
        name = os.path.splitext(parts[-1])[0]
        if any(x in parts for x in EXCLUDE_DIRS) or name in EXCLUDE_FILES:
            continue
        if name == "__init__":
            continue
        module_name = path.replace("/", ".").replace("\\", ".")[:-3]
        extensions.append(Extension(module_name, sources=[path]))
    return extensions

setup(
    ext_modules=cythonize(
        collect_modules(),
        compiler_directives={"language_level": "3"},
        nthreads=4,
    )
)
```

Run the build:
```bash
cd soca-dashboard
python setup.py build_ext --inplace
```

---

## Packaging for Distribution

After building, remove source files and ship only the binaries:

```bash
# Remove .py source files (keep __init__.py and migrations)
find . -name "*.py" \
  ! -name "__init__.py" \
  ! -path "*/migrations/*" \
  ! -name "manage.py" \
  ! -name "setup.py" \
  -delete

# Remove the generated C files (not needed after compilation)
find . -name "*.c" -delete
find . -name "build" -type d -exec rm -rf {} +
```

### Distribution package structure

```
soca-engine-client/
├── main.py                          ← keep: startup entry point
├── config.json                      ← client-specific config
├── .env                             ← client-specific: EDGE_NAME, API keys
├── yolo/                            ← model weight files (.pt)
├── snapshots/                       ← created at runtime
├── requirements.txt
├── core/
│   ├── __init__.py                  ← keep
│   ├── yolo_inference.*.so          ← compiled binary
│   ├── lpr_engine.*.so
│   └── ...
├── api/
│   ├── __init__.py
│   └── *.so
└── workers/
    └── *.so

soca-dashboard-client/
├── manage.py                        ← keep
├── requirements.txt
├── templates/                       ← HTML files (not compiled)
├── media/                           ← uploaded files
├── app/
│   ├── migrations/                  ← keep ALL migration .py files
│   ├── __init__.py                  ← keep
│   └── *.so                         ← compiled binaries
└── dashboard/
    ├── __init__.py
    └── *.so
```

---

## Platform-Specific Compilation

Compiled binaries are **OS and Python version specific**. You must compile separately for each target platform.

| Client OS | Build machine | Output extension |
|-----------|--------------|-----------------|
| Linux x86_64 | Linux x86_64 (or Docker) | `.cpython-311-x86_64-linux-gnu.so` |
| Windows 64-bit | Windows 64-bit + MSVC | `.cp311-win_amd64.pyd` |
| macOS ARM (M1/M2/M3) | Mac with Apple Silicon | `.cpython-311-arm-darwin.so` |
| macOS Intel | Mac Intel | `.cpython-311-x86_64-darwin.so` |

### Docker build for Linux (recommended for CI)

```dockerfile
# Dockerfile.build
FROM python:3.11-slim
RUN apt-get update && apt-get install -y gcc python3-dev
WORKDIR /build
COPY . .
RUN pip install cython setuptools
RUN python setup.py build_ext --inplace
```

```bash
docker build -f Dockerfile.build -t soca-build .
docker run --rm -v $(pwd)/dist:/output soca-build \
  bash -c "find . -name '*.so' | tar -czf /output/soca-engine-linux.tar.gz -T -"
```

---

## Important Constraints

1. **Python version must match** — binaries compiled for Python 3.11 will not load on Python 3.12. Pin the Python version in your client deployment.

2. **Migrations must stay plain** — never compile `app/migrations/*.py`. Django's migration runner reads them as source text at runtime.

3. **`__init__.py` must stay** — Python requires these to recognise directories as packages. They are typically empty or trivial.

4. **Templates are not protected** — Django HTML templates remain readable. This is unavoidable with Django's template engine. Consider minification as a partial measure.

5. **Not 100% unbreakable** — Cython binaries can be partially reverse-engineered using low-level binary analysis tools, but the effort is prohibitive for most users. It is a strong deterrent, not a cryptographic lock.

6. **Recompile for each release** — if you update the source code, you must recompile and redistribute new binaries to clients.

---

## Optional: License Key Enforcement

For stricter protection, add a license check inside a compiled module that validates against your licensing server at startup. Since the module is compiled, clients cannot bypass the check by editing source:

```python
# core/license.py  (will be compiled — clients cannot edit this)
import requests

def validate(license_key: str, edge_name: str):
    resp = requests.post("https://your-license-server.com/validate", json={
        "key": license_key,
        "edge": edge_name,
    }, timeout=5)
    if resp.status_code != 200 or not resp.json().get("valid"):
        raise RuntimeError("Invalid or expired license key. Contact support.")
```

Call `validate()` in `main.py` at startup before the FastAPI app starts serving.

---

## Summary Checklist

- [ ] Install Cython + build tools on build machine
- [ ] Create `setup.py` for soca-engine
- [ ] Create `setup.py` for soca-dashboard
- [ ] Run `python setup.py build_ext --inplace` on each target platform
- [ ] Verify compiled `.so`/`.pyd` files load correctly (`python -c "import app.views"`)
- [ ] Strip `.py` source files (keep migrations, `__init__.py`, `manage.py`)
- [ ] Package with client-specific `.env` and `config.json`
- [ ] Test full application startup with compiled binaries before shipping
