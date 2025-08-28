pipeline {
  agent any

  tools {
    maven 'maven'   
  }

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/Waseem7839/addressbook-cicd-project.git', branch: 'main'
      }
    }

    stage('Build with Maven') {
      steps {
        sh 'mvn clean package -DskipTests'
      }
    }

    stage('Install Tomcat') {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'waseem',
                                           keyFileVariable: 'SSH_KEY',
                                           usernameVariable: 'SSH_USER')]) {
          
          sh '''
            ANSIBLE_HOST_KEY_CHECKING=False \
            ansible-playbook -i "13.41.226.187," -u $SSH_USER --private-key $SSH_KEY ansible/tomcat-setup.yml
          '''
        }
      }
    }

    stage('Deploy WAR') {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: 'waseem',
                                           keyFileVariable: 'SSH_KEY',
                                           usernameVariable: 'SSH_USER')]) {
          sh '''
             ANSIBLE_HOST_KEY_CHECKING=False \
            ansible-playbook -i "13.41.226.187," -u $SSH_USER --private-key $SSH_KEY \
             ansible/deploy.yml --extra-vars "war_file=${WORKSPACE}/target/addressbook.war app_name=addressbook"
          '''
        }
      }
    }
  }
}
