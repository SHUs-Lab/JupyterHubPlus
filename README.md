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
After installation run Jupyterhub with the following command with the command line
```shell
jupyterhub
```
When Jupyterhub runs, it will provide a link to open in the browser usually 
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
First, run the following command in the command line

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
write following line in kernel.json. you have to update the cuda path in the following line... <br> Check the cuda path with "which nvcc" command
```shell
{"argv": ["python3", "-m", "jupyter_c_kernel", "-f", "{connection_file}"], "display_name": "Cuda-C", "language": "Cuda-C", "env": { "PATH":"/usr/local/cuda-11.7/bin/:$PATH", "LD_LIBRARY_PATH":"/usr/local/cuda-11.7/lib64"}}
```

### STEP2: Cuda C kernel installation

download source code from the current repository <br>
update nvcc path (which nvcc) in the kernel.py file in the following location <br>
jupyter-c-kernel/jupyter_c_kernel <br>
```shell
git clone https://github.com/SHUs-Lab/JupyterHubPlus.git
cd JupyterHubPlus/jupyter-c-kernel/jupyter_c_kernel
edit kernel.py and update nvcc path in the following lines
args = ['/usr/local/cuda-11.7/bin/nvcc', source_filename]  + ['-o', binary_filename ]
os.environ["PATH"] = os.environ["PATH"]+":/usr/local/cuda-11.7/bin/" 
```


then go to parent folder jupyter-c-kernel, and run following command

```shell
cd JupyterHubPlus/jupyter-c-kernel
pip install -e .
```
then run the following command in the command line
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
Now run the Jupyterhub. you will get Cuda C option 
<br>
jupyter-c-kernel code forked from https://github.com/brendan-rius/jupyter-c-kernel.git


## Monitoring with JupyterHub


### Update Roles in JupyterHub configuration file
JupyterHub provides role based access control RBAC (Role Based Access Control) <br>
Roles are **collections of scopes** that specify the level of what a client is allowed to do.<br>
A **scope** has a syntax-based design that reveals which resources it provides access to.<br>
Example: 
```
c.JupyterHub.load_roles = [
  {
    'name': 'teacher-role',
    'scopes': [
      # access to the admin page
      'admin-ui',
      # list users in the class group
      'list:users!group=student',
      # access servers for users in the class
      'access:servers!group=student',
    ],
    'groups': ['teacher'],
  }
]
```
The details of setting role and scope are available in the link: https://jupyterhub.readthedocs.io/en/stable/rbac/index.html <br>
After adding/updating the roles in Jupyterhub configuration file, it requires to <br>restart <br> the jupyterhub to get the updated access previliges. <br>
The groups are come from the created group (following section) from administrator profile.

### Manage group from adminstrator profile
The Group information is managed from the User interface. The admin user have the previleges to manage group and assign user to group. <br>
when a user request access, admin authorize the user from following url<br>
http://127.0.0.1:8081/hub/authorize
![image](https://github.com/SHUs-Lab/JupyterHubPlus/assets/18241223/65bb0371-5b32-4b51-9238-bc5844ed3773)

<br>
After that admin need to add that user from the following url <br>
http://127.0.0.1:8081/hub/admin#/ <br>

![image](https://github.com/SHUs-Lab/JupyterHubPlus/assets/18241223/9c5e2d31-ad03-464a-8397-13b891977cd7)

<br>
after adding user admin needs to assign that user in any group from the button at right side 
<br>
admin can create group or manage group from here
<br>  

![image](https://github.com/SHUs-Lab/JupyterHubPlus/assets/18241223/cff17bc0-7469-48b1-a896-7e84414185bd)

<br>

addmin can assign new user to a group <br>

![image](https://github.com/SHUs-Lab/JupyterHubPlus/assets/18241223/37dfa3ca-1f33-4dc9-9ff3-eb082cc51c15)

### Access student(a) group infromation from teacher(another) group
Any user from teacher group needs to login from the following url <br>
http://127.0.0.1:8000/hub/login
<br>

After login, he needs to select Jupyterhub control panel from the file menu <br>

![image](https://github.com/SHUs-Lab/JupyterHubPlus/assets/18241223/64d560fa-2c69-4ef2-a3c7-227da0c22146)

<br>

after that he needs  ot select admin and see following page <br>
![image](https://github.com/SHUs-Lab/JupyterHubPlus/assets/18241223/f2333c98-e834-494b-8c8d-3197c52c31c4)

<br>

By selecting access server he can access files from any user of the list. 








