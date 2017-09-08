# MkDocs

## Install
```
pip install mkdocs
```

## Create project
```
mkdocs new my-project
```
* For an existing project, just copy the created files to the existing project directory.

## Test locally
```
cd my-project
mkdocs serve
```

## Publish to github
```
# Create 'gh-pages' branch in your git project
cd my-project
mkdocs gh-deploy
```
* To access the generated site, got to: https://{username}.github.io/{repo-name}
