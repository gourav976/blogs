# Python Deployment
This readme file will help you to deploy python projects with different frameworks in server.

## Steps Overview:
    1. Create a wsgi or asgi file to run app.
    2. Create a service file.
    3. Create nginx file
    4. Start the serivce

## Let's start Deployment with Service file and Nginx

### 1. Create WSIG or ASGI file
Create a asgi or wsgi python file according to your framework, To know more about wsgi & asgi framework click on me.

**Example** -
```
from project import app

if __name__ == "__main__":
    app.run(host="localhost", port="8080")
```

### 2. Create Service file
A SERVICE file is a service unit file included with systemd, It contains information about how to manage a server application or service, including how to start or stop the service and when it should be automatically started.

Steps to create service file -

#### 2.1 Go to the service file directory
```
cd /etc/systemd/system
```

#### 2.2 Create a file named your-service-name.service
Before moving forward to create service file, first setup your virtual envirnment
and install gunicorn with command ```pip install gunicorn```

```
sudo nano your-service-name.service
```

Add below details in file
```
[Unit]
Description=Gunicorn instance to serve service-name-here
After=network.target

[Service]
User=ubuntu
Group=www-data

# Add working dir path here
WorkingDirectory=/home/ubuntu/{project_dir}

# Added project virtual envirnment path here
Environment="PATH=/home/ubuntu/{project_dir}/venv/bin"

# Add virtual envirant path with gunicorn installed library
ExecStart=/home/ubuntu/{project_dir}/venv/bin/gunicorn --workers 5 --threads 2 --timeout 120 --bind unix:{project_sock_file_name}.sock -m 007 wsgi:app

[Install]
WantedBy=multi-user.target
```

Note: Make sure you will add correct path of Project directory and virtual envirnment.

#### 2.3 Start, enable and check status of the service

Reload the service files to include the new service.
```
sudo systemctl daemon-reload
```

Start, enable and check status of your Service

```
sudo systemctl start your-service-name.service
sudo systemctl enable your-service-name.service
sudo systemctl status your-service-name.service
```

**Note:** If service file status will be success and active then congratulations your service file is created and up and running.

To restart or stop the service 

```
sudo systemctl restart your-service-name.service
sudo systemctl stop your-service-name.service
```

### 3. Create nginx service file
Nginx file will work like a bridge between your domain and your service file.

**NOTE:** Before moving forward, You have to point the server IP with domain from your domain provider.

#### 3.1 Install nginx in server
```
sudo apt update
sudo apt install nginx
```

#### 3.2 Create Nginx service file
```
sudo nano /etc/nginx/sites-available/www.pointed_domain_name_here.com
```

Add below details in the file

```
server {
    #listen 80;
    #listen [::]:80;

    # Add domain name here
    server_name {pointed-domain-name.com};
    
    # Add path of sock file which is created while creating service file.
    location / {
            include proxy_params;
            proxy_pass http://unix:/home/ubuntu/project-directory-path/{project_sock_file_name}.sock;
            proxy_pass_header  Set-Cookie;
            proxy_set_header Host $host;
    }
}
```

#### 3.3 Reload and Restart nginx
```
sudo systemctl reload nginx
sudo systemctl restart nginx
```

#### 3.4 Test nginx configured file
```
sudo nginx -t
```

#### 3.5 Certbot for SSL certificate
To run your website with **https**, Use below command and you will get list of nginx service files then select the one you have created.
```
sudo certbot 
```

**If success then congratulations your domain proxy setup completed successfully.**