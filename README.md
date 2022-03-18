>CMS (Contest Management System) 
```bash
Linux distributions : Ubuntu 18.04
CMS version 1.4.rc1 
https://github.com/cms-dev/cms/releases/download/v1.4.rc1/v1.4.rc1.tar.gz
```
>Dependencies and available compilers
```bash
# apt install build-essential openjdk-8-jdk-headless fp-compiler \
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
# python3 prerequisites.py install
```
>To install CMS and its Python dependencies on Ubuntu
```bash
# python3 setup.py install
# apt install python3-setuptools python3-tornado python3-psycopg2 \
     python3-sqlalchemy python3-psutil python3-netifaces python3-crypto \
     python3-six python3-bs4 python3-coverage python3-mock python3-requests \
     python3-werkzeug python3-gevent python3-bcrypt python3-chardet patool \
     python3-babel python3-xdg python3-future python3-jinja2 python3-yaml \
     python3-sphinx python3-cups python3-pypdf2
```
