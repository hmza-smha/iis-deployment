# Python FastAPI in NGINX

> if you are in windows, and you want to re-install ubuntu, run ```wsl --list --verbose``` then ```wsl --unregister <distribution-name>```

- ```nginx```: Installs the Nginx web server. Nginx is a popular, high-performance HTTP server, reverse proxy, and load balancer, often used for serving web applications and static content.
- ```sudo```:  This stands for "SuperUser DO" and allows the command to be run with elevated (administrator) privileges.
- ```apt```: This is the package management tool used by Debian-based systems to manage software packages.
- ```update```: This command updates the package index, which is essentially a local cache of the available software versions and repositories. It doesn’t install or upgrade anything but ensures the system has the latest information about available software.
- ```upgrade```: This command upgrades all the installed packages to their latest available versions, according to the updated package index.
- ```-y```: This flag automatically answers "yes" to any prompts that may appear during the upgrade process (such as confirming the installation of new packages or removal of obsolete ones). This prevents the command from pausing to ask for user input.
- ```install```: This tells apt to install the specified packages.

Here's a step-by-step guide to deploying a **FastAPI** app on **Ubuntu** using **Uvicorn**, **Gunicorn**, and **NGINX**.

---

## **1. Update and Upgrade Ubuntu**
```bash
sudo apt update && sudo apt upgrade -y
```

---

## **2. Install Required Packages**
```bash
sudo apt install python3-pip python3-venv nginx -y
```

``` pip3 --version ``` ``` python3 -m venv --help ``` ``` nginx -v ```

---

## **3. Set Up the FastAPI Application**
Navigate to your working directory and create a virtual environment:
```bash
mkdir fastapi_app && cd fastapi_app
python3 -m venv venv
source venv/bin/activate
```

Install **FastAPI** and **Uvicorn**:
```bash
pip install fastapi uvicorn gunicorn
```

Create the **FastAPI app**:
```bash
nano main.py
```
Paste the following code:
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello, FastAPI!"}
```

---

## **4. Test the Application Locally**
Run the app using Uvicorn:
```bash
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```
Visit `http://your-server-ip:8000` to see the response.

---
> you don't need to keep the application running using uvicorn in this step permanently.
> This step is just for testing to ensure that your FastAPI app is working correctly. Once you confirm that it's running properly, stop it by pressing Ctrl + C and proceed with setting up Gunicorn and NGINX for production.
> Once you've set up Gunicorn and started the fastapi.service using systemctl, your application will run as a background service, and you won't need to manually start it with Uvicorn.
---

## **5. Set Up Gunicorn for Production**
Install **Gunicorn**:
```bash
pip install gunicorn
```

> Green Unicorn
> Gunicorn allows you to launch multiple Uvicorn workers to better utilize the available CPU resources and scale your application. For example, with Gunicorn, you can run multiple Uvicorn workers in parallel to handle a large number of incoming requests.
> ```Systemd```: is a system and service manager for Linux operating systems. It is designed to start, stop, and manage system services and resources.
> A Gunicorn systemd service file is a configuration file used to manage and control a Gunicorn (Green Unicorn) web server process on a Linux-based system using systemd, which is the system and service manager for modern Linux distributions.
> 
Create a **Gunicorn systemd service file**:
```bash
sudo nano /etc/systemd/system/fastapi.service
```
Add the following content:
```ini
[Unit]
Description=FastAPI application
After=network.target

[Service]
User=ubuntu
Group=ubuntu
WorkingDirectory=/home/ubuntu/fastapi_app
ExecStart=/home/ubuntu/fastapi_app/venv/bin/gunicorn -w 4 -k uvicorn.workers.UvicornWorker -b 0.0.0.0:8000 main:app

[Install]
WantedBy=multi-user.target
```
**Note**: Replace `ubuntu` with your actual username.

Reload systemd and enable the service:
```bash
sudo systemctl daemon-reload
sudo systemctl start fastapi
sudo systemctl enable fastapi
```

> ```sudo systemctl daemon-reload```: This command is used to reload the systemd manager configuration. It tells systemd to re-read its configuration files (including service files) after changes have been made. This is necessary when you modify or create new service files and want systemd to apply the changes.
> ```sudo systemctl start fastapi```: This command starts the fastapi service. It's used to run a specific service immediately without having to reboot the system. In this case, it would be starting the FastAPI application (which is likely set up as a service on the system).
> ```sudo systemctl enable fastapi```: This command enables the fastapi service to start automatically when the system boots up. By enabling the service, you are telling systemd to launch the FastAPI service during the system startup, ensuring that it runs every time the server reboots without needing to manually start it.

Check the service status:
```bash
sudo systemctl status fastapi
```
>  ```sudo systemctl status fastapi```: is used to check the current status of the fastapi service on your system. It will provide information about whether the service is running, inactive, or if there are any issues.
---

## **6. Set Up NGINX as a Reverse Proxy**
Create a new NGINX configuration file:
```bash
sudo nano /etc/nginx/sites-available/fastapi
```

> ```/etc/nginx/sites-available/fastapi```:  refers to a configuration file for a website or web application being served by Nginx

Add the following configuration:
```nginx
server {
    listen 80;
    server_name your_server_ip;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Enable the configuration:
```bash
sudo ln -s /etc/nginx/sites-available/fastapi /etc/nginx/sites-enabled/
```

> ```ln -s```: This creates a symbolic link (symlink), which is essentially a shortcut or pointer to another file or directory. The -s option ensures that it's a symbolic link rather than a hard link.
> ```/etc/nginx/sites-available/fastapi```: This is the source file, which contains the configuration for your FastAPI application.
> ```/etc/nginx/sites-enabled/```: This is the destination directory. By creating a symlink here, you are telling Nginx to include the configuration file and use it to serve the FastAPI app.

Test the configuration:
```bash
sudo nginx -t
```

> ```nginx -t``` is used to test the Nginx configuration files for syntax errors and other issues.
> It performs a dry run, checking whether the configuration is valid without actually starting or reloading the Nginx server.


Restart NGINX:
```bash
sudo systemctl restart nginx
sudo systemctl enable nginx
```

---

## **7. Allow Firewall Rules**
If **UFW** is enabled, allow HTTP traffic:
```bash
sudo ufw allow 'Nginx Full'
```
Check the status:
```bash
sudo ufw status
```

> ```sudo ufw allow 'Nginx Full'```: is used to configure UFW (Uncomplicated Firewall), a user-friendly front-end for managing firewall rules on Ubuntu and other Linux systems.
> ```ufw```: The command-line tool to manage the firewall.
> ```allow```: Specifies that you're allowing a specific traffic type to pass through the firewall.
> ```'Nginx Full'```: This is a pre-defined application profile in UFW that allows HTTP (port 80) and HTTPS (port 443) traffic. It’s a rule set that opens these two ports for Nginx, which are necessary for serving web traffic.

---

## **8. Test Deployment**
Visit `http://your-server-ip` in your browser. You should see:
```json
{"message": "Hello, FastAPI!"}
```

---

## **9. (Optional) Enable HTTPS with Let's Encrypt**
Install **Certbot**:
```bash
sudo apt install certbot python3-certbot-nginx -y
```
Obtain an SSL certificate:
```bash
sudo certbot --nginx -d your_domain
```
Verify auto-renewal:
```bash
sudo certbot renew --dry-run
```

---

## **Deployment Complete!** 🎉  
Your FastAPI app is now running on Ubuntu with **Gunicorn** and **NGINX** in production. 🚀


---

### Restart the site
``` sudo systemctl restart myapp```

### Stop the Site
```sudo systemctl stop myapp```

### Start the Site
```sudo systemctl start myapp```

### Check status of the Site
```sudo systemctl status myapp```

### Nginx logs:
``` sudo tail -f /var/log/nginx/error.log ```

### FastAPI logs:
``` sudo journalctl -u fastapi --no-pager --lines 50 ```

## Out-Of-Memory (OOM) Error

### Reduce Gunicorn Workers
Each Gunicorn worker might be loading the model separately, consuming more GPU memory. Try reducing the number of workers.

#### Modify your Gunicorn command:

```gunicorn -w 1 -k uvicorn.workers.UvicornWorker app:app```

- ```- w 1```: Limits it to one worker process.

- ```- k uvicorn.workers.UvicornWorker``` : Uses Uvicorn worker for async support.

If this works but slows down responses, consider increasing workers but monitoring GPU usage.

### What Happened?

- Gunicorn, by default, starts multiple worker processes. Each worker loads its own copy of the model, leading to **high memory consumption**.
- Since your GPU has limited memory (19.67 GiB total, with only ~9.56 MiB free at the time of the error), multiple workers caused an **out-of-memory (OOM) error**.
- By reducing the worker count to ```-w 1```, only one instance of the model was loaded into GPU memory, avoiding duplication and freeing up space.
- 
### Why Do More Workers Use More GPU Memory?
- Each worker is an independent process (not threads).
- Unlike CPU models, GPU models don’t share memory across processes. Each worker copies the entire model into the GPU.
- If your model is large, running multiple workers multiplies the memory usage, leading to OOM errors.


## Running an application as a locally service
- Create a systemd Service File
```sudo nano /etc/systemd/system/fastapi-app2.service```

- Paste the following (adjust paths and user as needed):
```
[Unit]
Description=FastAPI App2 (Local Only)
After=network.target

[Service]
User=yourusername
Group=yourusername
WorkingDirectory=/home/yourusername/app2
ExecStart=/home/yourusername/.venv/bin/uvicorn app2:app --host 127.0.0.1 --port 5968
```

- Reload systemd and Start the Service
- ```sudo systemctl daemon-reload```
- ```sudo systemctl enable fastapi-app2```
- ```sudo systemctl start fastapi-app2```

- Test it
```curl http://127.0.0.1:5968/```
