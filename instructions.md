*How to update the docs

-you need sphinx installed

-pull the repository to get current version

-make changes to the relevant .md file

-if the .md file needs to be displayed but its name is not under the toctree 'contents' in the index.rst file, then add it

-run the following command at the root of the project directory:
```
sphinx-build . _build
```

-if build was successfull push the repo and read the docs will update automatically
