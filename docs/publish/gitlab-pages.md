(publish/gitlab-pages)=
# GitLab Pages and GitLab CI

Once your content is on GitLab, you can easily host it as a [GitLab
Pages][GitLab Pages] website. This is a service where GitLab hosts your static
files as if they were a standalone website.

Learn more about GitLab Pages at [GitLab Pages][GitLab Pages] and the official
[documentation][GitLab Pages documentation].

[GitLab Pages]: https://pages.gitlab.io
[GitLab Pages documentation]: https://docs.gitlab.com/ce/user/project/pages/.

There are two ways you can host your book with GitLab Pages:

* Copy & paste your book's HTML to a `public` directory
* Use a [GitLab CI][ci] to automatically build your book and update your
  website when you change the content.

We'll cover each option below.

## Manually put your book's contents online

In this case, you manually build your book's files, and then push them to a
GitLab repository in order to be hosted as a website. There are two ways to do
so

:::{admonition} Make sure these steps are done first
:class: warning

Before you do any of the following, make sure that these two steps are
completed:

1. Build the HTML version of your book i.e. from inside the root directory of
   the repository run `jupyter-book build .` (see also [](../start/build.md)).
   There should be a collection of HTML files in your book's `_build/html`
   directory.
   If this is a re-build, it might be useful to clean a previous build, i.e.
   via `jupyter-book clean .`.

2. Copy the `_build/html/*` to a directory named `public/`.
   It is the default place in a GitLab repository to serve a website via GitLab
   Pages at the location of your choice.
   See
   [the GitLab Pages documentation](https://docs.github.com/en/github/working-with-github-pages)
   for more information.

:::

### Copy and paste your book's `_build` contents into a directory named `public`

The simplest way to host your book online is to simply copy everything that is inside `_build` and put it inside a directory named `public` in the main branch. This is the default place where GitLab Pages looks for content.
Read more at [GitLab Pages][GitLab Pages]

: If you'd like to keep your built book alongside your book's source files, you may paste them into a `docs/` directory.
  :::{warning}
  Note that copying all of your book's build files into the same branch as your source files will cause your repository to become very large over time, especially if you have many images in your book.
  :::

In either case, follow these steps:

1. Copy the contents of `_build/html` directory into `public` (or your other branch).
2. Commit and push your changes to GitLab, and [configure it to start hosting your documentation](https://docs.github.com/en/github/working-with-github-pages).

## Automatically host your book with GitLab CI

A project's static Pages can be built by [GitLab CI][ci], following the steps
defined in [`.gitlab-ci.yml`](.gitlab-ci.yml):

```bash
# Full project: https://gitlab.com/pages/jupyter-book-example

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

[GitLab CI](https://docs.github.com/en/actions) is a tool that allows you to
automate things on GitLab. It is used for a variety of things, such as testing,
publishing packages and continuous integration.

Note that if you're not hosting your book on GitLab,
or if you'd like another, user-friendly service to build it automatically,
see the [guide to publishing your book on Netlify](./netlify.md).

```{note}
You should be familiar with GitLab Pages before using them
to automatically host your Jupyter Books.
[See the GitLab Pages documentation][GitLab Pages documentation]
for more information.
```

To build your book GitLab CI and host it at GitLab Pages, you'll need to create
a `.gitlab-ci.yml` file that does the following things:

* Activates when a *push* event happens on `master` (or whichever)
  branch has your latest book content.
* Installs Jupyter Book and any dependencies needed to build
  your book.
* Builds your book's HTML.
* Copies that HTML to your `public` directory.

For reference, [here is a sample repository](https://gitlab.com/pages/jupyter-book)
that builds a book with GitLab Pages.

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

Here is a simple YAML configuration for a GitLab CI that will build and copy
your book to the `public` directory.

```yaml
# This file is a template, and might need editing before it works on your project.
# To contribute improvements to CI/CD templates, please follow the Development guide at:
# https://docs.gitlab.com/ee/development/cicd/templates.html
# This specific template is located at:
# https://gitlab.com/gitlab-org/gitlab/-/blob/master/lib/gitlab/ci/templates/Pages/HTML.gitlab-ci.yml

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

## One more thing

To use a repository/project as your user/group website, you will need one additional step:
just rename your project to `namespace.gitlab.io`, where `namespace` is your `username` or `groupname`.
This can be done by navigating to your project's **Settings**.

[ci]: https://about.gitlab.com/gitlab-ci/
[jupyter-book]: http://jupyterbook.org
[install]: https://jupyterbook.org/start/overview.html#install-jupyter-book
[documentation]: https://jupyterbook.org/intro.html
