---
title: "PostgreSQL"
typora-root-url: ../
weight: 1
---

# Installation and Configuration
Install PostgreSQL
```
sudo apt install postgresql
```

Connect to our PostgreSQL, modify password for postgres user
```
sudo -u postgres psql template1 
//sudo -u xxx to run the command as xxx user other than the default target user (usually root)
ALTER USER postgres with encrypted password 'your_password'; // in SQL prompt
sudo systemctl restart postgresql.service
```

Test connections from local machines
```
sudo apt install postgresql-client
psql --host your-servers-dns-or-ip --username postgres --password --dbname template1
// e.g., psql --host 127.0.0.1 --username postgres --password --dbname template1
```

# Use gdb to trace the logic of insertion

## Install PGSQL: build from source code with debugging symbols

```
tar -xz -f postgresql-15.2.tar.gz
cd postgresql-15.2/
./configure --enable-debug
make
make install
mkdir -p /usr/local/pgsql/data
chown postgres /usr/local/pgsql/data
su - postgres
/usr/local/pgsql/bin/initdb -D /usr/local/pgsql/data
/usr/local/pgsql/bin/pg_ctl -D /usr/local/pgsql/data -l logfile start
/usr/local/pgsql/bin/createdb test
/usr/local/pgsql/bin/psql test
```

Remark:

- if /usr/local/pgsql/bin/pg_ctl -D /usr/local/pgsql/data -l logfile start failed, check lsof -i tcp:5432, then kill the corresponding PID
- if need to reinstall PGSQL
    ```
    make uninstall
    rm -rf /usr/local/pgsql/data
    rm -rf /usr/local/pgsql
    rm -rf postgresql-15.2/
    tar -xz -f postgresql-15.2.tar.gz
    ```