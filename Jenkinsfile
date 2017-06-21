#!/usr/bin/env groovy

  stage('Retrieve') {
    node {
      deleteDir()
      checkout scm
      sh 'az keyvault secret show --vault-name \'HMPP-KV-Duplicity\' --name \'ContainerKey\' | grep -i value | awk \'{print $2}\' | sed  \'s/\\"//g\' > .ContainerKey'
      sh 'az keyvault secret show --vault-name \'HMPP-KV-Duplicity\' --name \'ContainerName\' | grep -i value | awk \'{print $2}\' | sed  \'s/\\"//g\' > .ContainerName'
      container_key = readFile '.ContainerKey'
      container_name = readFile '.ContainerName'
    }
  }

  stage('Compile') {
    node {
      sh "chmod 400 ./keys/key.pem"
      }
  }

  stage('Backups') {
    node {
      git branch: 'master', credentialsId: 'c17c8237-bdce-4626-b448-a9a11f99f6d9', url: 'git@gitlab.azure.noms.root:ansible/ansible-role-backup.git'
	  sh ' ansible-vault decrypt  --vault-password-file ~/vault-password passfile'
      sh 'chmod 400 ./keys/key.pem; echo "exec cat" > apcat.sh;  chmod a+x apcat.sh'
      sh 'export ANSIBLE_CONFIG="ansible.cfg"'
      sh """cat <<EOF > ./new.ini
[azure]
tags=uuid:${env.UUID}
EOF"""
      sh "export ANSIBLE_HOST_KEY_CHECKING=False; export AZURE_INI_PATH=./new.ini; eval `ssh-agent`;/bin/cat passfile|SSH_ASKPASS=./apcat.sh ssh-add ./keys/key.pem; ansible-playbook -i ./inventory/newjenkins.txt --vault-password-file /var/lib/jenkins/vault-password -u abootstrap   --extra-vars='container_name=${container_name} container_key=${container_key}' --tags=runbackup master.yml; ssh-add -D"
   }
}
