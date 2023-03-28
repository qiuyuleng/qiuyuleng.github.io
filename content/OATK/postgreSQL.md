---
title: "PostgreSQL"
typora-root-url: ../
date: 2023-03-28T16:04:26+08:00
draft: true
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