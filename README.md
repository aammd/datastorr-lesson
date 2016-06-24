## outline

In this lesson, you will learn how to use `datastorr` to solve some common problems that are often encountered when working with data in groups. The lesson will have three parts:

1. A brief description of the Problem that datastorr aims to solve
2. A description of how *you* can start using `datastorr` to solve your problems *today* 
3. A look at some of the technologies that make `datastorr` work.

You work with a lot of data, and often it is changing over time -- sometimes, even indefinitely. You probably have your own solution for adressing these concerns. let's see how `datastorr` can work to help.

## A story

There is a group of researchers who are all working on the same project. They are compiling their dataset together -- each does an experiment in his or her lab, and they all contribute it to the same place. They plan to use this combined data to write a manuscript.

**diagram with suitably diverse emoji representing faces. arrows indicating they are all contributing to the data, and the data goes into one paper**

Every scientist who looks at this figure will see a big problem -- there is never a straight line between a researcher and a dataset. In our story, it is not long before one scientist discovers an error, which they correct. Then, another decides to add one more replicate of the data. Each of these things is important in their own -- the problem is, they are not communicated to everyone at once.

**dataset, represented by a ploygon, is now colored differently for each person**

Before long, we have a different version of the dataset for each researcher -- leading to different analysis results, and therefore different manuscripts.

**illustration of this**

This leads to confusion, which delays publication until everyone has the same version of the data

**crying emojis**

## Enter `datastorr`

The sad story above is probably familiar to anyone who collaborates on data. Isn't there an easier way?!

Let's begin by imagining a more linear life history for data.

**diagram. In this one the events of the previous dataset are placed in a linear sequence. the data is represented by a pentagon, a hexagon and an octogon etc. Arrows point up from emoji that indicate addition or deletion, and merge with an arrow that advances from the previous version of the data. Pointing downwards from the data are white arrows that point to different manuscripts**

Now, each change is included as a modification of the exisiting data. At any point, the dataset can be used to generate an analysis. Analyses are paired with the data they used. 

In order to make a worflow like this function properly, we need three components.

1. A way of advancing the version of the data (black arrows)
2. A way of naming the versions
3. A means of accessing specific versions of the data (white arrows)

`datastorr` provides all these things. We'll look at each in turn.

## what Versions of the data exist: `github_release_info()`

```r
datastorr::github_release_info("richfitz/data", readRDS)

datastorr::github_release_versions(local = FALSE)
```

This requires a repository with the very minimal components for a `datastorr` repo: a `datastorr.json` file

A minimal `datastorr.json` file. 

```
{
    "read": "base::readRDS",
    "filename": "data.rds"
}
```

This function does nothing on its own, but its output is useful in every other function here. The object it returns contains all the info they need. 

To see all the versions of the data in existence, use this

```r
datastorr::github_release_versions(local = FALSE)
```


**illustration here, using squares to represent repositories, and monospace font to represent the .json file (and any other files).**

? do you have to specify a `read` function just to create a new release!?

## Obtaining a version of the data : `github_release_get`

We're going to start by using datastorr to access some public, example datasets:

```r
datastorr::github_release_get(datastorr::github_release_info("richfitz/datastorr.example/", read = readRDS))

## also 
library(datastorr)
github_release_get(github_release_info("richfitz/datastorr.example/", read = readRDS))

```
or equivalently, via piping:

```r
library(magrittr)
"richfitz/datastorr.example/" %>% 
  github_release_info(read = readRDS) %>% 
  github_release_get()
```

You can see that we end up with a copy of the data, displayed on the screen.

Look at the website 

![](screenshot)

There are more versions!
_but first, an experiment_: Turn off your wifi and search for `version = "1.0.1".

> `ERROR`

Now try running it for the most recent (i.e. leave `version` blank, or search for `version = "1.0.2"`)

> it works!

Even without internet! but where is the data? you can see that there is no sign of a data file anywhere. Try restarting R, and then run the line again

> still works!

What's going on?

### a quick interlude -- a bit about how `datastorr` works

Datastorr works by placing your data into an "appropriate place" on your hard drive. This is handy, because the data now can be used across multiple projects. A call to `github_release_get()` will always have the same result -- the specified version of the dataset. 

But where is the data actually? It is stored in a specific drive on your computer -- the "app directory" Where applications store supporting information. it does this through another package, called `rappdirs` [link]().

<!-- show where the app dir is? -->

Abit about `storr`?

## make your own repository and manually add tags. 

Make a dummy repository, something like `YOURNAME/test_data`

Now, fill it in with a low version number, something like `v0.0.1`. <!-- screenshots -->.

In order to finish this, we need to upload some data <!-- screenshot -->

Now let's practice creating a very simple format for transmitting data in R -- the `rds` format

link to [Gavin's blog post about it](http://www.fromthebottomoftheheap.net/2012/04/01/saving-and-loading-r-objects/) here. 

```r
set.seed(42)
x <- rnorm(10)
setwd("~/Desktop")
saveRDS(x, "example.rds")
```

Now upload that alongside your release. You can download the contents of this file, just as you did the example data above:

```r
github_release_get(github_release_info("aammd/test_data", readRDS))
```

And you can see that the answer is the same!

Why does this not require `datastorr.json`??

## Releasing a new version of the data : `github_release_create`

Manually setting the upload process is possible, but it is not very convenient. `datastorr` provides some support for automating this process with `github_release_create`

Let's do an excercise to practice this. Clone your test_data repo from above

A few things to notice -- there is not `example.rds` anywhere. 

## shortcuts, tips & tricks.

There are lots of ways of streamlining this process still further. here are a few

### Combining repos

If you're making new versions of a dataset, you might have lots of code, notes, and raw data associated with the process. You might choose to version control all of this. 

**illustration, with the new data processing R repo coloured in grey**

But, on the other hand, you might decide to put everything in one place -- both the data creating process, AND the releases of the finished data. 

### using R packages

There are two places where you can create an R package. One is in the repository that is being released with the data. The other, is by creating a little "wrapper" package for `datastorr`

#### data-processing repository as package

* link to rrrpkg
* describe how DESCRIPTION can have a version number too (and `github_release_create` is watching it)

#### writing a "wrapper" package for data users

What to do when there is more downstream things to monitor? 

Packages that modify the behaviour of others, while only adding a little, are often called wrappers.


## `datastorr.json`

Accessing data requires us to know _how_ the data is to be read. This information is contained in `datastorr.json`

> datastorr glossary
> There are several terms that are important to highlight. These concepts are united in the `datastorr` workflow
> 
> * semantic versioning -- a common way to indicate version of software. numbers separated by dots. Major.minor.trivial
> * git -- version control software
> * github -- website that enables collaboration on a git repo
> * RDS files -- an R binary file format, smaller than csv
> * `json` -- a file format commonly used on the internet. Javascript Object Notation.
> * `storr`
> * `rappdirs`
> * repository -- a folder under version control
> * releases -- tags for commits. 
