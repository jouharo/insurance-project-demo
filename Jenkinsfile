node{
    stage('checkout'){
        git 'https://github.com/jouharo/insurance-project-demo.git'
    }
    stage('maven build'){
        sh 'mvn clean package'
    }
    stage('containerize'){
        sh 'docker build -t jouharo/insure-me:1.0 .'
    }
    stage('release'){
        withCredentials([string(credentialsId: 'dockerHubPwd', variable: 'dockerHubPwd')]) {
        sh "docker login -u jouharo -p ${dockerHubPwd}"
        sh 'docker push jouharo/insure-me:1.0'
        }
    }
    stage('Deploy to test'){
        ansiblePlaybook become: true, credentialsId: 'ansible-key', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'configure-test-server.yml', vaultTmpPath: ''
    }
    stage('Checkout regression test code'){
        git 'https://github.com/jouharo/my-selenium-test-app.git'
    }
    stage('maven build'){
        sh 'mvn clean package assembly:single'
    }
    stage('Execute selenium scripts'){
        sh 'java -jar target/my-app-test-0.0.1-SNAPSHOT-jar-with-dependencies.jar'
    }
    stage('checkout the code - prod deployment'){
        git 'https://github.com/jouharo/insurance-project-demo.git'
    }
    stage('Deploy to prod'){
        ansiblePlaybook become: true, credentialsId: 'ansible-key', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'configure-prod-server.yml', vaultTmpPath: ''
    }
}
