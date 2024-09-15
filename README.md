# Домашнее задание к занятию 5 «Тестирование roles»

## Подготовка к выполнению

1. Установите molecule и его драйвера: `pip3 install "molecule molecule_docker molecule_podman`.
2. Выполните `docker pull aragast/netology:latest` —  это образ с podman, tox и несколькими пайтонами (3.7 и 3.9) внутри.

![изображение](https://github.com/stepynin-georgy/hw_ansible_5/blob/main/img/Screenshot_187.png)

## Основная часть

Ваша цель — настроить тестирование ваших ролей. 

Задача — сделать сценарии тестирования для vector. 

Ожидаемый результат — все сценарии успешно проходят тестирование ролей.

### Molecule

1. Запустите  `molecule test -s ubuntu_xenial` (или с любым другим сценарием, не имеет значения) внутри корневой директории clickhouse-role, посмотрите на вывод команды. Данная команда может отработать с ошибками или не отработать вовсе, это нормально. Наша цель - посмотреть как другие в реальном мире используют молекулу И из чего может состоять сценарий тестирования.
2. Перейдите в каталог с ролью vector-role и создайте сценарий тестирования по умолчанию при помощи `molecule init scenario --driver-name docker`.

![изображение](https://github.com/stepynin-georgy/hw_ansible_5/blob/main/img/Screenshot_188.png)

3. Добавьте несколько разных дистрибутивов (oraclelinux:8, ubuntu:latest) для инстансов и протестируйте роль, исправьте найденные ошибки, если они есть.

```
platforms:
  - name: instance
    image: quay.io/centos/centos:stream8
    pre_build_image: true
  - name: ubuntu
    image: pycontribs/ubuntu:latest
    pre_build_image: true
  - name: centos
    image: pycontribs/centos:7
    pre_build_image: true
```

4. Добавьте несколько assert в verify.yml-файл для проверки работоспособности vector-role (проверка, что конфиг валидный, проверка успешности запуска и др.).

[verify.yml](https://github.com/stepynin-georgy/vector-role/blob/1.1.0/molecule/default/verify.yml)

5. Запустите тестирование роли повторно и проверьте, что оно прошло успешно.

<details><summary>Запуск `molecole test` с verify.yml</summary>

```
(venv) root@ansible-ubuntu:/opt/hw_ansible_4/playbook/roles/vector-role# molecule test
WARNING  The scenario config file ('/opt/hw_ansible_4/playbook/roles/vector-role/molecule/default/molecule.yml') has been modified since the scenario was created. If recent changes are important, reset the scenario with 'molecule destroy' to clean up created items or 'molecule reset' to clear current configuration.
WARNING  Driver docker does not provide a schema.
INFO     default scenario test matrix: dependency, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun with role_name_check=0...
WARNING  Multiple versions of 'community.docker' were found installed, only the first one will be used, 3.4.11 (/opt/hw_ansible_4/venv/lib/python3.9/site-packages/ansible_collections).
INFO     Running default > dependency
WARNING  Skipping, missing the requirements file.
WARNING  Skipping, missing the requirements file.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy
INFO     Sanity checks: 'docker'

PLAY [Destroy] *****************************************************************

TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) deletion to complete] *******************************
ok: [localhost] => (item=instance)
ok: [localhost] => (item=ubuntu)
ok: [localhost] => (item=centos)

TASK [Delete docker networks(s)] ***********************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Running default > syntax

playbook: /opt/hw_ansible_4/playbook/roles/vector-role/molecule/default/converge.yml
INFO     Running default > create

PLAY [Create] ******************************************************************

TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]

TASK [Log into a Docker registry] **********************************************
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost]

TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Create Dockerfiles from image names] *************************************
skipping: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})
skipping: [localhost]

TASK [Synchronization the context] *********************************************
skipping: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})
skipping: [localhost]

TASK [Discover local Docker images] ********************************************
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'false_condition': 'not item.pre_build_image | default(false)', 'item': {'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'false_condition': 'not item.pre_build_image | default(false)', 'item': {'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 1, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'false_condition': 'not item.pre_build_image | default(false)', 'item': {'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 2, 'ansible_index_var': 'i'})

TASK [Build an Ansible compatible image (new)] *********************************
skipping: [localhost] => (item=molecule_local/quay.io/centos/centos:stream8)
skipping: [localhost] => (item=molecule_local/pycontribs/ubuntu:latest)
skipping: [localhost] => (item=molecule_local/pycontribs/centos:7)
skipping: [localhost]

TASK [Create docker network(s)] ************************************************
skipping: [localhost]

TASK [Determine the CMD directives] ********************************************
ok: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) creation to complete] *******************************
FAILED - RETRYING: [localhost]: Wait for instance(s) creation to complete (300 retries left).
FAILED - RETRYING: [localhost]: Wait for instance(s) creation to complete (299 retries left).
FAILED - RETRYING: [localhost]: Wait for instance(s) creation to complete (298 retries left).
FAILED - RETRYING: [localhost]: Wait for instance(s) creation to complete (297 retries left).
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': 'j399411361140.200245', 'results_file': '/root/.ansible_async/j399411361140.200245', 'changed': True, 'item': {'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': 'j429901717297.200271', 'results_file': '/root/.ansible_async/j429901717297.200271', 'changed': True, 'item': {'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': 'j643989251909.200308', 'results_file': '/root/.ansible_async/j643989251909.200308', 'changed': True, 'item': {'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost                  : ok=6    changed=2    unreachable=0    failed=0    skipped=5    rescued=0    ignored=0

INFO     Running default > prepare
WARNING  Skipping, prepare playbook not configured.
INFO     Running default > converge

PLAY [Converge] ****************************************************************

TASK [Include vector] **********************************************************

TASK [vector-role : Create vector work directory] ******************************
changed: [ubuntu]
changed: [centos]
changed: [instance]

TASK [vector-role : Get Vector distrib] ****************************************
changed: [instance]
changed: [centos]
changed: [ubuntu]

TASK [vector-role : Unzip Vector archive] **************************************
changed: [ubuntu]
changed: [instance]
changed: [centos]

TASK [vector-role : Install Vector binary] *************************************
changed: [centos]
changed: [ubuntu]
changed: [instance]

TASK [vector-role : Check Vector installation] *********************************
changed: [instance]
changed: [ubuntu]
changed: [centos]

TASK [vector-role : Create Vector config vector.toml] **************************
changed: [centos]
changed: [ubuntu]
changed: [instance]

TASK [vector-role : Create vector.service daemon] ******************************
changed: [instance]
changed: [centos]
changed: [ubuntu]

TASK [vector-role : Modify vector.service file] ********************************
changed: [ubuntu]
changed: [centos]
changed: [instance]

TASK [vector-role : Create user vector] ****************************************
changed: [ubuntu]
changed: [centos]
changed: [instance]

TASK [vector-role : Create Vector data_dir] ************************************
changed: [centos]
changed: [instance]
changed: [ubuntu]

RUNNING HANDLER [vector-role : Start Vector service] ***************************
fatal: [ubuntu]: FAILED! => {"changed": false, "msg": "Could not find the requested service vector: "}
fatal: [instance]: FAILED! => {"changed": false, "msg": "Service is in unknown state", "status": {}}
fatal: [centos]: FAILED! => {"changed": false, "msg": "Service is in unknown state", "status": {}}

PLAY RECAP *********************************************************************
centos                     : ok=10   changed=10   unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
instance                   : ok=10   changed=10   unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=10   changed=10   unreachable=0    failed=1    skipped=0    rescued=0    ignored=0

CRITICAL Ansible return code was 2, command was: ansible-playbook --inventory /root/.cache/molecule/vector-role/default/inventory --skip-tags molecule-notest,notest /opt/hw_ansible_4/playbook/roles/vector-role/molecule/default/converge.yml
WARNING  An error occurred during the test sequence action: 'converge'. Cleaning up.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy

PLAY [Destroy] *****************************************************************

TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) deletion to complete] *******************************
FAILED - RETRYING: [localhost]: Wait for instance(s) deletion to complete (300 retries left).
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Delete docker networks(s)] ***********************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory


---
driver:
  name: docker
platforms:
  - name: instance
    image: quay.io/centos/centos:stream8
    pre_build_image: true
  - name: ubuntu
    image: pycontribs/ubuntu:latest
    pre_build_image: true
  - name: centos
    image: pycontribs/centos:7
    pre_build_image: true



---
# This is an example playbook to execute Ansible tests.

- name: Verify
  hosts: all
  gather_facts: false
  vars_files:
    -  "{{ lookup('ansible.builtin.env', 'MOLECULE_PROJECT_DIRECTORY') }}/vars/main.yml"
  tasks:

  - name: Get Vector version
    ansible.builtin.command: "vector --version"
    changed_when: false
    register: vector_version
  - name: Assert Vector instalation
    assert:
      that: "'{{ vector_version.rc }}' == '0'"

  - name: Validation Vector configuration
    ansible.builtin.command: "vector validate --no-environment --config-toml {{ vector_config }}"
    changed_when: false
    register: vector_validate
  - name: Assert Vector validate config
    assert:
      that: "'{{ vector_validate.rc }}' == '0'"



      playbook: /opt/hw_ansible_4/playbook/roles/vector-role/molecule/default/converge.yml
INFO     Running default > create

PLAY [Create] ******************************************************************

TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]

TASK [Log into a Docker registry] **********************************************
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost]

TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Create Dockerfiles from image names] *************************************
skipping: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})
skipping: [localhost]

TASK [Synchronization the context] *********************************************
skipping: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})
skipping: [localhost]

TASK [Discover local Docker images] ********************************************
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'false_condition': 'not item.pre_build_image | default(false)', 'item': {'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'false_condition': 'not item.pre_build_image | default(false)', 'item': {'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 1, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'false_condition': 'not item.pre_build_image | default(false)', 'item': {'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 2, 'ansible_index_var': 'i'})

TASK [Build an Ansible compatible image (new)] *********************************
skipping: [localhost] => (item=molecule_local/quay.io/centos/centos:stream8)
skipping: [localhost] => (item=molecule_local/pycontribs/ubuntu:latest)
skipping: [localhost] => (item=molecule_local/pycontribs/centos:7)
skipping: [localhost]

TASK [Create docker network(s)] ************************************************
skipping: [localhost]

TASK [Determine the CMD directives] ********************************************
ok: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) creation to complete] *******************************
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': 'j445406414192.172827', 'results_file': '/root/.ansible_async/j445406414192.172827', 'changed': True, 'item': {'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': 'j714902359878.172853', 'results_file': '/root/.ansible_async/j714902359878.172853', 'changed': True, 'item': {'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
FAILED - RETRYING: [localhost]: Wait for instance(s) creation to complete (300 retries left).
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': 'j505464697360.172878', 'results_file': '/root/.ansible_async/j505464697360.172878', 'changed': True, 'item': {'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost                  : ok=6    changed=2    unreachable=0    failed=0    skipped=5    rescued=0    ignored=0

INFO     Running default > prepare
WARNING  Skipping, prepare playbook not configured.
INFO     Running default > converge

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [instance]
ok: [ubuntu]
ok: [centos]

TASK [Include vector] **********************************************************

TASK [vector-role : Create directory vector] ***********************************
changed: [instance]
changed: [centos]
changed: [ubuntu]

TASK [vector-role : Get vector distrib] ****************************************
changed: [centos]
changed: [instance]
changed: [ubuntu]

TASK [vector-role : Unarchive vector] ******************************************
changed: [instance]
changed: [ubuntu]
changed: [centos]

TASK [vector-role : Create a symbolic link] ************************************
changed: [ubuntu]
changed: [instance]
changed: [centos]

TASK [vector-role : Create vector unit flie] ***********************************
changed: [centos]
changed: [instance]
changed: [ubuntu]

TASK [vector-role : Mkdir vector data] *****************************************
changed: [centos]
changed: [ubuntu]
changed: [instance]

TASK [vector-role : Vector config create] **************************************
changed: [centos]
changed: [instance]
changed: [ubuntu]

PLAY RECAP *********************************************************************
centos                     : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
instance                   : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Running default > idempotence

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [ubuntu]
ok: [instance]
ok: [centos]

TASK [Include vector] **********************************************************

TASK [vector-role : Create directory vector] ***********************************
ok: [centos]
ok: [ubuntu]
ok: [instance]

TASK [vector-role : Get vector distrib] ****************************************
ok: [ubuntu]
ok: [centos]
ok: [instance]

TASK [vector-role : Unarchive vector] ******************************************
skipping: [centos]
skipping: [ubuntu]
skipping: [instance]

TASK [vector-role : Create a symbolic link] ************************************
ok: [centos]
ok: [ubuntu]
ok: [instance]

TASK [vector-role : Create vector unit flie] ***********************************
ok: [instance]
ok: [ubuntu]
ok: [centos]

TASK [vector-role : Mkdir vector data] *****************************************
ok: [centos]
ok: [ubuntu]
ok: [instance]

TASK [vector-role : Vector config create] **************************************
ok: [centos]
ok: [instance]
ok: [ubuntu]

PLAY RECAP *********************************************************************
centos                     : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
instance                   : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
ubuntu                     : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Idempotence completed successfully.
INFO     Running default > side_effect
WARNING  Skipping, side effect playbook not configured.
INFO     Running default > verify
INFO     Running Ansible Verifier
WARNING  Skipping, verify action has no playbook.
INFO     Verifier completed successfully.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy

PLAY [Destroy] *****************************************************************

TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) deletion to complete] *******************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Delete docker networks(s)] ***********************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory



playbook: /opt/hw_ansible_4/playbook/roles/vector-role/molecule/default/converge.yml
INFO     Running default > create

PLAY [Create] ******************************************************************

TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]

TASK [Log into a Docker registry] **********************************************
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost]

TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Create Dockerfiles from image names] *************************************
skipping: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})
skipping: [localhost]

TASK [Synchronization the context] *********************************************
skipping: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})
skipping: [localhost]

TASK [Discover local Docker images] ********************************************
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'false_condition': 'not item.pre_build_image | default(false)', 'item': {'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'false_condition': 'not item.pre_build_image | default(false)', 'item': {'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 1, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'false_condition': 'not item.pre_build_image | default(false)', 'item': {'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 2, 'ansible_index_var': 'i'})

TASK [Build an Ansible compatible image (new)] *********************************
skipping: [localhost] => (item=molecule_local/quay.io/centos/centos:stream8)
skipping: [localhost] => (item=molecule_local/pycontribs/ubuntu:latest)
skipping: [localhost] => (item=molecule_local/pycontribs/centos:7)
skipping: [localhost]

TASK [Create docker network(s)] ************************************************
skipping: [localhost]

TASK [Determine the CMD directives] ********************************************
ok: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) creation to complete] *******************************
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': 'j726049353771.235060', 'results_file': '/root/.ansible_async/j726049353771.235060', 'changed': True, 'item': {'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': 'j909846531436.235086', 'results_file': '/root/.ansible_async/j909846531436.235086', 'changed': True, 'item': {'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
FAILED - RETRYING: [localhost]: Wait for instance(s) creation to complete (300 retries left).
changed: [localhost] => (item={'failed': 0, 'started': 1, 'finished': 0, 'ansible_job_id': 'j445862856520.235123', 'results_file': '/root/.ansible_async/j445862856520.235123', 'changed': True, 'item': {'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost                  : ok=6    changed=2    unreachable=0    failed=0    skipped=5    rescued=0    ignored=0

INFO     Running default > prepare
WARNING  Skipping, prepare playbook not configured.
INFO     Running default > converge

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [instance]
ok: [ubuntu]
ok: [centos]

TASK [Include vector] **********************************************************

TASK [vector-role : Create directory vector] ***********************************
changed: [ubuntu]
changed: [centos]
changed: [instance]

TASK [vector-role : Get vector distrib] ****************************************
changed: [centos]
changed: [instance]
changed: [ubuntu]

TASK [vector-role : Unarchive vector] ******************************************
changed: [instance]
changed: [ubuntu]
changed: [centos]

TASK [vector-role : Create a symbolic link] ************************************
changed: [centos]
changed: [instance]
changed: [ubuntu]

TASK [vector-role : Create vector unit flie] ***********************************
changed: [centos]
changed: [instance]
changed: [ubuntu]

TASK [vector-role : Mkdir vector data] *****************************************
changed: [centos]
changed: [ubuntu]
changed: [instance]

TASK [vector-role : Vector config create] **************************************
changed: [centos]
changed: [ubuntu]
changed: [instance]

PLAY RECAP *********************************************************************
centos                     : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
instance                   : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Running default > idempotence

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
ok: [instance]
ok: [ubuntu]
ok: [centos]

TASK [Include vector] **********************************************************

TASK [vector-role : Create directory vector] ***********************************
ok: [centos]
ok: [ubuntu]
ok: [instance]

TASK [vector-role : Get vector distrib] ****************************************
ok: [centos]
ok: [ubuntu]
ok: [instance]

TASK [vector-role : Unarchive vector] ******************************************
skipping: [centos]
skipping: [ubuntu]
skipping: [instance]

TASK [vector-role : Create a symbolic link] ************************************
ok: [centos]
ok: [ubuntu]
ok: [instance]

TASK [vector-role : Create vector unit flie] ***********************************
ok: [centos]
ok: [instance]
ok: [ubuntu]

TASK [vector-role : Mkdir vector data] *****************************************
ok: [centos]
ok: [instance]
ok: [ubuntu]

TASK [vector-role : Vector config create] **************************************
ok: [centos]
ok: [ubuntu]
ok: [instance]

PLAY RECAP *********************************************************************
centos                     : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
instance                   : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
ubuntu                     : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Idempotence completed successfully.
INFO     Running default > side_effect
WARNING  Skipping, side effect playbook not configured.
INFO     Running default > verify
INFO     Running Ansible Verifier

PLAY [Verify] ******************************************************************

TASK [Gathering Facts] *********************************************************
ok: [ubuntu]
ok: [instance]
ok: [centos]

TASK [Get Vector version] ******************************************************
ok: [instance]
ok: [ubuntu]
ok: [centos]

TASK [Assert Vector instalation] ***********************************************
ok: [centos] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [instance] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [ubuntu] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Validation Vector configuration] *****************************************
ok: [instance]
ok: [ubuntu]
ok: [centos]

TASK [Assert Vector validate config] *******************************************
ok: [centos] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [ubuntu] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [instance] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY RECAP *********************************************************************
centos                     : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
instance                   : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Verifier completed successfully.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy

PLAY [Destroy] *****************************************************************

TASK [Set async_dir for HOME env] **********************************************
ok: [localhost]

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) deletion to complete] *******************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Delete docker networks(s)] ***********************************************
skipping: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory
(venv) root@ansible-ubuntu:/opt/hw_ansible_4/playbook/roles/vector-role#
```

</details>

5. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.

### Tox

1. Добавьте в директорию с vector-role файлы из [директории](./example).
2. Запустите `docker run --privileged=True -v <path_to_repo>:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash`, где path_to_repo — путь до корня репозитория с vector-role на вашей файловой системе.
3. Внутри контейнера выполните команду `tox`, посмотрите на вывод.

<details><summary>Запуск tox</summary>

```
(venv) root@ansible-ubuntu:/opt/hw_ansible_4/playbook/roles/vector-role# tox -r
py39-ansible210: remove tox env folder /opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210
py39-ansible210: install_deps> python -I -m pip install 'ansible<2.12' -r tox-requirements.txt
py39-ansible210: commands[0]> molecule test -s default --destroy always
INFO     default scenario test matrix: dependency, lint, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun...
INFO     Guessed /opt/hw_ansible_4/playbook/roles/vector-role as project root directory
INFO     Using /root/.cache/ansible-lint/5eb14b/roles/georgy.vector symlink to current repository in order to enable Ansible to find the role using its expected full name.
INFO     Added ANSIBLE_ROLES_PATH=~/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles:/root/.cache/ansible-lint/5eb14b/roles
INFO     Running default > dependency
WARNING  Skipping, missing the requirements file.
WARNING  Skipping, missing the requirements file.
INFO     Running default > lint
INFO     Lint is disabled.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy
INFO     Sanity checks: 'docker'
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,
[WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) deletion to complete] *******************************
ok: [localhost] => (item=instance)
ok: [localhost] => (item=ubuntu)
ok: [localhost] => (item=centos)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Running default > syntax
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,

playbook: /opt/hw_ansible_4/playbook/roles/vector-role/molecule/default/converge.yml
INFO     Running default > create
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,
[WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Create] ******************************************************************

TASK [Log into a Docker registry] **********************************************
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost]

TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Create Dockerfiles from image names] *************************************
skipping: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Discover local Docker images] ********************************************
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 1, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 2, 'ansible_index_var': 'i'})

TASK [Build an Ansible compatible image (new)] *********************************
skipping: [localhost] => (item=molecule_local/quay.io/centos/centos:stream8)
skipping: [localhost] => (item=molecule_local/pycontribs/ubuntu:latest)
skipping: [localhost] => (item=molecule_local/pycontribs/centos:7)

TASK [Create docker network(s)] ************************************************

TASK [Determine the CMD directives] ********************************************
ok: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) creation to complete] *******************************
FAILED - RETRYING: Wait for instance(s) creation to complete (300 retries left).
FAILED - RETRYING: Wait for instance(s) creation to complete (299 retries left).
FAILED - RETRYING: Wait for instance(s) creation to complete (298 retries left).
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '897103052750.279590', 'results_file': '/root/.ansible_async/897103052750.279590', 'changed': True, 'failed': False, 'item': {'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '968354557656.279617', 'results_file': '/root/.ansible_async/968354557656.279617', 'changed': True, 'failed': False, 'item': {'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '966395800296.279649', 'results_file': '/root/.ansible_async/966395800296.279649', 'changed': True, 'failed': False, 'item': {'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost                  : ok=5    changed=2    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0

INFO     Running default > prepare
WARNING  Skipping, prepare playbook not configured.
INFO     Running default > converge
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [instance]
ok: [ubuntu]
ok: [centos]

TASK [Include vector] **********************************************************

TASK [vector-role : Create directory vector] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [ubuntu]
changed: [instance]
changed: [centos]

TASK [vector-role : Get vector distrib] ****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [ubuntu]
changed: [instance]

TASK [vector-role : Unarchive vector] ******************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [instance]
changed: [ubuntu]
changed: [centos]

TASK [vector-role : Create a symbolic link] ************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [ubuntu]
changed: [instance]
changed: [centos]

TASK [vector-role : Create vector unit flie] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [ubuntu]
changed: [instance]

TASK [vector-role : Mkdir vector data] *****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [ubuntu]
changed: [instance]

TASK [vector-role : Vector config create] **************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [instance]
changed: [ubuntu]

PLAY RECAP *********************************************************************
centos                     : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
instance                   : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Running default > idempotence
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [instance]
ok: [centos]

TASK [Include vector] **********************************************************

TASK [vector-role : Create directory vector] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [instance]
ok: [ubuntu]

TASK [vector-role : Get vector distrib] ****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [ubuntu]
ok: [instance]

TASK [vector-role : Unarchive vector] ******************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
skipping: [centos]
skipping: [ubuntu]
skipping: [instance]

TASK [vector-role : Create a symbolic link] ************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [instance]
ok: [centos]
ok: [ubuntu]

TASK [vector-role : Create vector unit flie] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [instance]
ok: [ubuntu]

TASK [vector-role : Mkdir vector data] *****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [ubuntu]
ok: [instance]

TASK [vector-role : Vector config create] **************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [centos]
ok: [instance]

PLAY RECAP *********************************************************************
centos                     : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
instance                   : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
ubuntu                     : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Idempotence completed successfully.
INFO     Running default > side_effect
WARNING  Skipping, side effect playbook not configured.
INFO     Running default > verify
INFO     Running Ansible Verifier
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,

PLAY [Verify] ******************************************************************

TASK [Gathering Facts] *********************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [instance]
ok: [centos]

TASK [Get Vector version] ******************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [centos]
ok: [instance]

TASK [Assert Vector instalation] ***********************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [instance] => {
    "changed": false,
    "msg": "All assertions passed"
}
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Validation Vector configuration] *****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [centos]
ok: [instance]

TASK [Assert Vector validate config] *******************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [instance] => {
    "changed": false,
    "msg": "All assertions passed"
}
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY RECAP *********************************************************************
centos                     : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
instance                   : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Verifier completed successfully.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,
[WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) deletion to complete] *******************************
FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left).
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory
py39-ansible210: OK ✔ in 2 minutes 35.17 seconds
py39-ansible30: remove tox env folder /opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30
py39-ansible30: install_deps> python -I -m pip install 'ansible<3.1' -r tox-requirements.txt
py39-ansible30: commands[0]> molecule test -s default --destroy always
INFO     default scenario test matrix: dependency, lint, cleanup, destroy, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun...
INFO     Guessed /opt/hw_ansible_4/playbook/roles/vector-role as project root directory
INFO     Using /root/.cache/ansible-lint/5eb14b/roles/georgy.vector symlink to current repository in order to enable Ansible to find the role using its expected full name.
INFO     Added ANSIBLE_ROLES_PATH=~/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles:/root/.cache/ansible-lint/5eb14b/roles
INFO     Running default > dependency
WARNING  Skipping, missing the requirements file.
WARNING  Skipping, missing the requirements file.
INFO     Running default > lint
INFO     Lint is disabled.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy
INFO     Sanity checks: 'docker'
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,
[WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) deletion to complete] *******************************
FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left).
ok: [localhost] => (item=instance)
ok: [localhost] => (item=ubuntu)
ok: [localhost] => (item=centos)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Running default > syntax
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,

playbook: /opt/hw_ansible_4/playbook/roles/vector-role/molecule/default/converge.yml
INFO     Running default > create
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,
[WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Create] ******************************************************************

TASK [Log into a Docker registry] **********************************************
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost]

TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Create Dockerfiles from image names] *************************************
skipping: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Discover local Docker images] ********************************************
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 1, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 2, 'ansible_index_var': 'i'})

TASK [Build an Ansible compatible image (new)] *********************************
skipping: [localhost] => (item=molecule_local/quay.io/centos/centos:stream8)
skipping: [localhost] => (item=molecule_local/pycontribs/ubuntu:latest)
skipping: [localhost] => (item=molecule_local/pycontribs/centos:7)

TASK [Create docker network(s)] ************************************************

TASK [Determine the CMD directives] ********************************************
ok: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) creation to complete] *******************************
FAILED - RETRYING: Wait for instance(s) creation to complete (300 retries left).
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '825587808028.293432', 'results_file': '/root/.ansible_async/825587808028.293432', 'changed': True, 'failed': False, 'item': {'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '86024457715.293459', 'results_file': '/root/.ansible_async/86024457715.293459', 'changed': True, 'failed': False, 'item': {'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '847922522234.293492', 'results_file': '/root/.ansible_async/847922522234.293492', 'changed': True, 'failed': False, 'item': {'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost                  : ok=5    changed=2    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0

INFO     Running default > prepare
WARNING  Skipping, prepare playbook not configured.
INFO     Running default > converge
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [instance]
ok: [centos]

TASK [Include vector] **********************************************************

TASK [vector-role : Create directory vector] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [instance]
changed: [ubuntu]

TASK [vector-role : Get vector distrib] ****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [ubuntu]
changed: [instance]
changed: [centos]

TASK [vector-role : Unarchive vector] ******************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [instance]
changed: [ubuntu]
changed: [centos]

TASK [vector-role : Create a symbolic link] ************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [instance]
changed: [ubuntu]

TASK [vector-role : Create vector unit flie] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [ubuntu]
changed: [instance]

TASK [vector-role : Mkdir vector data] *****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [instance]
changed: [ubuntu]

TASK [vector-role : Vector config create] **************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [instance]
changed: [ubuntu]

PLAY RECAP *********************************************************************
centos                     : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
instance                   : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Running default > idempotence
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [instance]
ok: [centos]
ok: [ubuntu]

TASK [Include vector] **********************************************************

TASK [vector-role : Create directory vector] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [ubuntu]
ok: [instance]

TASK [vector-role : Get vector distrib] ****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [instance]
ok: [ubuntu]

TASK [vector-role : Unarchive vector] ******************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
skipping: [centos]
skipping: [ubuntu]
skipping: [instance]

TASK [vector-role : Create a symbolic link] ************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [centos]
ok: [instance]

TASK [vector-role : Create vector unit flie] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [instance]
ok: [ubuntu]

TASK [vector-role : Mkdir vector data] *****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [instance]
ok: [ubuntu]

TASK [vector-role : Vector config create] **************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [instance]
ok: [ubuntu]

PLAY RECAP *********************************************************************
centos                     : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
instance                   : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
ubuntu                     : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Idempotence completed successfully.
INFO     Running default > side_effect
WARNING  Skipping, side effect playbook not configured.
INFO     Running default > verify
INFO     Running Ansible Verifier
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,

PLAY [Verify] ******************************************************************

TASK [Gathering Facts] *********************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [instance]
ok: [centos]

TASK [Get Vector version] ******************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [instance]
ok: [ubuntu]

TASK [Assert Vector instalation] ***********************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos] => {
    "changed": false,
    "msg": "All assertions passed"
}
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [instance] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [ubuntu] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Validation Vector configuration] *****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [instance]
ok: [ubuntu]

TASK [Assert Vector validate config] *******************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos] => {
    "changed": false,
    "msg": "All assertions passed"
}
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [instance] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [ubuntu] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY RECAP *********************************************************************
centos                     : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
instance                   : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Verifier completed successfully.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,
[WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) deletion to complete] *******************************
FAILED - RETRYING: Wait for instance(s) deletion to complete (300 retries left).
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory
  py39-ansible210: OK (155.17=setup[38.43]+cmd[116.74] seconds)
  py39-ansible30: OK (176.87=setup[76.05]+cmd[100.82] seconds)
  congratulations :) (332.10 seconds)
(venv) root@ansible-ubuntu:/opt/hw_ansible_4/playbook/roles/vector-role#
```

</details>

4. Создайте облегчённый сценарий для `molecule` с драйвером `molecule_podman`. Проверьте его на исполнимость.

[molecule,yml](https://github.com/stepynin-georgy/vector-role/blob/1.1.0/molecule/default/molecule.yml)

5. Пропишите правильную команду в `tox.ini`, чтобы запускался облегчённый сценарий.

```
[tox]
minversion = 1.8
basepython = python3.9
envlist = py{39}-ansible{210,30}
skipsdist = true

[testenv]
passenv = *
deps =
    -r tox-requirements.txt
    ansible210: ansible<2.12
    ansible30: ansible<3.1
commands =
    {posargs:molecule test -s default --destroy always}
```

8. Запустите команду `tox`. Убедитесь, что всё отработало успешно.

<details><summary>Запуск tox с облегченным сценарием тестирования</summary>

```
(venv) root@ansible-ubuntu:/opt/hw_ansible_4/playbook/roles/vector-role# molecule matrix test
WARNING  The scenario config file ('/opt/hw_ansible_4/playbook/roles/vector-role/molecule/default/molecule.yml') has been modified since the scenario was created. If recent changes are important, reset the scenario with 'molecule destroy' to clean up created items or 'molecule reset' to clear current configuration.
WARNING  Driver podman does not provide a schema.
INFO     Test matrix
---
default:
  - create
  - prepare
  - converge
  - idempotence
  - side_effect
  - verify
  - cleanup
  - destroy

(venv) root@ansible-ubuntu:/opt/hw_ansible_4/playbook/roles/vector-role#






(venv) root@ansible-ubuntu:/opt/hw_ansible_4/playbook/roles/vector-role# tox -r
py39-ansible210: remove tox env folder /opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210
py39-ansible210: install_deps> python -I -m pip install 'ansible<2.12' -r tox-requirements.txt
py39-ansible210: commands[0]> molecule test -s default --destroy always
INFO     default scenario test matrix: create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun...
INFO     Guessed /opt/hw_ansible_4/playbook/roles/vector-role as project root directory
INFO     Using /root/.cache/ansible-lint/5eb14b/roles/georgy.vector symlink to current repository in order to enable Ansible to find the role using its expected full name.
INFO     Added ANSIBLE_ROLES_PATH=~/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles:/root/.cache/ansible-lint/5eb14b/roles
INFO     Running default > create
INFO     Sanity checks: 'docker'
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,
[WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Create] ******************************************************************

TASK [Log into a Docker registry] **********************************************
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost]

TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Create Dockerfiles from image names] *************************************
skipping: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Discover local Docker images] ********************************************
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 1, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 2, 'ansible_index_var': 'i'})

TASK [Build an Ansible compatible image (new)] *********************************
skipping: [localhost] => (item=molecule_local/quay.io/centos/centos:stream8)
skipping: [localhost] => (item=molecule_local/pycontribs/ubuntu:latest)
skipping: [localhost] => (item=molecule_local/pycontribs/centos:7)

TASK [Create docker network(s)] ************************************************

TASK [Determine the CMD directives] ********************************************
ok: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) creation to complete] *******************************
FAILED - RETRYING: Wait for instance(s) creation to complete (300 retries left).
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '164825761845.376240', 'results_file': '/root/.ansible_async/164825761845.376240', 'changed': True, 'failed': False, 'item': {'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '149064000266.376267', 'results_file': '/root/.ansible_async/149064000266.376267', 'changed': True, 'failed': False, 'item': {'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '221303682250.376300', 'results_file': '/root/.ansible_async/221303682250.376300', 'changed': True, 'failed': False, 'item': {'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost                  : ok=5    changed=2    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0

INFO     Running default > prepare
WARNING  Skipping, prepare playbook not configured.
INFO     Running default > converge
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [centos]
ok: [instance]

TASK [Include vector] **********************************************************

TASK [vector-role : Create directory vector] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [ubuntu]
changed: [instance]

TASK [vector-role : Get vector distrib] ****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [ubuntu]
changed: [centos]
changed: [instance]

TASK [vector-role : Unarchive vector] ******************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [ubuntu]
changed: [centos]
changed: [instance]

TASK [vector-role : Create a symbolic link] ************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [ubuntu]
changed: [centos]
changed: [instance]

TASK [vector-role : Create vector unit flie] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [ubuntu]
changed: [instance]

TASK [vector-role : Mkdir vector data] *****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [instance]
changed: [ubuntu]

TASK [vector-role : Vector config create] **************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [instance]
changed: [ubuntu]

PLAY RECAP *********************************************************************
centos                     : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
instance                   : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Running default > idempotence
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [instance]
ok: [ubuntu]
ok: [centos]

TASK [Include vector] **********************************************************

TASK [vector-role : Create directory vector] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [ubuntu]
ok: [instance]

TASK [vector-role : Get vector distrib] ****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [centos]
ok: [instance]

TASK [vector-role : Unarchive vector] ******************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
skipping: [centos]
skipping: [ubuntu]
skipping: [instance]

TASK [vector-role : Create a symbolic link] ************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [instance]
ok: [ubuntu]

TASK [vector-role : Create vector unit flie] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [ubuntu]
ok: [instance]

TASK [vector-role : Mkdir vector data] *****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [instance]
ok: [centos]
ok: [ubuntu]

TASK [vector-role : Vector config create] **************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [instance]
ok: [ubuntu]

PLAY RECAP *********************************************************************
centos                     : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
instance                   : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
ubuntu                     : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Idempotence completed successfully.
INFO     Running default > side_effect
WARNING  Skipping, side effect playbook not configured.
INFO     Running default > verify
INFO     Running Ansible Verifier
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,

PLAY [Verify] ******************************************************************

TASK [Gathering Facts] *********************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [instance]
ok: [centos]

TASK [Get Vector version] ******************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [centos]
ok: [instance]

TASK [Assert Vector instalation] ***********************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [instance] => {
    "changed": false,
    "msg": "All assertions passed"
}
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Validation Vector configuration] *****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [centos]
ok: [instance]

TASK [Assert Vector validate config] *******************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [instance] => {
    "changed": false,
    "msg": "All assertions passed"
}
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY RECAP *********************************************************************
centos                     : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
instance                   : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Verifier completed successfully.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible210/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,
[WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) deletion to complete] *******************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory
py39-ansible210: OK ✔ in 1 minute 57.95 seconds
py39-ansible30: remove tox env folder /opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30
py39-ansible30: install_deps> python -I -m pip install 'ansible<3.1' -r tox-requirements.txt
py39-ansible30: commands[0]> molecule test -s default --destroy always
INFO     default scenario test matrix: create, prepare, converge, idempotence, side_effect, verify, cleanup, destroy
INFO     Performing prerun...
INFO     Guessed /opt/hw_ansible_4/playbook/roles/vector-role as project root directory
INFO     Using /root/.cache/ansible-lint/5eb14b/roles/georgy.vector symlink to current repository in order to enable Ansible to find the role using its expected full name.
INFO     Added ANSIBLE_ROLES_PATH=~/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles:/root/.cache/ansible-lint/5eb14b/roles
INFO     Running default > create
INFO     Sanity checks: 'docker'
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,
[WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Create] ******************************************************************

TASK [Log into a Docker registry] **********************************************
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost] => (item=None)
skipping: [localhost]

TASK [Check presence of custom Dockerfiles] ************************************
ok: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Create Dockerfiles from image names] *************************************
skipping: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
skipping: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Discover local Docker images] ********************************************
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 0, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 1, 'ansible_index_var': 'i'})
ok: [localhost] => (item={'changed': False, 'skipped': True, 'skip_reason': 'Conditional result was False', 'item': {'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item', 'i': 2, 'ansible_index_var': 'i'})

TASK [Build an Ansible compatible image (new)] *********************************
skipping: [localhost] => (item=molecule_local/quay.io/centos/centos:stream8)
skipping: [localhost] => (item=molecule_local/pycontribs/ubuntu:latest)
skipping: [localhost] => (item=molecule_local/pycontribs/centos:7)

TASK [Create docker network(s)] ************************************************

TASK [Determine the CMD directives] ********************************************
ok: [localhost] => (item={'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True})
ok: [localhost] => (item={'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True})

TASK [Create molecule instance(s)] *********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) creation to complete] *******************************
FAILED - RETRYING: Wait for instance(s) creation to complete (300 retries left).
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '857710181840.389335', 'results_file': '/root/.ansible_async/857710181840.389335', 'changed': True, 'failed': False, 'item': {'image': 'quay.io/centos/centos:stream8', 'name': 'instance', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '773138588987.389368', 'results_file': '/root/.ansible_async/773138588987.389368', 'changed': True, 'failed': False, 'item': {'image': 'pycontribs/ubuntu:latest', 'name': 'ubuntu', 'pre_build_image': True}, 'ansible_loop_var': 'item'})
changed: [localhost] => (item={'started': 1, 'finished': 0, 'ansible_job_id': '419785455886.389394', 'results_file': '/root/.ansible_async/419785455886.389394', 'changed': True, 'failed': False, 'item': {'image': 'pycontribs/centos:7', 'name': 'centos', 'pre_build_image': True}, 'ansible_loop_var': 'item'})

PLAY RECAP *********************************************************************
localhost                  : ok=5    changed=2    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0

INFO     Running default > prepare
WARNING  Skipping, prepare playbook not configured.
INFO     Running default > converge
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [instance]
ok: [centos]

TASK [Include vector] **********************************************************

TASK [vector-role : Create directory vector] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [instance]
changed: [ubuntu]

TASK [vector-role : Get vector distrib] ****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [ubuntu]
changed: [instance]

TASK [vector-role : Unarchive vector] ******************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [instance]
changed: [ubuntu]
changed: [centos]

TASK [vector-role : Create a symbolic link] ************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [instance]
changed: [ubuntu]

TASK [vector-role : Create vector unit flie] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [instance]
changed: [ubuntu]

TASK [vector-role : Mkdir vector data] *****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [ubuntu]
changed: [instance]

TASK [vector-role : Vector config create] **************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
changed: [centos]
changed: [instance]
changed: [ubuntu]

PLAY RECAP *********************************************************************
centos                     : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
instance                   : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Running default > idempotence
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,

PLAY [Converge] ****************************************************************

TASK [Gathering Facts] *********************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [instance]
ok: [centos]

TASK [Include vector] **********************************************************

TASK [vector-role : Create directory vector] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [centos]
ok: [instance]

TASK [vector-role : Get vector distrib] ****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [ubuntu]
ok: [instance]

TASK [vector-role : Unarchive vector] ******************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
skipping: [centos]
skipping: [ubuntu]
skipping: [instance]

TASK [vector-role : Create a symbolic link] ************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [instance]
ok: [centos]

TASK [vector-role : Create vector unit flie] ***********************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [ubuntu]
ok: [instance]

TASK [vector-role : Mkdir vector data] *****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [instance]
ok: [ubuntu]

TASK [vector-role : Vector config create] **************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos]
ok: [ubuntu]
ok: [instance]

PLAY RECAP *********************************************************************
centos                     : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
instance                   : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
ubuntu                     : ok=7    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Idempotence completed successfully.
INFO     Running default > side_effect
WARNING  Skipping, side effect playbook not configured.
INFO     Running default > verify
INFO     Running Ansible Verifier
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,

PLAY [Verify] ******************************************************************

TASK [Gathering Facts] *********************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [instance]
ok: [ubuntu]
ok: [centos]

TASK [Get Vector version] ******************************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [instance]
ok: [centos]

TASK [Assert Vector instalation] ***********************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos] => {
    "changed": false,
    "msg": "All assertions passed"
}
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [instance] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [ubuntu] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Validation Vector configuration] *****************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu]
ok: [centos]
ok: [instance]

TASK [Assert Vector validate config] *******************************************
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [centos] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [instance] => {
    "changed": false,
    "msg": "All assertions passed"
}
[WARNING]: Collection community.docker does not support Ansible version 2.10.17
ok: [ubuntu] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY RECAP *********************************************************************
centos                     : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
instance                   : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu                     : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

INFO     Verifier completed successfully.
INFO     Running default > cleanup
WARNING  Skipping, cleanup playbook not configured.
INFO     Running default > destroy
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/pkey.py:82: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "cipher": algorithms.TripleDES,
/opt/hw_ansible_4/playbook/roles/vector-role/.tox/py39-ansible30/lib/python3.9/site-packages/paramiko/transport.py:253: CryptographyDeprecationWarning: TripleDES has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.TripleDES and will be removed from this module in 48.0.0.
  "class": algorithms.TripleDES,
[WARNING]: Collection community.docker does not support Ansible version 2.10.17

PLAY [Destroy] *****************************************************************

TASK [Destroy molecule instance(s)] ********************************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Wait for instance(s) deletion to complete] *******************************
changed: [localhost] => (item=instance)
changed: [localhost] => (item=ubuntu)
changed: [localhost] => (item=centos)

TASK [Delete docker networks(s)] ***********************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

INFO     Pruning extra files from scenario ephemeral directory
  py39-ansible210: OK (117.95=setup[33.89]+cmd[84.06] seconds)
  py39-ansible30: OK (109.34=setup[37.04]+cmd[72.29] seconds)
  congratulations :) (227.56 seconds)
(venv) root@ansible-ubuntu:/opt/hw_ansible_4/playbook/roles/vector-role#
```

</details>

9. Добавьте новый тег на коммит с рабочим сценарием в соответствии с семантическим версионированием.

[v1.1.0](https://github.com/stepynin-georgy/vector-role/releases/tag/1.1.0)

После выполнения у вас должно получится два сценария molecule и один tox.ini файл в репозитории. Не забудьте указать в ответе теги решений Tox и Molecule заданий. В качестве решения пришлите ссылку на  ваш репозиторий и скриншоты этапов выполнения задания. 

## Необязательная часть

1. Проделайте схожие манипуляции для создания роли LightHouse.
2. Создайте сценарий внутри любой из своих ролей, который умеет поднимать весь стек при помощи всех ролей.
3. Убедитесь в работоспособности своего стека. Создайте отдельный verify.yml, который будет проверять работоспособность интеграции всех инструментов между ними.
4. Выложите свои roles в репозитории.

В качестве решения пришлите ссылки и скриншоты этапов выполнения задания.

---

### Как оформить решение задания

Выполненное домашнее задание пришлите в виде ссылки на .md-файл в вашем репозитории.
