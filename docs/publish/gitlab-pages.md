(publish/gh-pages)=
# GitLab Pages and Pages

Once your content is on GitLab, you can easily host it as a [GitLab
Pages](https://docs.github.com/en/github/working-with-github-pages) website.
This is a service where GitLab hosts your static files as if they were a
standalone website.

There are three ways you can quickly host your book with GitLab Pages:

* Copy/paste your book's HTML to a `docs/` folder, or a `gh-pages` branch of your repository.
* Use the `ghp-import` tool to automatically push your built documentation to a `gh-pages` branch.
* Use a GitLab Action to automatically build your book and update your website when you change the content.

We'll cover each option below.

## Manually put your book's contents online

In this case, you manually build your book's files, and then push them to a GitLab repository in order to be hosted as a website.
There are two ways to do so

:::{admonition} Make sure these steps are done first
:class: warning
Before you do any of the following, make sure that these two steps are completed:

1. Build HTML for your book (see [](../start/build.md)).
   There should be a collection of HTML files in your book's `_build/html` folder.
2. Configure your GitLab repository to serve a website via GitLab Pages at the location of your choice (either a branch or the `docs/` folder).
   See [the GitLab Pages documentation](https://docs.github.com/en/github/working-with-github-pages) for more information.
:::

### Copy and paste your book's `_build` contents into a new folder

The simplest way to host your book online
is to simply copy everything that is inside `_build`
and put it inside a directory named `public`
the default place where GitLab Pages looks for content.
There are two places we recommend:

In a separate branch
: You can configure GitLab Pages to build any books that are in a branch that you specify.
  By default, this is `gh-pages`.

In a `docs/` folder of your main branch
: If you'd like to keep your built book alongside your book's source files, you may paste them into a `docs/` folder.
  :::{warning}
  Note that copying all of your book's build files into the same branch as your source files will cause your repository to become very large over time, especially if you have many images in your book.
  :::

In either case, follow these steps:

1. Copy the contents of `_build/html` directory into `docs` (or your other branch).
2. Add a file called `.nojekyll` alongside your book's contents.
   This tells GitLab Pages to treat your files as a "static HTML website".
3. Push your changes to GitLab, and [configure it to start hosting your documentation](https://docs.github.com/en/github/working-with-github-pages).

## Automatically host your book with GitLab Pages

[GitLab Pages](https://docs.github.com/en/actions) is a tool that allows you to automate things on GitLab.
It is used for a variety of things, such as testing, publishing packages and continuous integration.

Note that if you're not hosting your book on GitLab,
or if you'd like another, user-friendly service to build it automatically,
see the [guide to publishing your book on Netlify](./netlify.md).

```{note}
You should be familiar with GitLab Pages before using them
to automatically host your Jupyter Books.
[See the GitLab Pages documentation](https://help.github.com/en/actions)
for more information.
```

To build your book with GitLab Pages, you'll need to create
an action that does the following things:

* Activates when a *push* event happens on `master` (or whichever)
  branch has your latest book content.
* Installs Jupyter Book and any dependencies needed to build
  your book.
* Builds your book's HTML.
* Uses a `gh-pages` action to upload that HTML to your `gh-pages` branch.

For reference, [here is a sample repository](https://github.com/executablebooks/github-action-demo)
that builds a book with GitLab Pages.

```{note}
Ensure that Jupyter Book's version in your `requirements.txt` file is at least
`0.7.0`.
```

:::{tip}
You can use the [Jupyter Book cookiecutter](https://github.com/executablebooks/cookiecutter-jupyter-book) to quickly create a book template that already includes the GitLab Pages workflow file needed to automatically deploy your book to GitLab Pages:

```bash
jupyter-book create --cookiecutter mybookpath/
```

For more help, see the [Jupyter Book cookiecutter GitLab repository](https://github.com/executablebooks/cookiecutter-jupyter-book), or run:

```bash
jupyter-book create --help
```
:::

Here is a simple YAML configuration
for a Github Action that will publish your book to a `gh-pages` branch.

```yaml
# This file is a template, and might need editing before it works on your project.
# To contribute improvements to CI/CD templates, please follow the Development guide at:
# https://docs.gitlab.com/ee/development/cicd/templates.html
# This specific template is located at:
# https://gitlab.com/gitlab-org/gitlab/-/blob/master/lib/gitlab/ci/templates/Pages/HTML.gitlab-ci.yml

# Full project: https://gitlab.com/pages/plain-html
stages:
  - build
  - deploy

jupyter-build:
  stage: build
  image: python:slim
  script:
    - pip install -U jupyter-book
    - jupyter-book clean .
    - jupyter-book build .
  artifacts:
    paths:
      - _build/

pages:
  stage: deploy
  image: busybox:latest
  script:
    - mv _build/html public
  artifacts:
    paths:
      - public
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

If you want to deploy your site to GitLab Pages at a User and Organization repository (`<username>.github.io`), check another example workflow and available options at the README of [peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages).
