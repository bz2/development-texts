# Using pyenv to manage Python versions

[pyenv] is a utility for quickly and easily managing and switching between different versions of Python installed on your local machine. It offers three major advantages over installing Python via packages from `apt` repositories (or similar):

- `pyenv` builds its versions of Python from source, so you're not limited to only the prebuilt Python versions that the package repository has.
- Because it's building from source it's also much less likely to complain during install.
- It allows easy configuration of the local version of Python to be used within a given project - no more worrying about which specific version of Python you're invoking when you type `python` or `pip`. If you're inside a project folder, `python` will always point to the local version of Python for that project, or else (if local is not configured) the `pyenv` global version of Python.

## Setup

Clone `pyenv` to your home directory.

```sh
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```

Modify your `.bash_profile` to initialise `pyenv` on shell startup.

```sh
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile
echo 'eval "$(pyenv init --path)"' >> ~/.bash_profile
```

Reapply `.bash_profile` settings to current shell.

```sh
source ~/.bash_profile
```

Install the prerequisite packages for building Python locally.

```sh
sudo apt-get update; sudo apt-get install make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
```

List the available versions of Python that you can install.

```sh
pyenv install --list
```

Use `pyenv` to install your target version of Python.

```sh
pyenv install 3.10.1
```

Set 3.10.1 as the local Python version for the current project/repository.

```sh
pyenv local 3.10.1
```

At this point, running ```python -V``` from inside sm_platform should return version 3.10.1, and ```pyenv which pip``` should point to a pip location associated with 3.10.1.

You can install a different Python in a different project, and as long as the local Python is configured `pyenv` will ensure that the correct one is invoked according to the project you're in.

[pyenv]: https://github.com/pyenv/pyenv
