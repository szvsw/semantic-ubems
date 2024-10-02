# semantic-ubems

[![Release](https://img.shields.io/github/v/release/szvsw/semantic-ubems)](https://img.shields.io/github/v/release/szvsw/semantic-ubems)
[![Build status](https://img.shields.io/github/actions/workflow/status/szvsw/semantic-ubems/main.yml?branch=main)](https://github.com/szvsw/semantic-ubems/actions/workflows/main.yml?query=branch%3Amain)
[![codecov](https://codecov.io/gh/szvsw/semantic-ubems/branch/main/graph/badge.svg)](https://codecov.io/gh/szvsw/semantic-ubems)
[![Commit activity](https://img.shields.io/github/commit-activity/m/szvsw/semantic-ubems)](https://img.shields.io/github/commit-activity/m/szvsw/semantic-ubems)
[![License](https://img.shields.io/github/license/szvsw/semantic-ubems)](https://img.shields.io/github/license/szvsw/semantic-ubems)

This is a repository for specifying semantic GIS-to-UBEM pipelines.

- **Github repository**: <https://github.com/szvsw/semantic-ubems/>
- **Documentation** <https://szvsw.github.io/semantic-ubems/>

## Getting started with your project

First, create a repository on GitHub with the same name as this project, and then run the following commands:

```bash
git init -b main
git add .
git commit -m "init commit"
git remote add origin git@github.com:szvsw/semantic-ubems.git
git push -u origin main
```

Finally, install the environment and the pre-commit hooks with

```bash
make install
```

You are now ready to start development on your project!
The CI/CD pipeline will be triggered when you open a pull request, merge to main, or when you create a new release.

To finalize the set-up for publishing to PyPI or Artifactory, see [here](https://fpgmaas.github.io/cookiecutter-poetry/features/publishing/#set-up-for-pypi).
For activating the automatic documentation with MkDocs, see [here](https://fpgmaas.github.io/cookiecutter-poetry/features/mkdocs/#enabling-the-documentation-on-github).
To enable the code coverage reports, see [here](https://fpgmaas.github.io/cookiecutter-poetry/features/codecov/).

---

Repository initiated with [fpgmaas/cookiecutter-poetry](https://github.com/fpgmaas/cookiecutter-poetry).
