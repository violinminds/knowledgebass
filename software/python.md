# Python

The following guides make use of:

- [`pip-review`](https://pypi.org/project/pip-review/)
- [`pip-chill`](https://pypi.org/project/pip-chill/)

So first you have to install them:

```shell
pip install -U pip-chill pip-review
```

## List all installed packages

All packages, with version number:

```shell
pip freeze
```

Only root packages, without version number:

```shell
pip-chill --no-version
# or, excluding pip-chill: pip-chill --no-version --no-chill
```

## Reinstall `pip`

1. uninstall all global packages (see dedicated section)

2. ```powershell
   python -m pip uninstall pip setuptools wheel
   wget https://bootstrap.pypa.io/get-pip.py
   python get-pip.py --force
   ```

3. (optional) reinstall global packages

## Update all global packages

```shell
python -m pip install --upgrade pip
pip-review --local --auto
```

## Uninstall all global packages

```shell
pip freeze >pip.freeze
pip uninstall -r pip.freeze -y
rm pip.freeze
# or:
#   pip-chill >pip.chill
#   pip uninstall -r pip.chill -y
#   rm pip.chill
```

## Reinstall all global packages to the latest available version

```shell
pip-chill --no-version >pip.chill
pip uninstall -r pip.chill -y
pip install -U -r pip.chill
rm pip.chill
```
