pipeline{
    agent none
    environment{
        BUILD_SERVER_IP='ec2-user@172.31.4.110'
       IMAGE_NAME='pakajayamadhuri/devops_practice:php$BUILD_NUMBER'
       DEPLOY_SERVER_IP='ec2-user@172.31.10.207'
    }

    stages{
        //terraform //ansible
        stage('BUILD PHP DOCKERIMAGE AND PUSH TO DOCKERHUB'){
            agent any
            steps{
                script{
                sshagent(['slave2']) {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                echo "Building the php image"
                sh "scp -o StrictHostKeyChecking=no -r BuildConfig ${BUILD_SERVER_IP}:/home/ec2-user"
                sh "ssh -o StrictHostKeyChecking=no ${BUILD_SERVER_IP} 'bash ~/BuildConfig/docker-script.sh'"
                sh "ssh ${BUILD_SERVER_IP} sudo docker build -t ${IMAGE_NAME} /home/ec2-user/BuildConfig/"
                sh "ssh ${BUILD_SERVER_IP} sudo docker login -u $USERNAME -p $PASSWORD"
                sh "ssh ${BUILD_SERVER_IP} sudo docker push ${IMAGE_NAME}"
                }
            }
        }
    }
}
stage('RUN PHP_DB with Dockercompose'){
            agent any
            steps{
                script{
                sshagent(['slave2']) {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                echo "Deploy PHP and Sql containers"
                sh "scp -o StrictHostKeyChecking=no -r DeployConfig ${DEPLOY_SERVER_IP}:/home/ec2-user"
                sh "ssh -o StrictHostKeyChecking=no ${DEPLOY_SERVER_IP} 'bash ~/DeployConfig/docker-script.sh'"
                sh "ssh ${DEPLOY_SERVER_IP} sudo docker login -u $USERNAME -p $PASSWORD"
                sh "ssh ${DEPLOY_SERVER_IP} bash /home/ec2-user/DeployConfig/docker-compose-script.sh ${IMAGE_NAME}"
                }
            }
        }
    }
}
    }
}