
<!-- README.md is generated from README.Rmd. Please edit that file -->

# Using `drat`

## Initial information

### What is `drat`?

Dirk Eddelbuettel (the package’s author) wrote:

> “Drat is an R package which makes it really easy to provide R packages
> via a repository, and also makes it easy to use such repositories for
> package installation and upgrades.”

In my words, you can have your own CRAN. This can be interesting for
companies wanting a central place to distribute internal packages.

For more information visit the [Drat
FAQ](https://eddelbuettel.github.io/drat/vignettes/dratfaq/) and the
[package on GitHub](https://github.com/eddelbuettel/drat).

### Why this document?

I spent half a day playing with `drat` and want to document properly
what I have learnt (for the future me and maybe it can help someone,
like a fast tutorial).

## Hands-on

### Initial setup

#### `drat`

First of all, make sure that `drat` is installed:

``` r
remotes::install_git("eddelbuettel/drat")
```

Then create a new project forking the repo
[`drat-base/drat`](https://github.com/drat-base/drat):

``` r
# Forked and created the project manually because the code below (on 2021-12-16) was not really working
usethis::create_from_github("drat-base/drat", fork = TRUE)
```

And, inside the project, delete the folders `src` and `bin`, that
contain a version of `drat` (not really needed to, but OK to do and will
give you a clean repository):

``` r
fs::dir_delete("docs/src")
fs::dir_delete("docs/bin")
```

#### Your package(s)

I have created a simple package using
[`fusen`](https://thinkr-open.github.io/fusen/), if you don’t know it
you should (it took me less than 5 minutes to create a small but a fully
functional package).

Having your package, you need to generate the source file:

``` r
devtools::build("path/to/the/package/myPackage")
```

It generates `myPackage_0.0.0.9000.tar.gz`.

### Adding packages to your `drat`

``` r
library(drat)
options(dratBranch = "docs")
insertPackage(file = "path/to/the/source/myPackage_0.0.0.9000.tar.gz",
              repodir = "path/to/the/drat/project")
```

Now the source is inside `docs/src/contrib` and all needed files were
updated. As simple as that.

### Using `GitHub` as repository

This is an easy way (but not the best for internal packages), just
enable GitHub Pages with the `docs` folder in the main branch as shown
in the [`README.md` of
`drat-base/drat`](https://github.com/drat-base/drat).

After the page is published (and you pushed your `drat` project after
`insertPackage()` to GitHub) you can test it:

``` r
install.packages("myPackage", repos = "https://<your_name>.github.io/drat")
```

And even better, you can add your GitHub page as a repository:

``` r
library(drat)
addRepo("<name_for_your_repo>", "https://<your_name>.github.io/drat")
```

This way you can install the packages you publish the normal way (as
long as there is no other package with the same name in CRAN or other
repository you may have included):

``` r
install.packages("myPackage")
```

This will work exactly like CRAN for updating packages.

### Using a website as repository

This would be the best solution for internal packages, as you (or the IT
department) can create a page with all the contents of the `docs` folder
(I would find a way to automate this process, as you will probably
maintain more than one package) that can be accessed only from the
company’s network. For this text I created a free page that can be
tested:

``` r
install.packages("myPackage", repos = "https://testdrat.000webhostapp.com")
```

You can add your page as a repository:

``` r
library(drat)
addRepo("<name_for_your_repo>", "https://yourpage.extention")
```

This way you can install the packages you publish the normal way (as
long as there is no other package with the same name in CRAN or other
repository you may have included):

``` r
install.packages("myPackage")
```

This will also work exactly like CRAN for updating packages.

### Using a folder as repository

This is the easiest, *and on Windows the most annoying* (because you
always have to use `type = "source"`<sup id="a1">[1](#f1)</sup>), way of
having an internal repository:

``` r
install.packages("myPackage", repos = "file://server/path/to/drat/docs", type = "source")
```

You can add your folder as a repository:

``` r
library(drat)
addRepo("<name_for_your_repo>", "file://server/path/to/drat/docs")
```

This way you can install the packages you publish the normal way (as
long as there is no other package with the same name in CRAN or other
repository you may have included):

``` r
install.packages("myPackage", type = "source")
```

This will also work exactly like CRAN for updating packages (also having
to use `type = "source` on Windows).

<b id="f1">1</b> You could add binaries for Windows using
`devtools::build("path/to/the/package/myPackage", binary = TRUE)` and
then add binaries to your repo using
`insertPackage(file = "path/to/the/source/myPackage_0.0.0.9000.zip", repodir = "path/to/the/drat/project")`
but, as far as I know, a binary for every R version would be needed.
[↵](#a1)
