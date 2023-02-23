---
title: "Ansible"
typora-root-url: ../
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

# Inventory file
alias(别名)

ansible_host=IP/FQDN(fully qualified domain name, 完整域名)

ansible_connection=ssh/winrm/localhost

ansible_port=22(default for ssh)

ansible_user=root/administrator

ansible_ssh_pass(ansible_password for win)=password



[group_name1]

Groupmember_alias1 or host



[group_name2]

Groupmember_alias2 or host



[group_name3:children] 继承

group_name1

group_name2

# YAML
Key :(colon) value

冒号后面一定要加空格！

![image-20230223215613603](/../img/image-20230223215613603.png)

![image-20230223215629409](/../img/image-20230223215629409.png)

![image-20230223215637877](/../img/image-20230223215637877.png)

![image-20230223215647804](/../img/image-20230223215647804.png)

Dictionary: unodered

List: ordered

# Ansible playbook

.yaml/.yml 格式的文件

Run ansible playbooks:

1. ansible <hosts> -? (module) <command> -i inventory.txt
   1. e.g., ansible target -m ping -i inventory.txt
   2. run command one by one
2. Ansible-playbook <playbook name> -i inventory.txt
   1. e.g., ansible-playbook test.yaml -i inventory.txt

# Ansible Module


各个module要查文档 ansible module，看文档的话，在ansible.builtin页面检索module名字，然后点进去看

第一个大表格是各个parameter
第二个大表格是attributes，还不知道是干什么用的
后面的例子也可以看一眼	

- 要怎么知道是用的哪个module，用这个module里的哪些parameter啊?

idempotency: 一个操作执行一遍和执行多遍(中间没有其他操作)的结果一样:

- started stopped: 保证机器是在一个特定的状态

# Ansible Variables

{{ variable name }}

![image-20230223231716089](/../img/image-20230223231716089.png)

To define variables in a playbook, using "vars" module

![image-20230223231754091](/../img/image-20230223231754091.png)

# Ansible Conditionals

when:相当于if，里面可以用and，or
register是用来储存当前指令的输出的，利用register，用command output来储存这个command的输出，来用在下一个task里面
An example:
![image-20230223231814820](/../img/image-20230223231814820.png)

![image-20230223231825203](/../img/image-20230223231825203.png)

# Ansible Loops

Loop/with_*(古老版本的)

{{ item }}指代loop的变量

![image-20230223231839476](/../img/image-20230223231839476.png)

# Ansible Roles

To make work reusable; 每次部署服务器(比如database server)所需养的步骤 (playbook中的代码)都差不多，为了提升代码复用性，把这些代码放在role文件中，然后直接用role文件
e.g:

![image-20230223231848855](/../img/image-20230223231848855.png)

Ansible galaxy https://galaxy.ansible.com/

- Find pre-package roles in ansbie galaxy, such as installing and configuring web servers, database servers and so on
- 用法
  - ansibie-galasy init mysql 会自动有一个mysql的文件夹
  - 方法一:将这个文件夹放到playbook/roles目录下面
    - ![image-20230223231908926](/../img/image-20230223231908926.png)
  - 方法二	/etc/ansible/roles (default path)	
  - 方法三: 指定路径roles_path=...

# Advanced Topics

1. how to run on win servers
2. Ansible galaxy
3. patterns
  1. hosts: host1,host2,host3 或 group1, host1 或 host*等
4. dynamic inventory
  1. Ansible-playbook -i inventory.py
5. custom moduies
  1. refer to the ansible documents
6. deploy a task manually