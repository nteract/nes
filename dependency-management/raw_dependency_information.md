# Storing Raw Dependencies in Notebooks

The goal of this idea is to enable capture of simple dependencies in the notebook document
as a first-class metadata assignment. We explored how this might look with a real-world
example and discussed trade-offs and scope of the potential addition.

## Packages

The simplest example we came up with was to specify the exact packages present by the
runtime environment. The intention here is to provide a simple best-effort record of
packages that should be installed before kernel execution. The mechanism for how this
would be populated isn'y explicitly set, but a simple UI component that makes editing it
easy would probably be the best initial implementation.

    "metadata": {
      "requirements": {
        "python": {
          "packages": [
            "pandas==0.1.0",
            "numpy-mkl==0.21.5",
            "my_external_package"
          ]
        ...
      ...
    ...
    
### Keys
The requirements section would be subdivided into a number of targets (i.e. languages) to match against.
Each target would include a `packages` section that indicates all of the requirements for that target
that should be available for this notebook to be "ready".

### Intended use-cases
This simple format is intended to give flexibility to express basic dependencies within the Notebook format.
It is not intended to provide a perfect replication of an execution environment. For a more complex case
one should look at the docker based solutions to capture the exact package, os, and other environment
conditions.

## Environments

Dependencies could also be scoped by the the environment they resided within. When reviewing the options
this was more an exploration of it might look, without finalization of structure of naming conventions.

    "metadata": { 
      "dependencies": {
        "python": {
          "environment": {
            // Using both at same time is problematic -- maybe don't solve here
            "conda": [
              "pandas==0.1.0",
              "numpy-mkl==0.21.5",
              "my_external_package"
            ],
            "venv": [
              "pandas==0.1.0",
              "numpy==0.21.5"
            ],
            // Local
            "system": [
              "pandas==0.2.0"
            ]
          }
        ...
      ...
    ...

### Keys
The keys here match those of the more simple case above expect they further subdivide the target by environment.
The intention is to be able to provide package name deltas for different ecosystems to be able to respect.
The motivating example was `numpy` which has different package names in conda vs venv environments. Here one
could express the package name in either or both environments. It was unclear if this had enough value to merit
the extra complexity.
