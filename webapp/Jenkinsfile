node {
    stage('Git checkout') {
        git 'https://github.com/malkiats/Projects.git'
    }
    
    stage('sending docker file to ansible') {
        sshagent(['ansible']) {
            sh 'ssh -o StrictHostKeyChecking=no -l root 192.168.100.41'
            sh 'scp /var/lib/jenkins/workspace/webapp/* root@192.168.100.41:/data/dockerfile/'
            //sh 'scp /var/lib/jenkins/workspace/webapp/* root@192.168.100.41:/data/dockerfile/'
        }
    }
    stage("Docker image build"){
        sshagent(['ansible']) {
            sh 'ssh -o StrictHostKeyChecking=no -l root 192.168.100.41 cd /data/dockerfile/'
            sh 'ssh -o StrictHostKeyChecking=no -l root 192.168.100.41 docker image build -t $JOB_NAME:v1.$BUILD_ID /data/dockerfile/.'
        }
    }
    stage("Docker image tag & push"){
        sshagent(['ansible']) {
            sh 'ssh -o StrictHostKeyChecking=no -l root 192.168.100.41'
            sh 'ssh -o StrictHostKeyChecking=no -l root 192.168.100.41 docker image tag $JOB_NAME:v1.$BUILD_ID malkiats/$JOB_NAME:v1.$BUILD_ID'
            sh 'ssh -o StrictHostKeyChecking=no -l root 192.168.100.41 docker image tag $JOB_NAME:v1.$BUILD_ID malkiats/$JOB_NAME:latest'
            sh 'ssh -o StrictHostKeyChecking=no -l root 192.168.100.41 docker image push malkiats/$JOB_NAME:v1.$BUILD_ID'
            sh 'ssh -o StrictHostKeyChecking=no -l root 192.168.100.41 docker image push malkiats/$JOB_NAME:latest'
            
            sh 'ssh -o StrictHostKeyChecking=no -l root 192.168.100.41 docker rmi -f malkiats/$JOB_NAME:v1.$BUILD_ID malkiats/$JOB_NAME:latest $JOB_NAME:v1.$BUILD_ID'
        }
    }
    stage("Deploy playbook"){
        sshagent(['ansible']) {
            sh 'ssh -o StrictHostKeyChecking=no -l root 192.168.100.41'
            sh 'scp /data/dockerfile/* root@192.168.100.54:/data/dockerfile/'
            //sh 'ssh -o StrictHostKeyChecking=no -l root 192.168.100.41'
            sh 'ssh -o StrictHostKeyChecking=no -l root 192.168.100.41 ansible-playbook /data/playbook/playbook-devtech.yml'
        }
    }
}
