# Install Apache Superset in Arch Linux

## Install Development dependenices
```shell
sudo pacman -S base-devel python python-pip python-wheel 
```

* Install all development packages, when prompted.

* Install Python 3.7, at this time of preparing this document, apache superset will work only python 3.7 or older version. If system has recent version of python, then python 3.7 has to be installed as well.

## Install python 3.7
* Ignore this step, if apache supset fixed the issue with recent version of python 3.7

* Download the source from https://www.python.org/downloads/source/

```shell 
wget https://www.python.org/ftp/python/3.7.10/Python-3.7.10.tar.xz 
tar -xvf Python-3.7.10.tar.xz 
mv Python-3.7.10 python37 
cd python37 
./configure --enable-optimizations --with-ensurepip=install 
make -j 8 
```
* Make step will take quite some time

```shell
 sudo make altinstall
```
* altinstall helps to install python version 3.7 without impacting any other python version exists in the system, if system doesnt have any other python, if we need to use that python for all, use "install" instead of "altinstall"

```shell
python3.7 --version
```
* Make sure python is showing version 3.7

## Create python virtual environment for 3.7 

```shell
python3.7 -m venv superset37
. superset37/bin/activate
python --version
```
* above commands should create virutal environment, and then the version output should be 3.7.x

## Install Apache Superset

```shell
python -m pip install --upgrade pip setuptools

python -m pip install apache-superset

superset db upgrade 

export FLASK_APP=superset

superset fab create-admin

# optional if required to play with some predefined data
superset load_examples 

superset fab create-admin

superset init
# To start a development web server on port 8088, use -p to bind to another port

superset run -p 8088 --with-threads --reload --debugger
```

