# PolyglotJustTheDocs

[GitHub Pages documentation](https://docs.github.com/en/pages)

If you don't already have a local copy of the repository, navigate to the 
location where you want to store the source files, replacing `PARENT-FOLDER`
with the folder you want to contain the folder for the repository.
```
cd PARENT-FOLDER
```
Note: This is **not** the repository's folder- that will go _inside_ this
folder.

Navigate to this folder, and clone the repo:
```
git clone https://github.com/Tuufless/PolyglotJustTheDocs.git
```

This will create the following folders:

```
PARENT-FOLDER
  `-- /PolyglotJustTheDocs
      `-- /.git
```
You now have a local copy of an empty repo.

### Creating a new Jekyll site

To do so, use the `jekyll new` command:
```
$ jekyll new --skip-bundle .
```
We now have a skeleton site in our repo:
```
PARENT-FOLDER
  `-- /PolyglotJustTheDocs
      |-- /.git
      |-- /_posts
      |-- .gitignore
      |-- _config.yml
      |-- 404.html
      |-- about.markdown
      |-- Gemfile
      `-- index.markdown
```

Now, open the `Gemfile` that Jekyll created, and:

1. Comment out the line that starts with `gem "jekyll"` by adding a "#" to the
   start of the line.
2. Uncomment the line that starts with `gem "github-pages"`.
3. Change that line to:
```
gem "github-pages", "~> GITHUB-PAGES-VERSION", group: :jekyll_plugins
```
where `GITHUB-PAGES-VERSION` is the latest supported version of the
`github-pages` gem. This version number can be found at the
[Dependency versions list](https://pages.github.com/versions/)

The correct version of Jekyll will be installed as a dependency of the
`github-pages` gem.

4. Finally, Jekyll will use the Minima theme as the default. I want to use `just-the-docs` instead, so I edit the line that starts with `gem "minima"`, and change it to `gem "just-the-docs"`.

5. In `_config.yml`, change the `theme`:
```
theme: just-the-docs
```

6. Finally, add `gem "webrick"` to the `Gemfile`. This is done because webrick
is [no longer a bundled gem in Ruby 3.0](https://www.ruby-lang.org/en/news/2020/12/25/ruby-3-0-0-released/).

If you tried to build the site without this, you would run into the following
error:
```
in `require`: cannot load such file -- webrick (LoadError)
```

Now, run `bundle install`.

You can now try to run the Jekyll site locally:
```
$ bundle exec jekyll serve
```
The site can be previewed in your web browser by navigating to `http://localhost:4000`.

Now, push the changes to Github.

### Just-the-docs theme could not be found

You may find that the build fails.

By default, when you create a Github repository and then enable Github Pages,
it is deployed with a Ruby gem called `github-pages`. Among other things, this
forces a certain version of Jekyll and _does not_ run `bundle install`; it only
uses a preselected set of gems.

Since `just-the-docs` is not one of them, you then get the error
```
Error: The just-the-docs theme could not be found.
```
In order to resolve this, the template packages its own Github Actions workflow
that does similar work, but uses the gems listed in our `Gemfile` and runs
`bundle install`. The only necessary step after this is to "enable" this version
of the workflow, instead of the `github-pages` gem. This is where the Github workflow comes in.

Go into Settings > Pages > Source and set the Source to be "GitHub Actions".

You are then presented with a Jekyll action, which you can configure. Either
commit those changes directly from Github, or copy the file contents into
`.github/workflows/jekyll.yml`. [More info](https://github.com/just-the-docs/just-the-docs/issues/1273)

### Exit code 16

Now, the Github Action fails with:
```
The process '/opt/hostedtoolcache/Ruby/3.1.4/x64/bin/bundle' failed with exit code 16
```

We need to run `bundle lock --add-platform x86_64-linux` from within the
project, and commit the changes. [More info](https://stackoverflow.com/questions/72331753/ruby-and-rails-github-action-exit-code-16)

This is done because the Github Action config for Ruby declares the `ubuntu-latest`
platform image for running the job on Github Actions. Github Actions uses
`x86_64-linux` platform for Ubuntu. However, the `Gemfile.lock` is missing that
platform, leading to exit code 16.

The site can now be previewed at https://tuufless.github.io/PolyglotJustTheDocs/
