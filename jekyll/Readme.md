# Jekyll

## Install
* Install gem
* Install jekyll and bundler: `gem install jekyll bundler`

## Setup
* New blog: `jekyll new {project}`
* Edit Gemfile:
    * Comment: `gem "jekyll", ...`
    * Uncomment: `gem "github-pages",...`
    * Run: `bundle install`

## Preview
```
bundle exec jekyll serve
# Now browse to http://localhost:4000
```

## Build
```
bundle exec jekyll build
```

## Deploy to github
* Make sure `gh-pages` branch exists
```
# Generate _site/ to gh-pages
github-pages br gh-pages

# Checkout to gh-pages
git checkout gh-pages

# Move _site content to root directory of gh-pages branch
mv _site/* .
```
