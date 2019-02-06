# Breakout group result

## docker2kernel

We propose a convention for creating repos to house containerized kernels

We would have a scaffolding script that creates:

project_name/
   requirements.txt # pip freeze
   Dockerfile # perhaps through repo2docker + custom scripts
   notebooks/ # empty folder
   kernelspecs/
      project_name/
         kernel.json
Project Layout:

project_name/
  requirements.txt
  Dockerfile
  notebooks/
    notebook1.ipynb
  kernelspecs/
    project_name/
      kernel.json
Kernel.json would be configured to launch the container described in Dockerfile..

kernelspecs installed via jupyter kernelspec install

The Dockerfile would have an ENTRYPOINT that launches the python shell with appropriate file after copying pip -r requirements.txt into the container.
