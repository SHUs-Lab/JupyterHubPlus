# JupyterHubPlus

## JupyterHub Installation steps

### STEP1: Install Jupyterhub
```shell
wget https://repo.anaconda.com/archive/Anaconda3-2022.10-Linux-x86_64.sh 
chmod 777 Anaconda3-2022.10-Linux-x86_64.sh
./Anaconda3-2022.10-Linux-x86_64.sh
```
### STEP2: Install libraries required
```shell
pip install ruamel-yaml
pip install pycosat
pip install jupyterhub-nativeauthenticator
pip install sudospawner
```
After installation run jupyterhub with the following command with command line
```shell
jupyterhub
```
when  jupyterhub runs, it will provide a link to open in the browser usually 
following: 

http://127.0.0.1:8000/ <br>
login here with your Linux account username and password


### STEP3: Create a Configuration file for Jupyterhub and set admin user

create a folder and go to that folder by command line. then generate configuration files for Jupyterhub

Generate configuration file
```shell
jupyterhub --generate-config
```
a file jupyterhub_config.py will be created in that folder. Open that configuration file and modify the following lines
```shell
replace 
# c.JupyterHub.spawner_class = 'jupyterhub.spawner.LocalProcessSpawner'   
with 
c.JupyterHub.spawner_class = 'jupyterhub.spawner.SimpleLocalProcessSpawner'

replace
# c.Authenticator.admin_users = set()
with
c.Authenticator.admin_users = {'your linux login username'}   


replace 
# c.JupyterHub.authenticator_class = 'jupyterhub.auth.PAMAuthenticator'
with
c.JupyterHub.authenticator_class = 'nativeauthenticator.NativeAuthenticator'

finally, add the following lines at the top of the configuration file

import os, nativeauthenticator
c.JupyterHub.template_paths = [f"{os.path.dirname(nativeauthenticator.__file__)}/templates/"]
```

after updating the configuration file start Jupyterhub with the updated configuration file on that folder from the command line
```shell
jupyterhub -f jupyterhub_config.py
```
When Jupyterhub runs, it will provide a link to open in the browser usually 
following: 
<br>
http://127.0.0.1:8000/
<br>
login here with your Linux account username and password


### STEP4:  User approval:

user will request their access from 
<br>
http://127.0.0.1:8000/
<br>
approve user request after login with your linux login username password (as you set it admin user)
from the following URL:
<br>
http://127.0.0.1:8081/hub/admin#/
<br>


ref of nativeauthenticator: https://native-authenticator.readthedocs.io/en/stable/quickstart.html

## Cuda C with JupyterHub

### STEP1: Install required libraries and update kernelspec
First, run the following command in command line

```shell
pip install ipykernel
pip install jupyter-client 
jupyter kernelspec list
```
you will find output like this
```shell
Available kernels:
  python3    /home/tk0432@unt.ad.unt.edu/anaconda3/share/jupyter/kernels/python3
```

now go to the folder kernels in your computer(path get from the above line -- /home/tk0432@unt.ad.unt.edu/anaconda3/share/jupyter/kernels).<br> Then create a folder Cuda-C. <be> within that folder create file kernel.json

```shell
cd /home/tk0432@unt.ad.unt.edu/anaconda3/share/jupyter/kernels/
mkdir Cuda-C
```
write following line in kernel.json. you have to update the cuda path in the following line... <br>check the cuda path with "which nvcc" command
```shell
{"argv": ["python3", "-m", "jupyter_c_kernel", "-f", "{connection_file}"], "display_name": "Cuda-C", "language": "Cuda-C", "env": { "PATH":"/usr/local/cuda-11.7/bin/:$PATH", "LD_LIBRARY_PATH":"/usr/local/cuda-11.7/lib64"}}
```

### STEP2: Cuda C kernel installation

download source code from the current repository <br>
update nvcc path (which nvcc) in the kernel.py file in the following location <br>
jupyter-c-kernel/jupyter_c_kernel <br>

then in parent folder jupyter-c-kernel, and run following command
```shell
pip install -e .
```
then run following command in command line
```shell
jupyter kernelspec list
```
you will get output like this
<br>
```shell
Available kernels:
  cuda-c     /home/tk0432@unt.ad.unt.edu/anaconda3/share/jupyter/kernels/Cuda-C
  python3    /home/tk0432@unt.ad.unt.edu/anaconda3/share/jupyter/kernels/python3
```
Now run the jupyter hub. you will get Cuda C option 
<br>
jupyter-c-kernel code forked from https://github.com/brendan-rius/jupyter-c-kernel.git




