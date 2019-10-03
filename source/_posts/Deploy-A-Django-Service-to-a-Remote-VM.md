---
title: Deploy A Django Service to a Remote VM
description: This is a summary of an article I've read on how to deploy a Django service to a remote server.
date: 2019-10-02 02:25:54
tags: [Django, Docker, Container]
categories: [Deployment]
---

## Create an User on remote VM
1. login to the remote vm
2. run: `adduser username`
3. add password and details
4. add the user to `sudo` group: `adduser username sudo`


## Update remote VM hostname
1. set new hostname: `hostnamectl set-hostname new_hostname`
2. update the `/etc/hosts` file:
    * open the file with `vim`: `vim /etc/hosts`
    * add this line `vm_ip_address  new_hostname` right below `127.0.0.1      localhost`

## Enable `ssh` Key Based Login
0. create a directory on the remote vm: `mkdir -p ~/.ssh`
1. generate ssh-key on local machine: `ssh-keygen -b 4096`
2. copy the public key to the remote vm: `scp ~/.ssh/id_rsa.pub username@remote_vm_ip:~/.ssh/authorized_keys`
3. login to the vm
4. give `700` permission to the `.ssh` directory
5. give `600` permissions to the contents inside `.ssh` directory

## Update `ssh` Permissions
1. login to the remote vm
2. open `/etc/ssh/sshd_config` with root access
3. Set `PermitRootLogin` to `no`
4. Set `PasswordAuthentication` to `no`
5. Restart `ssh service`: `sudo systemctl restart sshd`

## Setup Firewall: only allow incoming traffic on port 8000 and ssh
1. `sudo apt-get install ufw`
2. `sudo ufw default allow outgoing`
3. `sudo ufw default deny incoming`
4. `sudo ufw allow ssh`
5. `sudo ufw allow 8000`
6. `sudo ufw enable`, yes
7. `sudo ufw status`

## Prep Django App for Deployment
1. activate virtual environment
2. run `pip freeze > requirements.txt` to create requirements file for the virtual enirment
3. copy the project folder to the vm server
4. install `sudo apt-get install python3-pip python3-venv` on the vm
5. create virtual environment: `python3 -m venv django_project/venv`
6. activate the virtual environment
7. install requirement: `pip install -r requirements.txt`

## Update Django Settings in `settings.py`
1.open `settings.py`
2.update `ALLOWD_HOSTS` to include your VM's IP
3.update `STATIC_ROOT = os.path.join(BASE_DIR, 'static')`
4.run `python manage.py collectstatic` to create and copy static files to `static` directory
5.`python manage.py runserver 0.0.0.0:8000`

## Add Django Service to `Apache`
1. Install apache: `sudo apt install apache2`
2. Install apache python lib: `sudo apt install libapache2-mod-wsgi-py3`
3. Configure apache2 services in `/ect/apache2/sites-available`
4. Make a copy of the config file in the config directory: `sudo cp 000-default.conf django_project.conf`
5. Update the config file:
    * Add rules right before `</VirtualHost>`:
        ```
        Alias /static /home/username/django_project_dir/static
        <Directory /home/username/django_project_dir/static>
            Require all granted
        </Directory>
        
        Alias /media /home/username/django_project_dir/media
        <Directory /home/username/django_project_dir/media>
            Require all granted
        </Directory>
        
        <Directory /home/username/django_project_dir/project_dir>
            <Files wsgi.py>
                Require all granted
            </Files>
        </Directory>
        
        WSGIScriptAlias / /home/username/django_project_dir/django_project/wsgi.py
        WSGIDaemonProcess django_app python-path=/home/username/django_project/venv
        WSGIProcessGroup django_app
        ```
6. Enable the site from apache: `sudo a2ensite django_project`
7. Disable the default site: `sudo a2dissite 000-default.conf`
8. Update permission on database and media:
    * `sudo chown :www-data django_project/db.sqlite3`
    * `sudo chmod 664 django_project/db.sqlite3`
    * `sudo chown :www-data django_project`
9. Update permission on the project folder:
    ```
    sudo chown -R :www-data django_project/
    sudo chmod -R 775 django_project/
    ```
10. Create config file in `/etc` called `config.json` or `app_config.json`
11. Copy the `SECRET_KEY` from the project `settings.py` to the `app_config.json`
    ```
    {
        "SECRET_KEY": "SOME KEYS",
        "EMAIL_USER": "your email username",
        "EMAIL_PASS": "your email password"
    }
    ```
12. Update the `settings.py` with following code to load secret key:
    ```
    import json
    with open('/etc/app_config.json') as config_file:
        config = json.load(config_file)
        
    SECRET_KEY = config['SECRET_KEY']
    DEBUG = False
    
    EMAIL_HOST_USER = config.get('EMAIL_USER')
    EMAIL_HOST_PASS = config.get('EMAIL_PASS')
    ```

## Update Traffic Rules
1. `sudo ufw delete allow 8000`
2. `sudo ufw allow http/tcp`
3. restart apache server: `sudo service apache2 restart`


[Deployment checklist](https://docs.djangoproject.com/en/2.2/howto/deployment/checklist/)
