# psycopg2-builds
Precompiled builds for psycopg2 for different Python versions and OS

Amazon Lambda and some other "closed" systems requires to provide already built versions for custom Python packages.

While with most of other packages, it's simple enough to execute

```
pip3 install  --install-option="--prefix=" {package_name} -t {custom_folder}/
```

the psycopg2 requires many lbraries and binary files that prevents it from working.

The solution, is to use already precomoiled versions.

### This repo contains psycopg with SSL enabled!

This repository contains:

- psycopg2 2.7.5 for Python 3.6 that has built on amzn1 (suitable for AWS Lambda)
- psycopg2 2.7.5 for Python 2.7 that has built on amzn1 (sutable for AWS Lambda)
- psycopg2 2.7.5 for Python 3.6 that has built on Ubuntu 18
- psycopg2 2.7.5 for Python 2.7 that has built on Ubuntu 18


## How to use

Just copy the required version and rename it to `psycopg`, e.g.

```
svn export --force https://github.com/gakhov/psycopg2-builds/trunk/psycopg2-2.7.5-py2.7-amzn1_64/ {my_folder}/psycopg2
```

P.S. Yes, `svn`. It can help you to avoid downloading the whole repo, instead of one requried folder.

## How to reproduce

I used the approach of https://github.com/jkehler/awslambda-psycopg2

- Choose the appropriate OS
- Install required packages (if missing)

```
sudo yum install openssl openssl-devel zlib1g-devel
# Debian/Ubuntu: sudo apt-get install openssl libssl-dev zlib1g-dev
```

- Download the PostgreSQL and psycopg2 sources

```
mkdir /tmp/my_psycopg2
cd /tmp/my_psycopg2
get http://initd.org/psycopg/tarballs/PSYCOPG-2-7/psycopg2-2.7.5.tar.gz
wget https://ftp.postgresql.org/pub/source/v9.6.9/postgresql-9.6.9.tar.gz
tar -xvf postgresql-9.6.9.tar.gz
tar -xvf psycopg2-2.7.5.tar.gz
cd postgresql-9.6.9/
```

- Configure and install locally the PostgreSQL

```
./configure --prefix /tmp/my_psycopg2 --without-readline --without-zlib --with-openssl
make
make install
```

- Build the psycopg2 (use the right python)

```
cd ../
cd psycopg2-2.7.5
vim setup.cfg:
    pg_config=/tmp/my_psycopg2/bin/pg_config
    static_libpq=1
    libraries=ssl crypto

python3 setup.py build
```

- In the `build` folder you will find the precompiled version, e.g.

```
build/lib.linux-x86_64-3.6/psycopg2/
```
