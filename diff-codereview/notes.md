# Notes

## MORNING SESSION (CR & DIFF)

### PROBLEM

* Git - Line based diff'ing tool. Works well for code and binary. 
    * For Notebooks, hard to understand what changed across metadata, source, outputs
    * Plugin: Atlassian plugin for rendering Notebooks
    * GitHub and GitLab has rendering support for Notebook files 
* Use cases
    * Developer: I want to be able to view the changes I made so far
    * Reviewer:  I want to be able to legibly view changes made for a good review
        * Using the regular code review process  (i.e. diff between Version 3 & Version 4)
        * Data scientists/analysts have a different perspective on “Code Review” - Similar to document or paper review.
    * Linear Versions:
        * Linear versions outside Git - Bookstore or Eider like versions
    * Bitbucket issue: https://bitbucket.org/site/master/issues/16323/preview-diff-for-notebooks

### CURRENT SOLUTIONS

1. **NBDIME**
    1. CLI tool: diff nb1.ipynb nb2.ipynb
    2. Python tool:
        1. Python API should be consistent across NBDIME's web UI and 
    3. Web GUI: Starts a web server on the side
        1. Not easy to integrate into existing UIs
2. **COCALC - http://blog.sagemath.com/(details)**
    1. Jupyter protocol: Layer between client and server with a new UI
    2. use: https://github.com/google/diff-match-patch
    3.  Motivation: Compute infrastructure for whole classroom, not meant as an Enteprise solution
    4. Deltas for the notebooks are stored as a transaction log rather than full file 
    5. Concurrent editing: Delta diff will be applied to the diff, backend will accept or reject the diff. Delta is sent to the backend via WebSocket
    6. Review story is more of publishing a story
3. **AZURE**
    1. Azure notebooks has Angular UI for rendering the diff from nbdiff
    2. Doesn't use Git versioning
4. **BOOKSTORE**
    1. Issue #58 on the Bookstore repo (TODO: Link to the JupyterLab repo)
5. **REVIEWNB**
    1. Support for GitHub - Its a GitHub plugin
    3. Roadmap - https://github.com/ReviewNB/support/issues?q=is%3Aopen+is%3Aissue+label%3A%22Feature+Request%22+sort%3Acreated-asc
    4. Source code: NOT Open Source
    5. TODO: Does this look into Metadata differences? (i.e. Changed the kernel)
    6. Pricing:  For private repos
6. **GITHUB**
    1. John to start conversation with GitHub
7. **NBGRADER**
    1. Assignment scores can be assigned at the cell level
8. **NBSTRIPOUT**
    1. Strip outputs from notebooks 

### ACTION ITEMS

* Issue on NBDime for server API 
  * `/api/diff` present on the server, added issue for Swagger spec
* Issue with JLGit/Bookstore for a React component to render the diff
    * Example with NTeract  of re-usable components https://github.com/nteract/nteract/tree/master/packages
* Someone from MSFT to reach-out to GitHub
    * Promote nbdime adoption with github via community messaging: https://github.community/t5/GitHub-API-Development-and/Use-nbdime-as-diff-tool-for-Jupyter-Notebook/m-p/18725#M858
* Integrate nbdime, nbstripout, (and bookstore in a repo?) in an example repo
* Explore how phabricator could integrate with nbdime?
  * Went through the source code
  * Looks like we could React component into the UI
* Think about read-only comments, resolving, seeing older version comments

