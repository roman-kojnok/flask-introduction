

September 15, 2022	    9:16 AM	    from Roman to everyone:	https://devops-hub-group.slack.com/archives/C03RZM65F2S/p1663229646067369

September 15, 2022	    10:38 AM	    from QAWebex153 to everyone:	netstat -tulpn  |  grep 22

September 15, 2022	    12:36 PM	    from Steven Borley to everyone:	set nu

September 15, 2022	    2:11 PM	    from Steven Borley to everyone:	su - root

September 15, 2022	    2:22 PM	    from Steven Borley to everyone:	nano /lib/systemd/system/app.service


September 15, 2022	    2:33 PM	    from Steven Borley to everyone:	chmod +x /usr/bin/app.sh
 in terminal

September 15, 2022	    2:34 PM	    from Steven Borley to everyone:	nano /lib/systemd/system/app.service

[Unit]
Description=Application Service

[Service]
ExecStart=/usr/bin/app.sh

[Install]
WantedBy=multi-user.target



September 15, 2022	    2:37 PM	    from Tufael to everyone:	systemctl daemon-reload

September 15, 2022	    2:37 PM	    from Tufael to everyone:	in terminal 

# 1) installation services
login as root
apt-get update -y
python3 --version
apt-get install python3-venv -y
apt-get install nginx -y
systemctl start nginx
systemctl enable nginx
---------------------------------
2) Create Virtual environment for flask app
mkdir /root/project
cd /root/project
python3 -m venv venv
source venv/bin/activate
pip install wheel
pip install gunicorn flask           
-----------------------------------------
3) Create Flask Application
nano /root/project/flaskapp.py
from flask import Flask
app = Flask(__name__)
@app.route("/")
def hello():
    return "Welcome to Flask Application!"
if __name__ == "__main__":
    app.run(host='0.0.0.0')
---------------------------------
4) nano /root/project/wsgi.py
from flaskapp import app
if __name__ == "__main__":
    app.run()
-------------
gunicorn --bind 0.0.0.0:5000 wsgi:app
--------------
deactivate
---------------
# Create systemd services
nano /etc/systemd/system/flaskapp

# 1) installation services

login as root
apt-get update -y
python3 --version
apt-get install python3-venv -y
apt-get install nginx -y
systemctl start nginx
systemctl enable nginx
---------------------------------
2) Create Virtual environment for flask app
mkdir /root/project
cd /root/project
python3 -m venv venv
source venv/bin/activate
pip install wheel
pip install gunicorn flask           
-----------------------------------------
3) Create Flask Application
nano /root/project/flaskapp.py
from flask import Flask
app = Flask(__name__)
@app.route("/")
def hello():
    return "Welcome to Flask Application!"
if __name__ == "__main__":
    app.run(host='0.0.0.0')
---------------------------------
4) nano /root/project/wsgi.py
from flaskapp import app
if __name__ == "__main__":
    app.run()
-------------
gunicorn --bind 0.0.0.0:5000 wsgi:app
--------------
deactivate
---------------
Create systemd services
nano /etc/systemd/system/flaskapp

# 1) installation services
login as root
apt-get update -y
python3 --version
apt-get install python3-venv -y
apt-get install nginx -y
systemctl start nginx
systemctl enable nginx

# 2) Create Virtual environment for flask app
mkdir /root/project
cd /root/project
python3 -m venv venv
source venv/bin/activate
pip install wheel
pip install gunicorn flask    

# 3) Create Flask Application
nano /root/project/flaskapp.py
from flask import Flask
app = Flask(__name__)
@app.route("/")
def hello():
    return "Welcome to Flask Application!"
if __name__ == "__main__":
    app.run(host='0.0.0.0')
---------------------------------
4) nano /root/project/wsgi.py
from flaskapp import app
if __name__ == "__main__":
    app.run()
-------------
gunicorn --bind 0.0.0.0:5000 wsgi:app
--------------
deactivate

# Create systemd services
nano /etc/systemd/system/flaskapp.service
[Unit]
Description=Gunicorn instance to serve Flask
After=network.target
[Service]
User=root
Group=www-data
WorkingDirectory=/root/project
Environment="PATH=/root/project/venv/bin"
ExecStart=/root/project/venv/bin/gunicorn --bind 0.0.0.0:5000 wsgi:app
[Install]
WantedBy=multi-user.target
-----------------------
chmod 777 /root/project
-------------------------
systemctl daemon-reload
systemctl start flaskapp.service
systemctl enable flaskapp.service
------------
systemctl status flaskapp.service

# Nginx as a reverse proxy to serve the Flask application 
nano /etc/nginx/conf.d/flask.conf
server {
    listen 80;
    server_name flaskserver;
    location / {
        include proxy_params;
        proxy_pass  http://127.0.0.1:5000;
    }
}
----------------------------
# verify
nginx -t
systemctl restart nginx
curl http://flaskserver

