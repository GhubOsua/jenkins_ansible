## 1. Установка jenkins на centos;
https://www.jenkins.io/doc/book/installing/linux/

## 2. Создал jenkins файл и ansible playbook для установки MariaDb;
https://github.com/GhubOsua/jenkins_ansible

## 3. Настройки в jenkins;
Прописываешь ключ приватны ssh в jenkins http://192.168.4.5:8080/manage/credentials/ , чтобы через контроллер jenkins по УЗ vagrant можно было использовать ansbile;
Сам файл jenkins:
```
pipeline {
  agent { label 'linux' }
  environment {
   ANSIBLE_PRIVATE_KEY=credentials('mariadb-private-key') 
  }
  stages {
    stage('Hello') {
      steps {
        sh 'ansible-galaxy collection install -r requirements.yml'
        sh 'ansible-playbook -i inventory/mariadb.hosts --private-key=$ANSIBLE_PRIVATE_KEY playbooks/mariadb.yml'
      }
    }
  }
}
```
## 4. По поводу параметра "разворачивать каждый тим серверов БД по параметру";
К сожалению не успеваю добавить в playbook, т.к. ночных смен добавились и не могу разорваться.
В playbook добавить
```
vars:
supported_distros:
- CentOS
- Ubuntu
```
И когда идет установка в yum, добавить when
```
- name: Install MariaDB server
      yum:
        name:
          - MariaDB-server
        state: latest
      when: ansible_distribution in supported_distros
 ```
Или несколько условий добавить:    when: ansible_distribution_version == "7.5" and ansible_kernel == "3.10.0-327.el7.x86_64"
Или более сложеные условия, как описано в книге по ansible
```
when: >
( ansible_distribution == "RedHat" and
ansible_distribution_major_version == "7" )
or
( ansible_distribution == "Fedora" and
ansible_distribution_major_version == "28" )
```
## Добавил изменения в playbook:
```
Started by user test_jenkins
Obtained Jenkinsfile-2 from git https://github.com/GhubOsua/jenkins_ansible.git
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins in /var/lib/jenkins/workspace/jenkins_pipeline_ansible
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Declarative: Checkout SCM)
[Pipeline] checkout
Selected Git installation does not exist. Using Default
The recommended git tool is: NONE
No credentials specified
 > git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/jenkins_pipeline_ansible/.git # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://github.com/GhubOsua/jenkins_ansible.git # timeout=10
Fetching upstream changes from https://github.com/GhubOsua/jenkins_ansible.git
 > git --version # timeout=10
 > git --version # 'git version 1.8.3.1'
 > git fetch --tags --progress https://github.com/GhubOsua/jenkins_ansible.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/main^{commit} # timeout=10
Checking out Revision 0357d5a48ea43ce0756cccfed34044db3de99503 (refs/remotes/origin/main)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 0357d5a48ea43ce0756cccfed34044db3de99503 # timeout=10
Commit message: "edit file"
 > git rev-list --no-walk d5464232f12f91d4c3f5b2a52f01c0941b076caf # timeout=10
[Pipeline] }
[Pipeline] // stage
[Pipeline] withEnv
[Pipeline] {
[Pipeline] withCredentials
Masking supported pattern matches of $ANSIBLE_PRIVATE_KEY or $ANSIBLE_PRIVATE_KEY_PSW
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Hello)
[Pipeline] sh
+ ansible-playbook -i inventory/mariadb.hosts --private-key=**** playbooks/mariadb.yml

PLAY [MariaDB installation] ****************************************************

TASK [Gathering Facts] *********************************************************
ok: [centOS7test2]
ok: [ubuntutest1]

TASK [Add repository CentOS] ***************************************************
[WARNING]: conditional statements should not include jinja2 templating
delimiters such as {{ }} or {% %}. Found: ansible_facts['distribution'] == "{{
supproted_distros_1 }}"
[WARNING]: conditional statements should not include jinja2 templating
delimiters such as {{ }} or {% %}. Found: ansible_facts['distribution'] == "{{
supproted_distros_1 }}"
skipping: [ubuntutest1]
changed: [centOS7test2]

TASK [Install MariaDB server for CentOS] ***************************************
[WARNING]: conditional statements should not include jinja2 templating
delimiters such as {{ }} or {% %}. Found: ansible_facts['distribution'] == "{{
supproted_distros_1 }}"
[WARNING]: conditional statements should not include jinja2 templating
delimiters such as {{ }} or {% %}. Found: ansible_facts['distribution'] == "{{
supproted_distros_1 }}"
skipping: [ubuntutest1]
changed: [centOS7test2]

TASK [start MariaDB service for CentOS] ****************************************
[WARNING]: conditional statements should not include jinja2 templating
delimiters such as {{ }} or {% %}. Found: ansible_facts['distribution'] == "{{
supproted_distros_1 }}"
[WARNING]: conditional statements should not include jinja2 templating
delimiters such as {{ }} or {% %}. Found: ansible_facts['distribution'] == "{{
supproted_distros_1 }}"
skipping: [ubuntutest1]
changed: [centOS7test2]

TASK [verify installation] *****************************************************
[WARNING]: conditional statements should not include jinja2 templating
delimiters such as {{ }} or {% %}. Found: ansible_facts['distribution'] == "{{
supproted_distros_1 }}"
[WARNING]: conditional statements should not include jinja2 templating
delimiters such as {{ }} or {% %}. Found: ansible_facts['distribution'] == "{{
supproted_distros_1 }}"
skipping: [ubuntutest1]
changed: [centOS7test2]

TASK [debug] *******************************************************************
[WARNING]: conditional statements should not include jinja2 templating
delimiters such as {{ }} or {% %}. Found: ansible_facts['distribution'] == "{{
supproted_distros_1 }}"
[WARNING]: conditional statements should not include jinja2 templating
delimiters such as {{ }} or {% %}. Found: ansible_facts['distribution'] == "{{
supproted_distros_1 }}"
skipping: [ubuntutest1]
ok: [centOS7test2] => {
    "mysql_version.stdout_lines": [
        "mysql  Ver 15.1 Distrib 10.6.11-MariaDB, for Linux (x86_64) using readline 5.1"
    ]
}

TASK [MariaDB install DB for Ubuntu] *******************************************
[WARNING]: conditional statements should not include jinja2 templating
delimiters such as {{ }} or {% %}. Found: ansible_facts['distribution'] == "{{
supproted_distros_2 }}"
[WARNING]: conditional statements should not include jinja2 templating
delimiters such as {{ }} or {% %}. Found: ansible_facts['distribution'] == "{{
supproted_distros_2 }}"
skipping: [centOS7test2]
changed: [ubuntutest1]

PLAY RECAP *********************************************************************
centOS7test2               : ok=6    changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
ubuntutest1                : ok=2    changed=1    unreachable=0    failed=0    skipped=5    rescued=0    ignored=0   

[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // withCredentials
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS
```

##### Хотел еще через jenkins реализовать docker , но времене нет, перед НГ много текущих задач и каждым нужно тех. задания выполнять. Прошу прощения, за такой черновик. Капитально застрял и не понимал как прокидывается ключ от vagrant ssh.
