# Quick Reference for Papermill and Scrapbook

## Papermill

[Repo](https://github.com/nteract/papermill) |
[Documentation](https://papermill.readthedocs.io/en/latest/)

**Papermill** is a tool for parameterizing, executing, and analyzing
Jupyter Notebooks.

### Try it

[![badge](https://tinyurl.com/ybwovtw2)](https://mybinder.org/v2/gh/nteract/papermill/master?filepath=binder%2Fprocess_highlight_dates.ipynb)
[![badge](https://tinyurl.com/y7uz2eh9)](https://mybinder.org/v2/gh/nteract/papermill/master?filepath=binder%2Fcli-simple%2Fcli_example.ipynb)

### Install it

From the command line:

``` {.sourceCode .bash}
python3 -m pip install papermill
```

### Learn more

- [Netflix blog: Scheduling Notebooks at Netflix](https://medium.com/netflix-techblog/scheduling-notebooks-348e6c14cfd6)
- [Download Link for JuptyerCon talk slidedeck](https://cdn.oreillystatic.com/en/assets/1/event/285/Scheduled%20notebooks_%20A%20means%20for%20manageable%20and%20traceable%20code%20execution%20Presentation.pptx)
- [Podcast episode: Using Notebooks As The Unifying Layer for Data Roles at Netflix with Matthew Seal](https://www.dataengineeringpodcast.com/using-notebooks-as-the-unifying-layer-for-data-roles-at-netflix-with-matthew-seal-episode-54/)

---

## Scrapbook

[Repo](https://github.com/nteract/scrapbook) |
[Documentation](https://scrapbook.readthedocs.io/en/latest/)

A library for recording a notebook's data values and reading these recorded data
values at a later time.

**scrapbook** provides a robust and flexible recording schema. This library is
planned to replace [papermill](https://papermill.readthedocs.io)'s existing
`record` functionality.

### Try it

[![badge](https://tinyurl.com/ybk8qa3j)](https://mybinder.org/v2/gh/nteract/scrapbook/master?filepath=binder%2FResultsDemo.ipynb)

### Install it

From the command line (beta name temporary):

``` {.sourceCode .bash}
python3 -m pip install scrapbook-beta
```

### Learn more

- [Ideas and possible approaches from README](https://github.com/nteract/scrapbook#ideas-and-possible-approaches-for-development)
- [API and Module Reference](https://nteract-scrapbook.readthedocs.io/en/latest/reference/modules.html)

---

*resources-papermill.md* 

*2019-02-02*
