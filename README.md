Домашняя работа №15 "PAM"

Задание. Запретить всем пользователям, кроме группы admin, логин в выходные и праздничные дни.

Ход работы.

1. Запускаем ВМ и подключаемся к ней:

```
vagrant up
vagrant ssh
```

2. Создаем двух пользователей user1 и user2. 
```
sudo useradd user1

sudo useradd user2
```
3. Назначаем им пароли
```
echo 'Pass1' | sudo passwd --stdin user1
echo 'Pass2' | sudo passwd --stdin user2
```
4. Создаем группу admin
```
sudo group add -f admin
```
5. Добавляем пользователя vagrant и user1 в группу admin

```
sudo usermod user1 -a -G admin
sudo usermod vagrant -a -G admin
```
6. Для реализации запрета подключения по выходным дням создадим скрипт проверки login.sh в каталоге /usr/local/bin/

И изменим права на исполнение файла
```
chmod +x /usr/local/bin/login.sh
```
7. Добавим в файл /etc/pam.d/sshd модуль pam_exec и скрипт
```
#%PAM-1.0
auth       required     pam_sepermit.so
auth       substack     password-auth
auth       include      postlogin
# Used with polkit to reauthorize users in remote sessions
-auth      optional     pam_reauthorize.so prepare
account    required     pam_exec.so /usr/local/bin/login.sh
account    required     pam_nologin.so
account    include      password-auth
password   include      password-auth
# pam_selinux.so close should be the first session rule
session    required     pam_selinux.so close
session    required     pam_loginuid.so
# pam_selinux.so open should only be followed by sessions to be executed in the user context
session    required     pam_selinux.so open env_params
session    required     pam_namespace.so
session    optional     pam_keyinit.so force revoke
session    include      password-auth
session    include      postlogin
# Used with polkit to reauthorize users in remote sessions
-session   optional     pam_reauthorize.so prepare
```
8. Проверяем.
```
hunter@Hunter:~/HW15/HW15_PAM$ date
Вс 29 янв 2023 00:23:10 MSK
hunter@Hunter:~/HW15/HW15_PAM$ ssh user1@192.168.50.10
user1@192.168.50.10's password: 
[user1@pam ~]$ 
[user1@pam ~]$ exit
logout
Connection to 192.168.50.10 closed.
hunter@Hunter:~/HW15/HW15_PAM$ ssh user2@192.168.50.10
user2@192.168.50.10's password: 
/usr/local/bin/login.sh failed: exit code 1
Connection closed by 192.168.50.10 port 22
```
