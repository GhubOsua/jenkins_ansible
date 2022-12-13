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
##### Хотел еще через jenkins реализовать docker , но времене нет, перед НГ много текущих задач и каждым нужно тех. задания выполнять. Прошу прощения, за такой черновик. Капитально застрял и не понимал как прокидывается ключ от vagrant ssh.
