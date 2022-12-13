## 1. Установка jenkins на centos;
https://www.jenkins.io/doc/book/installing/linux/

## 2. Создал jenkins файл и ansible playbook для установки MariaDb;
https://github.com/GhubOsua/jenkins_ansible

## 3. Настройки в jenkins;
Прописываешь ключ приватны ssh в jenkins http://192.168.4.5:8080/manage/credentials/ , чтобы через контроллер jenkins по УЗ vagrant можно было использовать ansbile;
Сам файл jenkins:
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
