## Development 🛠️

Set up your environment:

```bash
python -m venv venv
source ./venv/bin/activate  # On Windows use: .\venv\Scripts\activate
pip install -r requirements.txt
````

Run tests and coverage:

```bash
python -m pytest --verbose --cov=pydmtxlib --cov-report=term-missing --cov-report=html pydmtxlib
python -m pydmtxlib.scripts.read_datamatrix pydmtxlib/tests/datamatrix.png
```

### Test matrix of supported Python versions 🐍

Run tox to test multiple Python versions:

* **macOS/Linux:**

```bash
rm -rf .tox && tox
```

* **Windows (PowerShell):**

```powershell
Remove-Item -Recurse -Force .tox
tox
```

### Windows-specific notes ⚠️

Save the 32-bit and 64-bit `libdmtx.dll` files as `libdmtx-32.dll` and `libdmtx-64.dll` respectively in the `pydmtxlib` directory.
The `_windows_fname` function in `dmtx_library.py` picks the correct DLL.
This setup ensures the DLLs work when running from source, installed package, or frozen binary.

---

## Releasing 🚀

1. **Build wheels and source distributions**

Create source and wheel builds. The `win32` and `win_amd64` wheels will include the correct `libdmtx.dll`:

```bash
rm -rf build dist MANIFEST.in pydmtxlib.egg-info
cp MANIFEST.in.all MANIFEST.in
python setup.py bdist_wheel

cat MANIFEST.in.all MANIFEST.in.win32 > MANIFEST.in
python setup.py bdist_wheel --plat-name=win32

rm -rf build pydmtxlib.egg-info
cat MANIFEST.in.all MANIFEST.in.win64 > MANIFEST.in
python setup.py bdist_wheel --plat-name=win_amd64

rm -rf build MANIFEST.in pydmtxlib.egg-info
```

2. **Release to TestPyPI**

Upload test builds:

```bash
twine upload -r testpypi dist/*
```

3. **Test the release**

* Visit [https://testpypi.python.org/pypi/pydmtxlib/](https://testpypi.python.org/pypi/pydmtxlib/)
* On Windows:

```powershell
c:\python38\python.exe -m venv testenv
.\testenv\Scripts\Activate.ps1
```

* Install dependencies missing from TestPyPI (for Python 2.x only):

```bash
pip install enum34 pathlib
```

* Install Pillow for testing and scripts:

```bash
pip install Pillow
```

* Install your package from TestPyPI:

```bash
pip install --index-url https://testpypi.python.org/simple pydmtxlib
```

* Run test commands:

```bash
read_datamatrix --help
read_datamatrix <path-to-datamatrix.png>
```

4. **Release to PyPI**

If all tests pass, upload to PyPI:

```bash
twine upload dist/*
```

Check [https://pypi.python.org/pypi/pydmtxlib/](https://pypi.python.org/pypi/pydmtxlib/) and then install normally:

```bash
pip install pydmtxlib[scripts]
```