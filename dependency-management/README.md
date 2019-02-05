# Notebook Dependency Management

## Contents

- [Problem Abstract](#problem-abstract)
  - [The Growing Problem](#the-growing-problem)
  - [Goal of Discussion](#goal-of-discussion)
- [User personas](#user-personas)

  - "Just give me a Notebook"
  - Standard image with ability to customize
  - Fully specify the environment

- [Types of Dependency Problems](#types-of-dependency-problems)
- [Current approaches](#current-approaches)
- [Potential Solutions](#potential-solutions)
- [Appendix](#appendix)
  - [Terminology](#terminology)
  - [Resources](#resources)

## Problem Abstract

### The Growing Problem

Dependency management in Notebooks has historically been left as an exercise
for the reader. For an advanced user exploring a notebook’s potential this has
been a fine trade-off. However as notebook infrastructure has become more
distributed and is fulfilling more advanced use-cases the need for a
structured way to manage dependencies across kernels and deployments has been
growing. Many companies have baked their own solutions which work within
limited scopes, but are difficult to maintain, scale, or serve new use-cases.
Ideally we want to move the state of the art forward of dependency management
as it relates to notebooks by either defining a convention, tool, or both.

### Goal of Discussion

Separate dependency definition and dependency matching within Jupyter
ecosystem to allow for a variety of dependency issues.

## User Personas

TODO

## Types of Dependency Problems

Here’s three stories that capture many of the dependency problems:

1. I have a notebook and I want to **share it** with others and give them a
     way to **replicate it** for their own use. (Related, I want to open my
     notebook on a **different notebook server** than where I was originally
     working with it.)
2. I have a notebook and want to **run it headless** in some
     service that supports that (using papermill or similar).
3. I have a notebook
     with an environment and I want to **clone that environment** so I can use it
     with a new notebook or some other program (like if I export the notebook to
     .py, for instance).

These stories have good grounding in where users experience friction. If we
look closer at the root causes for friction in these cases we can see a
variety of points of friction that make solving these stories difficult.

- Installing language dependencies to the correct environment. It can be hard to install to the right python site-packages when you have several virtualenvs or conda installs.
- Installing OS packages required for library
  dependencies. This is more challenging and perhaps not something we should
  solve at first. In general it almost always requires human intervention the
  first time to do correctly and understand the constraints under which is
  should work.
- Installing packages before launching the kernel's python
  process, or perhaps restarting the kernel after installation. Some packages
  are not importable, or damage neighboring imported packages, if installed
  after the active process launched. This issue plagues !pip install pattern
  for a few scientific computing packages in particular.
- Runtime installation
  (e.g. `pip install foo`) can be tedious for users to bundle code into
  dependencies in shared locations, can fail to install when in conflict with
  the notebook environment, and can pose licensing or security concerns.
- Pre-built dependency artifacts can be tedious for a user to define, test, and deploy. Requires more advanced understanding of tooling and surrounding
  ecosystem for a notebook server to do right.
- Applications calling a notebook
  should be able to choose how to respect dependency assignments. For example,
  a scheduled notebook may have an older version of the package and may want
  to choose whether to upgrade it or leave it pinned back to meet the requirement specification based on success of failure of other tasks. If the
  install is always forcibly run by the notebook this can't be controlled, and the notebook writer may install packages with known stability or security issues by including the -U option always.
- Service maintainers may want to
  inject additional dependencies or rules about dependencies without modifying
  user notebooks. There may be a base requirements file that's pulled in
  statically or dynamically which needs to be processed before or after
  notebook dependencies are met.
- Hardware and resource dependency indicators that need to be matched before launch.

As you can see there’s a lot of nuanced points of friction. Luckily many of
these have been solved in part for different languages or ecosystems and we
should be able to lean more into such tooling when available in a controlled
manner.

## Current Solutions

For a user or maintainer today, the options either prescribe very controlled
environments or push the burden of responsibility to the user. This is a few
solutions we’ve seen in real production systems:

- Single large image with managed containers.

- Most dependencies for an organization can met this way but it comes with a
  high price tag and never completely solves the problem.

- Requirements list for apt-get / python / etc installed into dev environment
  automatically on boot

- Only helps for very controlled environments with fixed deployment paths.
  Notebooks tend to not run outside of those environments.
- pip[3] install package-you-hope-is-compatible repeated at the top. This can
  now be done a _little_ better with %pip or %conda which will solve selecting the correct installer for the calling kernel.
- The %pip and %conda magics that got recently added for python which help a
  little when in multi-python environments

- Custom image or dependency tar managed by the notebook writer or her team (this has a lot of maintenance and security issues over time)

- [repo2docker](https://github.com/jupyter/repo2docker) gives a fundamental that could be made to have a smart template for patterns we adopt. In theory many of the topics above can be partially solved with a JIT docker image. This has latency and management concerns in some situations but it's a reasonable trade-off for many situations.

- Nested Docker Containers

- Multiple build systems within the same company mean there are customers are managing their dependencies already as docker containers making it challenging for the Notebooks service to inject other dependencies needed for the platform. Uber has internally developed a nested container model to address these use cases.

- System scaffolding within the parent ecosystem
- Managed systems can provide a framework to control various dependencies and opinions. Has similar trade-offs to managed images.

## Potential Solutions

There's more than one tool here to help solve the problems in a better way.
Furthermore the primary problem is around what should be the first-class mode
of transport for specifying what and how dependencies should be met.
Basically, where one specifies what the dependencies of a notebook are helps
drive what solutions work best across the various particulars. The potential
locations fall into a few categories:

1. Share a notebook as-is

2. a) Inside the notebook (ipynb file) as metadata. b) Inside the notebook
      as a new cell type or magic

3. Share a notebook environment

4. a) Inside a companion file to the ipynb file b) In group of files (like
      a github repo) that travels together.

Generally option 1a) and 2b) seem to have the best merits for providing
dependencies in a manner that accommodates many kernels and doesn’t impact
existing interfaces dramatically. We want to choose solutions that are not
dependent on languages, but specify a setting in which most language
dependency management solutions can fit.

Here’s a brainstorm of some more specific examples of how requirements could
be applied to these locations.

- A Requirements cell type which reads like the requirements file of the
- kernel language. A requirements.txt's contents for python, an sbt or gradle
- property file for scala, description or packrat file for R, etc... Then
- perhaps extend the kernel spec to include a kernel preparation which takes
- the requirements cell contents and performs installation of that cell. This
- way the various interfaces could choose how to execute the requirements cell
- and older interfaces not aware of this spec expansion could pass the cell
- contents as is to kernels and install just-in-time as a fallback to more
- sophisticated management options. There's a few ways to maintain backwards
- compatibility in this space, so I think we could change many of the minor
- details without sacrificing the first-class model improvement. A reserved
- section of the notebook metadata which specifies notebook requirements
- either as a file reference or some structured part of the document. Perhaps
- something like { "jupyter": { "requirements": { "pip": { "packages":
- ["pandas", "nbconvert"], "options": ["-U"] } } } }. UI's for easily managing
- this could follow the base capability. The downside here is that languages
- with more complex requirements files may need a lot of guidance and
- maintenance support to specify a structured document version of their specs
- (I'm looking at you sbt :/). Write a plugin that uses the repo2docker
- mechanism for determining packaging from the filenames in the dependency and
- executes the correct package installer on demand. (in some environments,
- that plugin may run automatically on notebook startup). This plugin could
- also support viewing and editing the dependencies as well as importing,
- exporting, and comparing with the corresponding files in the file system.
- This would be useful when you are working in a repo with an environment.yml
- for instance and you want to share your single notebook with someone without
- having to share the whole repo. First-class magics in each of the command
- kernels. We have %pip and %conda now. It would follow that %sbt and %packrat
- could easily follow. This doesn't address all of the concerns above, but
- it's a good step in the right direction.
