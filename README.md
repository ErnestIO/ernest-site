# ernest.io

The ernest.io site is based on the [Hugo Bootstrap Premium theme](https://github.com/appernetic/hugo-bootstrap-premium), and takes inspiration from the [nats.io site](http://nats.io).

## Contributing

If you would like to contribute code to Ernest please follow the standard Fork-and-Branch GitHub workflow. If you're not familiar with this process please read the following guides:

* ['Forking Projects' GitHub Guide](https://guides.github.com/activities/forking/)

* ['Fork a Repo' GitHub Help article](https://help.github.com/articles/fork-a-repo/)

We will review and discuss with you any contributions or corrections submitted via GitHub Pull Request.

## Making Changes

To make sure your changes render correctly, you can build and preview the site on your local system using Hugo. 

**Note 1:** On OS X, if you have [Homebrew](http://brew.sh/), installation is even easier: just run `brew install hugo`.

**Note 2:** Hugo requires Go 1.4+. If Go is not already installed on your system, you can [get it here](https://golang.org/dl/).

Now install Hugo:

```
go get -u -v github.com/spf13/hugo
```

Clone your forked copy of the repository:

```
git clone git@github.com:<YOUR GIT USERNAME>/ernest-site.git
```

Change to the directory:

```
cd ernest-site/
```

Build the site and start the server:

```
hugo server -w
```

The `-w` switch starts Hugo in live preview mode, so you can see your updated content and layout in real time as you edit.

## Navigation

The main menu can be changed by modifying `data/Menu.toml`:

```
[1-download]
Name = "Download"
URL = "download"

[2-documentation]
Name = "Documentation"
URL = "documentation"
```

The Documentation side menu can be changed by modifying `[[menu.documentation]]` in `config.toml`:

```
[[menu.documentation]]
  name = "Introduction"
  weight = 0
[[menu.documentation]]
  name = "vCloud Director"
  weight = 1
```

## Content

All content resides in the `content` directory and its sub-directories. Each main menu link corresponds to a file (html or markdown) or a sub-directory.

Each page needs a header similar to this:

```
+++
date = "2016-05-17"
cssid = "download"
type = "download"
description = "Ernest makes it easier to develop and deploy applications in the Cloud. Ernest supports full-stack application orchestration that allows you to drive software and infrastructure as a unified system."
+++
```

Documentation pages need additional information in their headers to control how they appear in the side menu:

```
+++
date = "2016-06-06"
title = "Introduction"
category = "documentation"
showChildren=true
[menu.documentation]
  name = "Introduction"
  weight = -100
  identifier = "vcloud-intro"
  parent = "vCloud Director"
+++
```

## Style Guidelines and Conventions

* Use topic-based files and titles
* Use only headers 1 (#), 2 (##) and 3 (###)
* Use single spaces to separate sentences
* Markdown syntax: http://daringfireball.net/projects/markdown/syntax#img
 * Links: `[Ernest](http://ernest.io/)`
 * Cross references: `[Support](/support/)`
 * Images: `![dog](/img/ernest-dog.png)`
* Triple ticks for code, commands to run, user operations, input/output
* Single ticks for executable names, file paths, inline commands, parameters, etc.
* Graphics: save as *.png; source in `static/img/`
