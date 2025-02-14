Instructions for making a FloPy release
-----------------------------------------------

## Make a release branch from develop

1.  Make a release branch from develop (*e.g.* `release3.2.10`)
    
2.  If the flopy release coincides with a new MODFLOW 6 release
    
    1.  Update `usgsprograms.txt` in the [GitHub pymake repository](https://github.com/modflowpy/pymake) with the path to the new MODFLOW 6 release. Also update all other targets in `usgsprograms.txt` with the path to new releases.
    
    2.  Recompile all of the executables release on the [GitHub executables repository](https://github.com/MODFLOW-USGS/executables) using the `buildall.py` pymake script and Intel compilers for all operating systems.
    
    3.  Update the README.md on the [GitHub executables repository](https://github.com/MODFLOW-USGS/executables) with the information in the `code.md` file created by the `buildall.py` pymake script. 
    
    4.  Make a new release on the [GitHub executables repository](https://github.com/MODFLOW-USGS/executables) and add all of the operating system specific zip files containing the compiled executables (`linux.zip`, `mac.zip`, `win64.zip`, `win32.zip`). Publish the new release.
    
    5.  Update MODFLOW 6 dfn files in the repository and MODFLOW 6 package classes by running:

        ```
        python -c 'import flopy; flopy.mf6.utils.generate_classes(branch="master", backup=False)'
        ```
    6.  Run `isort` on the updated MODFLOW 6 package classes by running the following from the root directory:

        ```
        isort .
        ```
    7.  Run `black` on the updated MODFLOW 6 package classes by running the following from the root directory:

        ```
        black .
        ```



## Update the release version number

1.  Increment `major`, `minor`, and/or `micro` numbers in `flopy/version.py`, as appropriate.


## Update the Software/Code citation for FloPy

1. Update the authors in `CITATION.cff` for the Software/Code citation for FloPy, if required.


## Build USGS release notes

1.  Manually run `make-release.py` in the `scripts/` directory to update version information using:

    ```
    python make-release.py
    ```

2.  Manually run `update-version_changes.py` in the `scripts/` directory to update version changes information using:

    ```
    python update-version_changes.py
    ```


## Update the example notebooks

Use `run_notebooks.py` in the `release` directory to rerun all of the notebooks in:

1.  `examples\Notebooks` directory.
2.  `examples\Notebooks\groundwater_paper` directory.
2.  `examples\Notebooks\FAQ` directory.


## Commit the release branch

1.  Commit the changes to the release (*e.g.* `release3.2.10`) branch.
2.  Push the commit to the [upstream GitHub repository](https://github.com/modflowpy/flopy).
3.  Wait until the commit successfully runs on [GitHub Actions](https://github.com/modflowpy/flopy/actions).


## Update master branch

1.  Change to the `master` branch in SourceTree.
2.  Merge the release branch (*e.g.* `release3.2.10`) branch into the `master` branch.
3.  Commit changes to `master` branch and push the commit to GitHub.


## Finalizing the release

1.  Tag the commit with the `__version__` number using SourceTree (don't forget to commit the tag).
2.  Push the commit and tag to the GitHub website.
3.  Make release on [GitHub website](https://github.com/modflowpy/flopy/releases). Add version changes for [current release](https://github.com/modflowpy/flopy/blob/develop/docs/version_changes.md) from to release text. Publish release.


## Update flopy-feedstock for conda install

1.  Download the `*.tar.gz` file for the current release from the [GitHub website](https://github.com/modflowpy/flopy/releases).

2.  Calculate the sha256 checksum for the `*.tar.gz` using:

    ```
    openssl sha256 flopy-version.tar.gz
    ```

    from a terminal.

3.  Pull upstream [flopy-feedstock](https://github.com/conda-forge/flopy-feedstock) into local copy of the [flopy-feedstock fork](https://github.com/jdhughes-usgs/flopy-feedstock) repo:

    ```
    cd /Users/jdhughes/Documents/Development/flopy-feedstock_git
    git fetch upstream
    git checkout master
    git reset --hard upstream/master
    git push origin master --force
    ```

4.  Rerender the repo using `conda-smithy` (make sure `conda-smithy` is installed using conda):

    ```
    conda smithy rerender
    ```

4.  Update the version number in `{% set version = "3.2.7" %}` and sha256 in the [flopy-feedstock fork meta.yaml](https://github.com/jdhughes-usgs/flopy-feedstock/blob/master/recipe/meta.yaml) file.

5.  Commit changes and push to [flopy-feedstock fork](https://github.com/jdhughes-usgs/flopy-feedstock).

6.  Make pull request to [flopy-feedstock](https://github.com/conda-forge/flopy-feedstock)


## Update PyPI

1.  Make sure the latest `build` and `twine` tools are installed using:

    ```
    pip install --upgrade build twine
    ```

2.  Create the source and wheel packages with:

    ```
    rm -rf dist
    python -m build
    ```

3.  Check and upload the release to PyPI using:

    ```
    twine check --strict dist/*
    twine upload dist/*
    ```

## Sync develop and master branches

1.  Merge the `master` branch into the `develop` branch.

2.  Increment `major`, `minor`, and/or `micro` numbers in `flopy/version.py`, as appropriate.

3.  Manually run `make-release.py` in the `scripts/` directory to update version information using:

    ```
    python make-release.py
    ```
4.  Commit and push the modified `develop` branch.


