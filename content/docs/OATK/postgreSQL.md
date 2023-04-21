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

```
\\Terminal 1:
psql -d test -h localhost -U postgres
insert into t_insert values(1,'11','12','13');
select pg_backend_pid(); \\ get PID of pgsql process

\\Teriminal 2:
gdb -p PID_from_terminal_1 \\Start gdb, attach to the pgsql process
b PageAddItemExtended \\ set breakpoint

\\Terminal 1:
insert into t_insert values(2,'11','12','13');

\\Teriminal 2:
c //Continuing run till the breakpoint. Breakpoint 1, show the first line of function PageAddItemExtended
bt // print the call stack
next //single-step debugging
```

GDB常用指令备忘录 https://www.jianshu.com/p/6cdd79ed7dfb


pg_stat_statements

Install process:

- CREATE EXTENSION pg_stat_statements;
    - if failed: ERROR:  could not open extension control file "/usr/pgsql-13/share/extension/pg_stat_statements.control": No such file or directory
        - Solution 1: when installing PGSQL. use make world, make install-world 
        - Solution 2: cd src/contrib/pg_stat_statements/; make; make install
- SHOW config_file; Show the location of postgresql.conf
- At the bottom of postgresql.conf, add 
    ```
    shared_preload_libraries = 'pg_stat_statements'
    pg_stat_statements.track = all
    pg_stat_statements.max = 10000
    pg_stat_statements.track_utility = true
    ```
- Restart PostgreSQL: sudo systemctl restart postgresql

- SELECT * FROM pg_stat_statements

- if failed: ERROR: pg_stat_statements must be loaded via shared_preload_libraries

    - lsof -i tcp:5432, then kill the corresponding PID