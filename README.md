# JupyterHubPlus

## Installation steps

### STEP1: Install Jupyterhub

wget https://repo.anaconda.com/archive/Anaconda3-2022.10-Linux-x86_64.sh <br>
chmod 777 Anaconda3-2022.10-Linux-x86_64.sh
./Anaconda3-2022.10-Linux-x86_64.sh

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

http://127.0.0.1:8000/
login here with your Linux account username and password


### STEP3: Create a Configuration file for Jupyterhub and set admin user

create a folder and go to that folder by command line. then generate configuration files for jupyterhub

Generate configuration file

jupyterhub --generate-config

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
c.JupyterHub.authenticator_class = 'native'

finally, add the following lines at the top of the configuration file

import os, nativeauthenticator
c.JupyterHub.template_paths = [f"{os.path.dirname(nativeauthenticator.__file__)}/templates/"]
```

after updating the configuration file start jupyter hub with the updated configuration file on that folder from the commandline

jupyterhub -f jupyterhub_config.py

when  jupyterhub runs, it will provide a link to open in the browser usually 
following: 

http://127.0.0.1:8000/
login here with your Linux account username and password


### STEP4:  User approval:

user will request their access from http://127.0.0.1:8000/

approve user request after login with your linux login username password (as you set it admin user)
from the following URL:

http://127.0.0.1:8081/hub/admin#/



ref of nativeauthenticator: https://native-authenticator.readthedocs.io/en/stable/quickstart.html




