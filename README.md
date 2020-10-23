[![N|Solid](https://www.fullstackpython.com/img/logos/ansible-wide.png)](https://docs.ansible.com/)

### Описание
В данном репозитории представлен сценарий для автоматической установки следующих  компонентов:
  - zabbix (zabbix-agent 5.0) debian 7 (zabbix-agent 4.4)
  - puppet (puppet-agent 6) debian 7 (puppet-agent 5)
  - nagios 
  - rdiff-backup (1.3.3)
 
| file/dir | description |
| ------ | ------ |
| ansible.cfg |конфигурационный файл ansible |
| hosts | файл инвентаризации хостов |
| ora.yml | файл сценария |
| roles | директория с ролями (zabbix,nagios,puppet,rdiff-backup) |

Все аспекты ролей определяются в директории roles в следующих файлах: 
```
.
|-- nagios
|   |-- handlers
|   |   `-- main.yml
|   |-- tasks
|   |   |-- main.yml
|   |   |-- setup-Debian.yml
|   |   `-- setup-RedHat.yml
|   |-- templates
|   |   |-- hoster.j2
|   |   `-- hoster_centos.j2
|   `-- vars
|       |-- Debian.yml
|       |-- RedHat.yml
|       `-- main.yml
|-- puppet
|   |-- handlers
|   |   `-- main.yml
|   |-- tasks
|   |   |-- main.yml
|   |   |-- setup-CentOS.yml
|   |   |-- setup-Debian.yml
|   |   `-- setup-Ubuntu.yml
|   |-- templates
|   |   `-- puppet.j2
|   `-- vars
|       `-- main.yml
|-- rdiff-backup
|   |-- files
|   |   `-- rdiff-backup-1.3.3.tar.gz
|   `-- tasks
|       |-- main.yml
|       |-- setup-Debian.yml
|       `-- setup-RedHat.yml
`-- zabbix
    |-- handlers
    |   `-- main.yml
    |-- tasks
    |   |-- main.yml
    |   |-- setup-CentOS.yml
    |   |-- setup-Debian.yml
    |   `-- setup-Ubuntu.yml
    |-- templates
    |   `-- zabbix_agentd.j2
    `-- vars
        `-- main.yml
```
##### 
Перед запуском манифеста редактируем файл hosts и добавляем IP адреса сервером для которых необходимо выполнить данный сценарий ( установка - zabbix - puppet- nagios - rdiff-backup )
```sh
[Administration]
167.99.138.74
167.99.138.75
```
Далее открываем файл ora.yml и комментируем (если надо) роли которые мы бы не хотели устанавливать
```sh
- name: Start install
  hosts: Administration
  become: yes
  
  roles:
    - role: zabbix
      Hostname: "{{ ansible_hostname }}"  #example: hoster.by | default hostname  
      HostMetadata: admin_hoster          #default test_hoster
    - role: puppet
    - role: nagios
    # - role: rdiff-backup
```
Также мы можем отредактировать директиву HostMetadata если хотим чтобы при установки zabbix-agent(а) сервер добавился в нужную группу при авторегистрации
```
test_hoster
admin_hoster
```
### Установка ansible
https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

### Запуск сценария
Скачиваем репозиторий
```
git clone https://github.com/ppflatland/ora.git
```
! Важно
> Перед запуском сцерия ssh ключи должны быть проброшены на удалённый сервер (сервер на котором будут устанавливаться необходимые компоненты)

Запускаем
```
ansible-playbook ora.yml
```
### Поддерживаемые дистрибутивы:
- Centos 8, 7, 6
- Ubuntu 20, 18, 16, 14
- Debian 10, 9, 8, 7
