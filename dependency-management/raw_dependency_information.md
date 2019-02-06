# Storing Raw Dependencies in Notebooks

The goal of this idea is to enable capture of simple dependencies in the notebook document
as a first-class metadata assignment. We explored how this might look with a real-world
example and discussed trade-offs and scope of the potential addition.

## Packages

The simplest example we came up with was to specify the exact packages present by the
runtime environment. # TODO continue

# TODO: Keys

    "metadata": 
      "dependencies": {
        "python": {
          "packages": [
            "pandas==0.1.0",
            "numpy-mkl==0.21.5",
            "my_external_package"
          ]
        ...
      ...
    ...
    
## Environments

Dependencies could also be scoped by the the environment they resided within.

    "metadata": 
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
