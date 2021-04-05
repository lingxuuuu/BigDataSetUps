$ sudo usermod -aG docker ${USER}

$ sudo docker pull thebookpeople/oracle-xe-11g

$ sudo docker images

$ sudo docker run -dit -p 1521:1521 thebookpeople/oracle-xe-11g

$ sudo docker ps -a
(to see what docker space is running, and get the container ID)

$ sudo docker exec -it {83d135925e8c-Container ID} /bin/bash

You are now in the root: root@{Container ID}

root@83d135925e8c:/# sqlplus
username:system
password: oracle

You are logged in SQL as system:

SQL> ALTER USER HR ACCOUNT UNLOCK IDENTIFIED BY hr;

You altered the user to HR:

SQL> select * from hr.employees;
(You selected the hr schema)

SQL> exit

root@bbbcd669a929:/# sqlplus
Enter user-name: hr
Enter password: hr

You are logged in as HR:
SQL> select * from employees;
(when you logged as HR, select * from employees is the same with when you logged in as system and select * hr.employees;)

##When you need to reopen your docker

$ systemctl status docker

$ sudo docker ps -a

$ sudo docker exec -it {83d135925e8c-Container ID} /bin/bash

root@83d135925e8c:/# sqlplus
username:system
password: oracle

You are logged in SQL as system:

SQL> ALTER USER HR ACCOUNT UNLOCK IDENTIFIED BY hr;

You altered the user to HR:

SQL> select * from hr.employees;
(You selected the hr schema)

SQL> exit

root@bbbcd669a929:/# sqlplus
Enter user-name: hr
Enter password: hr

You are logged in as HR:
SQL> select * from employees;
(when you logged as HR, select * from employees is the same with when you logged in as system and select * hr.employees;)

To stop and remove container:
$sudo docker stop {container id}
$sudo docker rm {container id}

$sudo docker stop $(docker ps -aq)
$sudo docker rm $(docker ps -aq)

Connect to Dbeaver:
Host: localhost
Database: xe
Username: hr
Password: hr
