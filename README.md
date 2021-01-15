# Django Learning

Django是一个开放源代码的Web应用框架，由Python写成。

**website.**

- [django官网](https://www.djangoproject.com)    
- [django基础教程——自强学堂](https://code.ziqiangxuetang.com/django/django-tutorial.html)
- [django-rest-framework官网](https://www.django-rest-framework.org/tutorial/quickstart/)
- [python3基础教程——廖雪峰](https://www.liaoxuefeng.com/wiki/1016959663602400)

---

# Install Environment On CentOs6.7

**install python.**

```shell
sudo su -
cd /root
wget http://cdn.npm.taobao.org/dist/python/3.6.5/Python-3.6.5.tgz
tar -zxvf Python-3.6.5.tgz
yum -y install zlib*
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
cd /root/Python-3.6.5/
./configure --prefix=/usr/local/python3 --with-ssl
make
make install
ln -s /usr/local/python3/bin/python3 /usr/bin/python3 
```

**install pip.**

```shell
ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
yum install mysql-devel
yum install python-devel

mkdir /root/.pip/ 
vim /root/.pip/pip.conf  
[global]
index-url = https://mirrors.aliyun.com/pypi/simple/
[install]
trusted-host = mirrors.aliyun.com

pip3 install --upgrade pip
```

**install virtualenv & supervisor.**

```shell
pip3 install virtualenv
pip3 install supervisor
ln -s /usr/local/python3/bin/virtualenv /usr/bin/virtualenv
ln -s /usr/local/python3/bin/supervisorctl /usr/bin/supervisorctl
ln -s /usr/local/python3/bin/supervisord /usr/bin/supervisord
ln -s /usr/local/python3/bin/echo_supervisord_conf /usr/bin/echo_supervisord_conf
echo_supervisord_conf > /etc/supervisord.conf
```

**setup web app.**

```shell
useradd test-service
sudo su - test-service
mkdir /home/test-service/.pip/
vim /home/test-service/.pip/pip.conf
[global]
index-url = http://mirrors.aliyun.com/pypi/simple/
[install]
trusted-host = mirrors.aliyun.com

# 老版本：virtualenv --no-site-packages venv
virtualenv --system-site-packages venv
source venv/bin/activate
# git获取代码：yum install git
pip3 install -r requirements.txt
pip3 install gunicorn
mkdir /data/logs/test-service

vim /etc/supervisord.conf
[program:test-service]
directory=/home/test-service/test-service
command=/home/test-service/venv/bin/gunicorn test-service.wsgi -w4 -b 0.0.0.0:9090
user=test-service
environment=INSTANCE_ENV=PRO
autostart=true
autorestart=true
redirect_stderr=true
stderr_logfile=/tmp/supervisor_stderr.log
stdout_logfile=/tmp/supervisor_stdout.log
```

**supervisord commands.**

```shell
supervisord -c /etc/supervisord.conf
supervisorctl status
supervisorctl update
supervisorctl start test-service
supervisorctl stop test-service
supervisorctl restart test-service
```

# Install Other Utils

**install nginx.**

```shell
yum install git
```

**install nginx.**

```shell
yum install nginx

whereis nginx
nginx: /usr/sbin/nginx /etc/nginx

vim /etc/nginx/nginx.conf
server {
    listen 8080;
    server_name test.com;

    location / {
      try_files $uri $uri/ /index.html;
      root /root/frontend/dists;
      index  index.html index.htm;
    }
}

/usr/sbin/nginx -c /etc/nginx/nginx.conf
nginx -t
nginx -s reload
```

**install redis.**

```shell
yum install redis

whereis redis
redis: /etc/redis.conf

vim /etc/redis.conf
bind 0.0.0.0

service redis start
chkconfig redis on
```

# Other

**upgrade pip in virtualenv.**

在pycharm的virtualenv升级pip版本失败的解决方案：
-- 查看当前pip、setuptools版本
-- 先升级setuptools
-- 再升级pip
失败原因是因为setuptools对pip有版本要求

```shell
pip list
pip install --upgrade setuptools
python -m pip install --upgrade pip
```

**set env.**

设置环境变量，区分线上、测试数据库：
export INSTANCE_ENV=PRO

```shell
# root用户
vim /etc/profile  # 在最后一行加入export INSTANCE_ENV=PRO
source /etc/profile
echo $INSTANCE_ENV

# test-service用户
vim ~/.bashrc  # 在最后一行加入export INSTANCE_ENV=PRO
source ~/.bashrc
echo $INSTANCE_ENV
```

