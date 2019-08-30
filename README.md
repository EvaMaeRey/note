A companion guide to Jim Hester’s presentation, ‘You can make an R
package in 20 minutes’
================
Gina Reynolds
8/29/2019

  - [Introduction](#introduction)
  - [Step 00: Press play *on the video
    guide*](#step-00-press-play-on-the-video-guide)
  - [Step 0: Have some functions that you’d like to package up *like the
    below from Hester’s ‘note’
    example*](#step-0-have-some-functions-that-youd-like-to-package-up-like-the-below-from-hesters-note-example)
  - [Step 1: Create package architecture *using
    usethis::create\_package()*](#step-1-create-package-architecture-using-usethiscreate_package)
  - [Step 2: Describe what the package does *in the DESCRIPTION
    file*](#step-2-describe-what-the-package-does-in-the-description-file)
  - [Step 3: Add your functions *to an R Script saved in the R
    folder*](#step-3-add-your-functions-to-an-r-script-saved-in-the-r-folder)
  - [Step 4: Make the package “active” *and test your functions
    interactively*](#step-4-make-the-package-active-and-test-your-functions-interactively)
  - [Step 5: Run a check on the new package *using
    devtools::check()*](#step-5-run-a-check-on-the-new-package-using-devtoolscheck)
  - [Step 6: Add needed dependencies *using
    usethis::use\_package(“package\_name”)*](#step-6-add-needed-dependencies-using-usethisuse_packagepackage_name)
      - [Step 6a.](#step-6a.)
      - [Step 6b: Add dependencies and export functions in R script
        *using “\#’ @”
        notation*](#step-6b-add-dependencies-and-export-functions-in-r-script-using-notation)
  - [Step 7: Documentation these additions *using
    Roxygen*](#step-7-documentation-these-additions-using-roxygen)
  - [Step 8: Incorporate documentation additions into package *using
    devtools::document()*](#step-8-incorporate-documentation-additions-into-package-using-devtoolsdocument)
  - [Repeat Step 5 *devtools::check()*](#repeat-step-5-devtoolscheck)
  - [Step 9: Create a readme file with
    *usethis::use\_readme\_md()*](#step-9-create-a-readme-file-with-usethisuse_readme_md)
  - [Step 10: Create some relevent tests with
    *usethis::use\_test()*](#step-10-create-some-relevent-tests-with-usethisuse_test)
  - [Beyond 20 minutes](#beyond-20-minutes)
  - [Step 11: Create a license using
    *usethis::use\_\*\_license()*](#step-11-create-a-license-using-usethisuse__license)
  - [Step 12: Build and install package using
    *devtools::build()*](#step-12-build-and-install-package-using-devtoolsbuild)
  - [Step 13: Sharing online?](#step-13-sharing-online)
      - [13.a Initialize your directory as git
        repository](#a-initialize-your-directory-as-git-repository)
      - [13.b Push your package to your github
        account.](#b-push-your-package-to-your-github-account.)
  - [Step 14: Tell the world how to get your
    package\!](#step-14-tell-the-world-how-to-get-your-package)
  - [More topics](#more-topics)
  - [Additional Resources](#additional-resources)

<!-- README.md is generated from README.Rmd. Please edit that file -->

## Introduction

<!-- *RStats community:*  Want to build an R package?   -->

<!-- *Me:*  Yes, yes I do.   -->

Recently, several R packages have made creating new R packages — with
all the required architecture and implementing best practices — much
easier. Nevertheless, there is quite a lot to learn to make use of them.
There are a number of excellent guides, and one is by Jim Hester, which
is a video presentation recorded at the RStudio 2018 conference. I like
the video format and the title is enticing: “Make an R package in 20
minutes.” That sounds good. In fact, the build is closer to 12 minutes,
as Jim takes some time to motivate the talk. In the end the pace is
really pretty fast. This “companion guide” collects the steps for
reference.

In truth, creating your *first* R package will probably take more than
twenty minutes. You might need to pause and rewind the video several
times. You’ll probably stop and mull over what has been created for you
when using the various helpful functions. You might even scrap a first
attempt and begin again from scratch. But the ability to reuse your work
and share your work in an accessible format is so exciting, isn’t it?
Let’s begin\!

## Step 00: Press play *on the video guide*

Your video guide is
[here](https://www.rstudio.com/resources/videos/you-can-make-a-package-in-20-minutes/);
the package build starts at about minute
8.

## Step 0: Have some functions that you’d like to package up *like the below from Hester’s ‘note’ example*

If you are interested in creating a package, you have probably already
written some functions. Maybe you’d like to make them available and want
to make them available to the world. So let’s call this Step 0.

For this tutorial you can use Jim Hester’s code reproduced here:

``` r
rate <- 44100
multiplier <- 2 * pi / rate
bpm <- 80
default_volume <- 5

notes <- c("A" = 0, "A#" = 1, "Bb" = 1, "B" = 2, "Cb" = 2, "B#" = 3, "C" = 3,
  "C#" = 4, "Db" = 4, "D" = 5, "D#" = 6, "Eb" = 6, "E" = 7, "Fb" = 7, "E#" = 8,
  "F" = 8, "F#" = 9, "Gb" = 9, "G" = 10, "G#" = 11, "Ab" = 11)

calc_frequency <- function(note, octave) {
  # 440hz is A above middle C
  440 * 2^((unname(notes[note]) + (octave * 12)) / 12)
}

calc_volume <- function(x) {
  # x should be between 1 and 10
  stopifnot(x >= 1, x <= 10)
  
  x / 10
}

calc_length <- function(rate, length, bpm) {
  seq(1, as.integer(rate * length * 60 / bpm))
}

calc_multiplier <- function(rate) {
  2 * pi / rate
}

note <- function(note, length = 1, octave = 0, volume = default_volume) {
  frequency <- calc_frequency(note, octave)
  volume <- calc_volume(volume)
  length <- calc_length(rate, length, bpm)
  multiplier <- calc_multiplier(rate)
  res <- sin(frequency * multiplier * length) * volume
  structure(res, class = "note")
}

# install.packages(audio)
library(audio)

play <- audio::play

print.note <- function(x, ...) {
  audio::play(x, ...)
}
```

## Step 1: Create package architecture *using usethis::create\_package()*

We create the new directory for the new “note package” with
`create_package`.

``` r
usethis::create_package("~/Google Drive/note")
```

A new RStudio session should open, with the working directory of “note”,
and the following file architecture for the package.

``` r
.
├── DESCRIPTION
├── NAMESPACE
├── R
└── note.Rproj
```

## Step 2: Describe what the package does *in the DESCRIPTION file*

Then you might want to check out the contents of the DESCRIPTION file.

The DESCRIPTION looks like this…

``` r
Package: note
Title: What the Package Does (One Line, Title Case)      <- Hey reader!  Change this part!
Version: 0.0.0.9000
Authors@R: 
    person(given = "First",
           family = "Last",
           role = c("aut", "cre"),
           email = "first.last@example.com",
           comment = c(ORCID = "YOUR-ORCID-ID"))
Description: What the package does (one paragraph).
License: What license it uses
Encoding: UTF-8
LazyData: true
```

And you should probably update the title – ie replace “What the Package
Does (One Line, Title Case)”. Of course you should do this for the
“Description” too, especially if you package is intended for an
audience of more than one.

## Step 3: Add your functions *to an R Script saved in the R folder*

  - Create a new script
  - Copy and paste the desired function into that script (what’s in step
    0)
  - Save in the “R” folder

So now you have an R script in your package directory:

``` r
.
├── DESCRIPTION
├── NAMESPACE
├── R
│   └── note.R           <- what you added
└── note.Rproj
```

## Step 4: Make the package “active” *and test your functions interactively*

Then make the package active using devtools::load\_all() in

``` r
devtools::load_all()
```

(You can also click “build” tab in rstudio, and then “load all”)

Then you can try out a function from your package.

## Step 5: Run a check on the new package *using devtools::check()*

Let’s see if there are any problems in this package.

``` r
devtools::check()
```

Uh-oh\! You will
warnings\!

## Step 6: Add needed dependencies *using usethis::use\_package(“package\_name”)*

### Step 6a.

It is not good practice to use library() or require() to use functions
from other package. Instead we first declare dependencies. Using the
function usethis::use\_package will add dependencies to your DESCRIPTION
file.

``` r
usethis::use_package("audio")
```

Automatically, you should see that the end of the contents of the
DESCRIPTION file has added “audio”:

``` r
Package: note
Title: Play Music in R
Version: 0.0.0.9000
Authors@R: 
    person(given = "First",
           family = "Last",
           role = c("aut", "cre"),
           email = "first.last@example.com",
           comment = c(ORCID = "YOUR-ORCID-ID"))
Description: What the package does (one paragraph).
License: What license it uses
Encoding: UTF-8
LazyData: true
Imports: 
    audio
```

### Step 6b: Add dependencies and export functions in R script *using “\#’ @” notation*

Now, in the R script, we indicate import from other packages — and what
functions may be exported.

Exported functions are the functions that will be available to the users
once the package is loaded.

``` r
#' @importFrom audio play play.default
#' @export
audio::play

#' @export
print.note <- function(x, ...) {
  audio::play(x, ...)
}
```

## Step 7: Documentation these additions *using Roxygen*

Highlight the name of the function (‘note’) you want to document.

Then, in the menu go to:

**code -\> Insert Roxygen skeleton**

This will give you the *skeleton*:

``` r
#' Title
#'
#' @param note 
#' @param length 
#' @param octave 
#' @param volume 
#'
#' @return
#' @export
#'
#' @examples
note <- function(note, length = 1, octave = 0, volume = default_volume) {
  frequency <- calc_frequency(note, octave)
  volume <- calc_volume(volume)
  length <- calc_length(rate, length, bpm)
  multiplier <- calc_multiplier(rate)
  res <- sin(frequency * multiplier * length) * volume
  structure(res, class = "note")
}
```

Filling in the *skeleton*:

``` r
#' Create music
#'
#' @param note name
#' @param length in beats
#' @param octave from middle C
#' @param volume from 1 to 10
#'
#' @return a note object
#' @export
#'
#' @examples
#' note("A")
note <- function(note, length = 1, octave = 0, volume = default_volume) {
  frequency <- calc_frequency(note, octave)
  volume <- calc_volume(volume)
  length <- calc_length(rate, length, bpm)
  multiplier <- calc_multiplier(rate)
  res <- sin(frequency * multiplier * length) * volume
  structure(res, class = "note")
}
```

## Step 8: Incorporate documentation additions into package *using devtools::document()*

``` r
devtools::document()  
```

or *build -\> document* to make Roxygen comments part of your package.

You’ll notice the this creates a new subdirectory (man) and documents
related to the exported functions.

``` r
.
├── DESCRIPTION
├── NAMESPACE
├── R
│   └── note.R
├── man
│   ├── note.Rd
│   └── reexports.Rd
└── note.Rproj
```

## Repeat Step 5 *devtools::check()*

Again check you package. You might do this often when you are creating
your own package.

``` r
devtools::check() 
```

## Step 9: Create a readme file with *usethis::use\_readme\_md()*

Help your users understand the package with a readme.

``` r
usethis::use_readme_md()
```

You’ll now see README.md among your files, which you can open and edit.

``` r
.
├── DESCRIPTION
├── NAMESPACE
├── R
│   └── note.R
├── README.md
├── man
│   ├── note.Rd
│   └── reexports.Rd
└── note.Rproj
```

## Step 10: Create some relevent tests with *usethis::use\_test()*

Creates testing infrastructure. Think of some things that should be true
with given inputs in a function. Use the test\_that::test\_that() f

``` r
usethis::use_test()
```

You’ll see a new folder dedicated to testing the package functions.

``` r
.
├── DESCRIPTION
├── NAMESPACE
├── R
│   └── note.R
├── README.md
├── man
│   ├── note.Rd
│   └── reexports.Rd
├── note.Rproj
└── tests
    ├── testthat
    └── testthat.R
```

Open testthat.R and do what Jim does\!

-----

# Beyond 20 minutes

Now I’m drawing mostly from [Karl Browman’s primer on building
packages](https://kbroman.org/pkg_primer/).

# Step 11: Create a license using *usethis::use\_\*\_license()*

You might see a warning if you run a check at this point, about having
no license. So consider adding one.

For
example:

``` r
usethis::use_mit_license(name = "Your Name, Reader")  # replace "Your Name, Reader" with *your name*
```

# Step 12: Build and install package using *devtools::build()*

``` r
devtools::build()
```

This builds a tar.gz, folder which is your compressed package\!

“\~/Google Drive/note\_0.0.0.9000.tar.gz”

# Step 13: Sharing online?

## 13.a Initialize your directory as git repository

``` r
usethis::use_git()
```

Or you can do this in your terminal: git init.

## 13.b Push your package to your github account.

Push package directory to [github](github.com).

You might be able to use usethis::use\_github().

``` r
usethis::use_github()
```

But instead, I use the github instructions
[here](https://help.github.com/en/articles/adding-an-existing-project-to-github-using-the-command-line).

# Step 14: Tell the world how to get your package\!

Now you can let your friends know where your package “lives”. And people
can install your package with devtools::install\_github, and
“githubusername/packagename”. This can be part of your readme. If you
don’t submit to cran right away, you might direct people to install with
the development version using devtools::install\_github.

Here is an example:

``` r
devtools::install_github("EvaMaeRey/note")
```

-----

# More topics

What is travis? What about the coverage report? What about use\_rcpp?

``` r
usethis::use_travis()
usethis::use_coverage()
usethis::use_rcpp()
```

A good starting point for these topics is the resources listed below.

-----

# Additional Resources

Some great additional resources are the following:

  - [GitHub repository for Jim Hester’s
    presentation](https://github.com/jimhester/presentations/tree/master/2018_02_03-You-can-make-a-package-in-20-minutes#readme)
  - [The Note Package I created on
    Github](https://github.com/EvaMaeRey/note)
  - [Hadley Wickham’s *R Packages*](http://r-pkgs.had.co.nz/)
  - [Hillary Parker’s *Writing an R package from
    scratch*](https://hilaryparker.com/2014/04/29/writing-an-r-package-from-scratch/)
  - [Karl Browman’s *R package primer*](https://kbroman.org/pkg_primer/)

<!-- ```{r} -->

<!-- citation(package = c("base")) -->

<!-- citation(package = c("usethis")) -->

<!-- citation(package = c("devtools")) -->

<!-- citation(package = c("fs")) -->

<!-- citation(package = c("roxygen2")) -->

<!-- ``` -->
