# Домашнее задание к занятию 4 «Работа с roles»

## Подготовка к выполнению

1. * Необязательно. Познакомьтесь с [LightHouse](https://youtu.be/ymlrNlaHzIY?t=929).
2. Создайте два пустых публичных репозитория в любом своём проекте: vector-role и lighthouse-role.
3. Добавьте публичную часть своего ключа к своему профилю на GitHub.

## Основная часть

Ваша цель — разбить ваш playbook на отдельные roles. 

Задача — сделать roles для ClickHouse, Vector и LightHouse и написать playbook для использования этих ролей. 

Ожидаемый результат — существуют три ваших репозитория: два с roles и один с playbook.

**Что нужно сделать**

1. Создайте в старой версии playbook файл `requirements.yml` и заполните его содержимым:

   ```
   ---
     - src: git@github.com:AlexeySetevoi/ansible-clickhouse.git
       scm: git
       version: "1.13"
       name: clickhouse 
   ```

2. При помощи `ansible-galaxy` скачайте себе эту роль.

Создал файл `requirements.yml` и загрузил себе эту роль: `ansible-galaxy install -r requirements.yml -p roles`

3. Создайте новый каталог с ролью при помощи `ansible-galaxy role init vector-role`.

Создл две роли: `ansible-galaxy role init vector-role` и `ansible-galaxy role init lighthouse-role`

4. На основе tasks из старого playbook заполните новую role. Разнесите переменные между `vars` и `default`.

**vector-role**

```
---
# vars file for vector-role
vector_workdir: "/home/ubuntu/vector"
vector_os_user: "vector"
vector_os_group: "vector"
```

```
---
# defaults file for vector-role
vector_version: "0.21.1"
vector_os_arch: "x86_64"
```

**lighthouse-role**

```
---
# defaults file for lighthouse-role
lighthouse_vcs: https://github.com/VKCOM/lighthouse.git
lighthouse_dir: /var/lib/lighthouse
nginx_user_name: root
```

5. Перенести нужные шаблоны конфигов в `templates`.

`vector-role/templates/vector.toml`
`lighthouse-role/templates/lighthouse.conf`
`lighthouse-role/templates/nginx.conf`

6. Опишите в `README.md` обе роли и их параметры. Пример качественной документации ansible role [по ссылке](https://github.com/cloudalchemy/ansible-prometheus).
7. Повторите шаги 3–6 для LightHouse. Помните, что одна роль должна настраивать один продукт.

Также отредактировал meta-файл в каждой роли. Перенес все task из site.yml по отдельным ролям.

8. Выложите все roles в репозитории. Проставьте теги, используя семантическую нумерацию. Добавьте roles в `requirements.yml` в playbook.

```
---
  - name: clickhouse 
    src: git@github.com:AlexeySetevoi/ansible-clickhouse.git
    scm: git
    version: "1.11.0"
  - name: vecror-role 
    src: git@github.com:stepynin-georgy/vector-role.git
    scm: git
    version: "1.0.0"
  - name: lighthouse-role 
    src: git@github.com:stepynin-georgy/lighthouse-role.git
    scm: git
    version: "1.0.0"
```

[vector-role](https://github.com/stepynin-georgy/vector-role)

[lighthouse-role](https://github.com/stepynin-georgy/lighthouse-role)

9. Переработайте playbook на использование roles. Не забудьте про зависимости LightHouse и возможности совмещения `roles` с `tasks`.

Поправил playbook, для работы с ролями: [site.yaml](https://github.com/stepynin-georgy/hw_ansible_4/blob/main/playbook/site.yml). И проверил его работу:
 
```
---
- name: Install Clickhouse
  hosts: clickhouse
  roles:
    - clickhouse-role

- name: Install Lighthouse
  hosts: lighthouse
  roles:
    - lighthouse-role

- name: Install Vector
  hosts: vector
  roles:
    - vector-role
```

```
root@netology:/opt/ansible_hw4/playbook# ansible-playbook -i inventory/prod.yml site.yml 
[WARNING]: Found both group and host with same name: vector
[WARNING]: Found both group and host with same name: clickhouse
[WARNING]: Found both group and host with same name: lighthouse

PLAY [Install Clickhouse] ***************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host clickhouse is using the discovered Python interpreter at /usr/bin/python3.12, but future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
ok: [clickhouse]

TASK [clickhouse-role : Include OS Family Specific Variables] ***************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [clickhouse-role : include_tasks] **************************************************************************************************************************************************************************************************
included: /opt/ansible_hw4/playbook/roles/clickhouse-role/tasks/precheck.yml for clickhouse

TASK [clickhouse-role : Requirements check | Checking sse4_2 support] *******************************************************************************************************************************************************************
ok: [clickhouse]

TASK [clickhouse-role : Requirements check | Not supported distribution && release] *****************************************************************************************************************************************************
skipping: [clickhouse]

TASK [clickhouse-role : include_tasks] **************************************************************************************************************************************************************************************************
included: /opt/ansible_hw4/playbook/roles/clickhouse-role/tasks/params.yml for clickhouse

TASK [clickhouse-role : Set clickhouse_service_enable] **********************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [clickhouse-role : Set clickhouse_service_ensure] **********************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [clickhouse-role : include_tasks] **************************************************************************************************************************************************************************************************
included: /opt/ansible_hw4/playbook/roles/clickhouse-role/tasks/install/apt.yml for clickhouse

TASK [clickhouse-role : Install by APT | Apt-key add repo key] **************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [clickhouse-role : Install by APT | Remove old repo] *******************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [clickhouse-role : Install by APT | Repo installation] *****************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [clickhouse-role : Install by APT | Package installation] **************************************************************************************************************************************************************************
skipping: [clickhouse]

TASK [clickhouse-role : Install by APT | Package installation] **************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [clickhouse-role : Hold specified version during APT upgrade | Package installation] ***********************************************************************************************************************************************
ok: [clickhouse] => (item=clickhouse-client)
ok: [clickhouse] => (item=clickhouse-server)
ok: [clickhouse] => (item=clickhouse-common-static)

TASK [clickhouse-role : include_tasks] **************************************************************************************************************************************************************************************************
included: /opt/ansible_hw4/playbook/roles/clickhouse-role/tasks/configure/sys.yml for clickhouse

TASK [clickhouse-role : Check clickhouse config, data and logs] *************************************************************************************************************************************************************************
ok: [clickhouse] => (item=/var/log/clickhouse-server)
changed: [clickhouse] => (item=/etc/clickhouse-server)
changed: [clickhouse] => (item=/var/lib/clickhouse/tmp/)
changed: [clickhouse] => (item=/var/lib/clickhouse/)

TASK [clickhouse-role : Config | Create config.d folder] ********************************************************************************************************************************************************************************
changed: [clickhouse]

TASK [clickhouse-role : Config | Create users.d folder] *********************************************************************************************************************************************************************************
changed: [clickhouse]

TASK [clickhouse-role : Config | Generate system config] ********************************************************************************************************************************************************************************
changed: [clickhouse]

TASK [clickhouse-role : Config | Generate users config] *********************************************************************************************************************************************************************************
changed: [clickhouse]

TASK [clickhouse-role : Config | Generate remote_servers config] ************************************************************************************************************************************************************************
skipping: [clickhouse]

TASK [clickhouse-role : Config | Generate macros config] ********************************************************************************************************************************************************************************
skipping: [clickhouse]

TASK [clickhouse-role : Config | Generate zookeeper servers config] *********************************************************************************************************************************************************************
skipping: [clickhouse]

TASK [clickhouse-role : Config | Fix interserver_http_port and intersever_https_port collision] *****************************************************************************************************************************************
skipping: [clickhouse]

TASK [clickhouse-role : Notify Handlers Now] ********************************************************************************************************************************************************************************************

RUNNING HANDLER [clickhouse-role : Restart Clickhouse Service] **************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [clickhouse-role : include_tasks] **************************************************************************************************************************************************************************************************
included: /opt/ansible_hw4/playbook/roles/clickhouse-role/tasks/service.yml for clickhouse

TASK [clickhouse-role : Ensure clickhouse-server.service is enabled: True and state: restarted] *****************************************************************************************************************************************
changed: [clickhouse]

TASK [clickhouse-role : Wait for Clickhouse Server to Become Ready] *********************************************************************************************************************************************************************
ok: [clickhouse]

TASK [clickhouse-role : include_tasks] **************************************************************************************************************************************************************************************************
included: /opt/ansible_hw4/playbook/roles/clickhouse-role/tasks/configure/db.yml for clickhouse

TASK [clickhouse-role : Set ClickHose Connection String] ********************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [clickhouse-role : Gather list of existing databases] ******************************************************************************************************************************************************************************
ok: [clickhouse]

TASK [clickhouse-role : Config | Delete database config] ********************************************************************************************************************************************************************************
skipping: [clickhouse]

TASK [clickhouse-role : Config | Create database config] ********************************************************************************************************************************************************************************
skipping: [clickhouse]

TASK [clickhouse-role : include_tasks] **************************************************************************************************************************************************************************************************
included: /opt/ansible_hw4/playbook/roles/clickhouse-role/tasks/configure/dict.yml for clickhouse

TASK [clickhouse-role : Config | Generate dictionary config] ****************************************************************************************************************************************************************************
skipping: [clickhouse]

TASK [clickhouse-role : include_tasks] **************************************************************************************************************************************************************************************************
skipping: [clickhouse]

PLAY [Install Lighthouse] ***************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host lighthouse is using the discovered Python interpreter at /usr/bin/python3.12, but future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
ok: [lighthouse]

TASK [lighthouse-role : Install nginx] **************************************************************************************************************************************************************************************************
ok: [lighthouse]

TASK [lighthouse-role : Create Nginx config] ********************************************************************************************************************************************************************************************
ok: [lighthouse]

TASK [lighthouse-role : Install git] ****************************************************************************************************************************************************************************************************
ok: [lighthouse]

TASK [lighthouse-role : Copy lighthouse rfom git] ***************************************************************************************************************************************************************************************
ok: [lighthouse]

TASK [lighthouse-role : Create lighthouse config] ***************************************************************************************************************************************************************************************
ok: [lighthouse]

PLAY [Install Vector] *******************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host vector is using the discovered Python interpreter at /usr/bin/python3.12, but future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
ok: [vector]

TASK [vector-role : Create vector work directory] ***************************************************************************************************************************************************************************************
ok: [vector]

TASK [vector-role : Get Vector distrib] *************************************************************************************************************************************************************************************************
ok: [vector]

TASK [vector-role : Unzip Vector archive] ***********************************************************************************************************************************************************************************************
ok: [vector]

TASK [vector-role : Install Vector binary] **********************************************************************************************************************************************************************************************
ok: [vector]

TASK [vector-role : Check Vector installation] ******************************************************************************************************************************************************************************************
changed: [vector]

TASK [vector-role : Create Vector config vector.toml] ***********************************************************************************************************************************************************************************
ok: [vector]

TASK [vector-role : Create vector.service daemon] ***************************************************************************************************************************************************************************************
changed: [vector]

TASK [vector-role : Modify vector.service file] *****************************************************************************************************************************************************************************************
changed: [vector]

TASK [vector-role : Create user vector] *************************************************************************************************************************************************************************************************
ok: [vector]

TASK [vector-role : Create Vector data_dir] *********************************************************************************************************************************************************************************************
ok: [vector]

RUNNING HANDLER [vector-role : Start Vector service] ************************************************************************************************************************************************************************************
changed: [vector]

PLAY RECAP ******************************************************************************************************************************************************************************************************************************
clickhouse                 : ok=27   changed=6    unreachable=0    failed=0    skipped=10   rescued=0    ignored=0   
lighthouse                 : ok=6    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vector                     : ok=12   changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

10. Выложите playbook в репозиторий.
11. В ответе дайте ссылки на оба репозитория с roles и одну ссылку на репозиторий с playbook.

---

### Как оформить решение задания

Выполненное домашнее задание пришлите в виде ссылки на .md-файл в вашем репозитории.

---

