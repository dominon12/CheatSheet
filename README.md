# SSH Connection
```bash
ssh <username>@<ip-adress>
```

# Add user
```bash
adduser <username>
```

# Update system
```bash
apt update
apt upgrade
```

# Install sudo
```bash
apt install sudo
```

# Give new user sudo permissions
```bash
usermod -aG sudo <username>
```

# Check if new user in the sudo group
```bash
groups <username>
```

# Change user to new user with sudo rights
```bash
su <new_user_username>
```

# Go to new user’s directory
```bash
cd ~
```

# Install what u need
```bash
sudo apt install nginx git supervisor postgresql python3-venv 
```

# Install RabbitMQ
```bash
sudo apt install erlang rabbitmq-server
```

# Create postgresql db
```bash
sudo service postgresql restart
sudo -u postgres psql
```

```sql
CREATE DATABASE <dbname>;
CREATE USER <username> WITH PASSWORD '<password>';
ALTER ROLE <username> SET client_encoding TO 'UTF8';
ALTER ROLE <username> SET default_transaction_isolation TO 'read committed';
ALTER ROLE <username> SET timezone TO 'UTC';
GRANT ALL PRIVILEGES ON DATABASE <dbname> TO <username>;
ALTER DATABASE <dbname> OWNER TO <username>;
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO <username>;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO <username>;
\q
```

# Pgbouncer
```bash
sudo apt install pgbouncer 
sudo vim /etc/pgbouncer/pgbouncer.ini
```

```bash
* = host=localhost port=5432
pool_mode = transaction
auth_type = md5 
max_client_conn = 1000
admin_users = smokelab
```
```bash
sudo vim /etc/pgbouncer/userlist.txt
```
```bash
"Db_user" "db_pass"
```
```bash
sudo service pgbouncer restart
```
## Check if everything work 
```bash
psql -h localhost -p 6432 -U <username> -d <dbname>
```
```sql
\d
```

## Change port in settings.py from 5432 to 6432

# Clone project repository
```bash
git clone <token>@github.com/
```

# Enter project’s folder
```bash
cd <folder-name>
```

# Create and enter virtual environment
```bash
python3 -m venv venv
source venv/bin/activate
```

# If it’s django-project and u don’t have media and logs folders
```bash
mkdir media
mkdir logs
```

# Install dependencies
```bash
pip install -r requirements.txt
```

# Install additional packages 
```bash
sudo apt install -y make build-essential libssl-dev zlib1g-dev
sudo apt install -y libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm 
sudo apt install -y libncurses5-dev libncursesw5-dev xz-utils tk-dev
```

# Install gunicorn
```bash
pip install gunicorn
```

# Install psycopg2
```bash
pip install psycopg2-binary
```

# In case of error installing psycopg2
```bash
sudo apt-get build-dep python-psycopg2
sudo apt-get build-dep python3-psycopg2 # Python 3
```

# Make db migrations
```bash
python manage.py makemigrations
python manage.py migrate —run-syncdb
```

# Setting up gunicorn
```bash
gunicorn <project-name>.wsgi:application —bind 127.0.0.1:8000 
```

# Setting up NGINX
```bash
sudo vim /etc/nginx/sites-available/default
```

```nginx
server {
	listen 80;
	server_name <domain-or-ip>;
	access_log /var/log/nginx/nginx_logs.log;

	location /static/ {
		root /home/<username>/<project-name>;
		expires 30d;	
	}
	
	location /media/ {
		root /home/<username>/<project-name>;
		expires 30d;	
	}

	location / {
		proxy_pass http://127.0.0.1:8000;
		proxy_set_header Host $server_name;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	}
}
```

```bash
sudo service nginx restart
```

Нa случай ддоса:
```nginx
location /search {
   return 444;
}
```

```bash
export LANGUAGE=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```

# Setting up a supervisor
```bash
cd /etc/supervisor/conf.d/
sudo ln /home/<username>/<project-name>/config/<conf-filename>.conf
sudo update-rc.d supervisor enable
sudo service supervisor start
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl status
```

# To restart after code changing
```bash
sudo supervisorctl restart <project_name>
```

# Setting up RabbitMQ
```bash
sudo rabbitmqctl add_user <username> <password>
sudo rabbitmqctl set_user_tags <username> administartor
sudo rabbitmqctl set_permissions -p / <username> ".*" ".*" ".*"
sudo service rabbitmq-server start/stop/status
```

# Setting up Celery
Add celery.conf with:
```conf
[program:celery]
directory=/home/<username>/<project-name>
command=/home/<username>/venv/bin/celery worker -l info -A <project-name> -n worker1
autostart=true
stdout_logfile=/home/<username>/<project-name>/logs/celery.log
```
```bash
cd /etc/supervisor/conf.d/
sudo ln /home/<username>/<project-name>/config/celery.conf
sudo update-rc.d supervisor enable
sudo service supervisor start
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl status
```

# Configure ssl with let’s encrypt
```bash
sudo apt install snapd
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
sudo certbot renew --dry-run
```

# Memcached
```bash
sudo apt install memcached libmemcached-tools
pip install python-memcached
memcached -vv
```

# To stop process
```bash
ps -ef
ps -ef | grep python
kill -9 2430
```

# To kill workers
```bash
sudo kill -9 <worker_id>
```
# To see list of celery workers
```bash
ps uax | grep celery
```

# Postgresql backup
```bash
pg_dump -h localhost -Fc -o -U smokelab smokelaboratory > _3_nov_2021.dump
pg_restore -U pokyza -d dropshipback -1 8_sept_2021.dump
```
