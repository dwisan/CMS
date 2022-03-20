>CMS (Contest Management System) 
```bash
Linux distributions : Ubuntu 18.04
CMS version 1.4.rc1 
https://github.com/cms-dev/cms/releases/download/v1.4.rc1/v1.4.rc1.tar.gz
INTERFACE Adress : 172.18.111.203
```
>Dependencies and available compilers
```bash
# sudo apt install build-essential openjdk-8-jdk-headless fp-compiler \
    postgresql postgresql-client python3.6 cppreference-doc-en-html \
    cgroup-lite libcap-dev zip texlive-latex-base a2ps haskell-platform \
    rustc mono-mcs
```
>Download CMS 1.4.rc1 from GitHub as an archive, then extract it on your filesystem
```bash
# wget https://github.com/cms-dev/cms/releases/download/v1.4.rc1/v1.4.rc1.tar.gz
# tar xvfz v1.4.rc1.tar.gz
```
>Preparation steps
```bash
# cd cms
# sudo python3 prerequisites.py install
Type Y if you want me to automatically add "{SUDO USER}" to the cmsuser group: Y
```
>To install CMS and its Python dependencies on Ubuntu
```bash
# sudo apt install python3-setuptools python3-tornado python3-psycopg2 \
     python3-sqlalchemy python3-psutil python3-netifaces python3-crypto \
     python3-six python3-bs4 python3-coverage python3-mock python3-requests \
     python3-werkzeug python3-gevent python3-bcrypt python3-chardet patool \
     python3-babel python3-xdg python3-future python3-jinja2 python3-yaml \
     python3-sphinx python3-cups python3-pypdf2
# sudo python3 setup.py install
```
> Change PostgreSQL accept the connection from different hosts
```bash
 # on /etc/postgresql/10/main/postgresql.conf
 listen_addresses = '127.0.0.1,172.18.111.203'
 
 # on /etc/postgresql/10/main/pg_hba.conf adding a line like
 host  cmsdb  cmsuser  172.18.111.0/24  md5
 
 # restart postgreSQL
 # sudo service postgresql restart
```
>Configuring the DB
```bash
# sudo su - postgres
---Create user for CMS
# createuser --username=postgres --pwprompt cmsuser

---Create Database for CMS
# createdb --username=postgres --owner=cmsuser cmsdb

---Grant roles for user to database
# psql --username=postgres --dbname=cmsdb --command='ALTER SCHEMA public OWNER TO cmsuser'
# psql --username=postgres --dbname=cmsdb --command='GRANT SELECT ON pg_largeobject TO cmsuser'

---Change Config in  /usr/local/etc/cms.conf
 in "_section": "Database",
"database": "postgresql+psycopg2://cmsuser:**CHANGE_PASSWORD**@172.18.111.203:5432/cmsdb"

---Create the database schema for CMS
# cmsInitDB
```
> Configuring CMS
```bash
 # on /usr/local/etc/cms.conf
 change "core_services" replace localhost to 172.18.111.203
 change Listening address to 172.18.111.203
 change secret_key in WebServer _section with result 
    # cd cms & python3 -c 'from cmscommon import crypto; print(crypto.get_hex_random_key())'
 
 change "contest_listen_address": [""] to "contest_listen_address": ["172.18.111.203"]
 change "admin_listen_address": "" to "admin_listen_address": "172.18.111.203"
 change {username}:{password} in "rankings": ["http://{username}:{password}@172.18.111.203:8890/"] as same as /user/local/etc/cms.ranking.conf
 
 # on /user/local/etc/cms.ranking.conf
 change "bind_address": "" to "bind_address": "172.182.111.203"
 change "username":   "{CHANGE ME}",
 change "password":   "{CHANGE ME}",
```
> Create systemd services for Services Running
```bash
--- Create systemd service for cmsLogService
# sudo touch /etc/systemd/system/logioi.service
# sudo nano /etc/systemd/system/logioi.service

[Unit]
Description=ioi service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=wisan
ExecStart=/usr/local/bin/cmsLogService

[Install]
WantedBy=multi-user.target

--- Create systemd Ranking Service
# sudo touch /etc/systemd/system/ranking.service
# sudo nano /etc/systemd/system/ranking.service

[Unit]
Description=ioi service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=wisan
ExecStart=/usr/local/bin/cmsRankingWebServer

[Install]
WantedBy=multi-user.target

---Create systemd service for ResourceService 
# sudo touch /etc/systemd/system/ioi.service
# sudo nano /etc/systemd/system/ioi.service

[Unit]
Description=ioi service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=wisan
ExecStart=/usr/local/bin/cmsResourceService -a 1

[Install]
WantedBy=multi-user.target
```
> Running CMS
```bash
# sudo systemctl daemon-reload
# sudo systemctl start logioi.service
# sudo systemctl start ranking.service
--- Adding admin user for AdminWebServer
# sudo cmsAddAdmin wisan
# sudo cmsAdminWebServer
result: username wisan and password {Password Generated}
---Create Contest first shard number #1 on AdminWebServer : http://172.18.111.202:8889
---change shard number of contest in /etc/systemd/system/ioi.service at last digit 

ExecStart=/usr/local/bin/cmsResourceService -a 1 

# sudo systemctl daemon-reload
# sudo systemctl start ioi.service
```
> Howto Use
```bash
AdminWebServer : http://172.18.111.202:8889
ContestWebServer : http://172.18.111.202:8888
Ranking : http://172.18.111.202:8890
```
