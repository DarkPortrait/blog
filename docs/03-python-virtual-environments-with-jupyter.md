# What are Python Virtual Environments
Python virtual environment is a tool that helps to isolate packages by projects. This is useful to keep the list of installed packages clean.
`pip list`

## Installing virtual environment in Ubuntu
Install the python3 virtual environment package:
`sudo apt-get install python3-venv`

## Creating a python virtual environment
It is good practice to create a folder to keep all the environments. In that folder:
`python3 -m venv my-env`

## Activate and deactivate a python virtual environment
Activate the environment using:
`source myenv/bin/activate`

The terminal prompt will show name of the environment. 

To deactivate:
`deactivate`

## Delete a virtual environment
Deleting the virtual environment folder deletes the environment. 
`rm -d my-env`

## List packages in the environment
`pip freeze`
This will do `pip list` but in a format which can be used by the pip tool to install the packages. 
Saving the list to a requirement.txt:
`pip freeze > requirement.txt`

## Replicate packages from an environment
Using pip to install the packages listed in the requirement.txt file:
`pip install -r requirement.txt`

## Using virtual environments in Jupyter Lab
Enable the virtual environment and install the ipykernel package.
`pip install --user ipykernel`

Add the environment in jupyter using:
`python -m ipykernel install --user --name=my-env`

This will show the following in terminal if done correctly.
`Installed kernelspec myenv in /home/user/.local/share/jupyter/kernels/my-env`

This will add a new kernel my-env on the Jupyter Lab welcome page.

## Removing environment from Jupyter
Delete the environment from the environment folder and then remove from the Jupyter lab.
View a list of kernels available:
`jupyter kernelspec list`

Now, to uninstall the kernel, you can type:
`jupyter kernelspec uninstall my-env`

## References
* https://janakiev.com/blog/jupyter-virtual-envs/
* https://www.youtube.com/watch?v=Kg1Yvry_Ydk
