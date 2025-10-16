Library('ulrich-shared-library')_

pipeline {
    environement {
        IMAGE_NAME = 'webapp'
        IMAGE_TAG = 'v1'
        DOCKER_PASSWORD = credentilas('docker-password')
        DOCKER_USERNAME = 'ulrichsteve'
        HOST_PORT = 80
        CONTAINER_PORT = 80
        IP_DOCKER = '172.17.0.1'
    }
    agnet any
    stages  {
         stage ('build'){
            steps {
                script{
                    sh '''
                        docker build --no-cache -t $IMAGE_NAME:$IMAGE_TAG .
                    '''
                }
                }
            }
         stage ('Test'){
                steps {
                    script{
                        sh '''
                            docker run --rm -dp $HOST_PORT:CONTAINER_PORT --name $IMAGE_NAME $IMAGE_NAME:$IMAGE_TAG
                            sleep 5
                            curl -I http://$IP_DOCKER
                            sleep 5
                            docker stop $IMAGE_NAME 
                        '''
                    }
                }
            }
         stage ('Release'){
                steps {
                    script{
                        sh '''
                            docker tag $IMAGE_NAME:$IMAGE_TAG $DOCKER_USERNAME/$IMAGE_NAME:$IMAGE_TAG
                            echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                            ducker push $DOCKER_USERNAME/$IMAGE_NAME:$IMAGE_TAG
                        '''
                    }
                }
            }    
         stage ('Deploy review'){
                environement{
                    SERVER_IP = '16.171.172.129'
                    SERVER_USERNAME = 'ubuntu'
                }
                steps {
                    script{
                        timeout(time: 30, unit: "MINUTES"){
                            input message: "Voulez vous realiser un deploiement sur l'environnement de review ?", ok: 'YES'
                        }
                        sshagnet(['Key-pair']){
                        sh '''
                            echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                            ssh -o StricHostKeyCheking=no -1 $SERVER_USERNAME $SERVER_IP "docker rm -f $IMAGE_NAME || echo 'All deleted"  
                            ssh -o StricHostKeyCheking=no -1 $SERVER_USERNAME $SERVER_IP "docker pull $DOCKER_USERNAME/$IMAGE_NAME:$IMAGE_TAG || echo "Image Download successFully"
                            sleep 30
                            ssh -o StricHostKeyCheking=no -1 $SERVER_USERNAME $SERVER_IP "docker run --rm -dp $HOST_PORT:$CONTAINER_PORT --name $IMAGE_NAME $DOCKER_USERNAME/$IMAGE_NAME:$IMAGE_TAG
                            sleep 5
                            curl -l http://$SERVER_IP:$HOST_PORT
                        '''
                        }

                    }
                }                           
            }
         stage ('Deploy Staging'){
                   environement{
                    SERVER_IP = '13.61.11.35'
                    SERVER_USERNAME = 'ubuntu'
                }
                steps {
                    script{
                         timeout(time: 30, unit: "MINUTES"){
                            input message: "Voulez vous realiser un deploiement sur l'environnement de Staging ?", ok: 'YES'
                        }
                        sshagnet(['Key-pair']){
                        sh '''
                            echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                            ssh -o StricHostKeyCheking=no -1 $SERVER_USERNAME $SERVER_IP "docker rm -f $IMAGE_NAME || echo 'All deleted"  
                            ssh -o StricHostKeyCheking=no -1 $SERVER_USERNAME $SERVER_IP "docker pull $DOCKER_USERNAME/$IMAGE_NAME:$IMAGE_TAG || echo "Image Download successFully"
                            sleep 30
                            ssh -o StricHostKeyCheking=no -1 $SERVER_USERNAME $SERVER_IP "docker run --rm -dp $HOST_PORT:$CONTAINER_PORT --name $IMAGE_NAME $DOCKER_USERNAME/$IMAGE_NAME:$IMAGE_TAG
                            sleep 5
                            curl -l http://$SERVER_IP:$HOST_PORT
                        '''
                        }

                    }
                }                           
            }
         stage ('Deploy Prod'){
                // when {expression{GIT_BRANCH -- 'origin/prod'}}
                environement{
                    SERVER_IP = '51.21.196.195'
                    SERVER_USERNAME = 'ubuntu'
                }
                steps {
                    script{
                         timeout(time: 30, unit: "MINUTES"){
                            input message: "Voulez vous realiser un deploiement sur l'environnement de Prod ?", ok: 'YES'
                        }
                        sshagnet(['Key-pair']){
                        sh '''
                            echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                            ssh -o StricHostKeyCheking=no -1 $SERVER_USERNAME $SERVER_IP "docker rm -f $IMAGE_NAME || echo 'All deleted"  
                            ssh -o StricHostKeyCheking=no -1 $SERVER_USERNAME $SERVER_IP "docker pull $DOCKER_USERNAME/$IMAGE_NAME:$IMAGE_TAG || echo "Image Download successFully"
                            sleep 30
                            ssh -o StricHostKeyCheking=no -1 $SERVER_USERNAME $SERVER_IP "docker run --rm -dp $HOST_PORT:$CONTAINER_PORT --name $IMAGE_NAME $DOCKER_USERNAME/$IMAGE_NAME:$IMAGE_TAG
                            sleep 5
                            curl -l http://$SERVER_IP:$HOST_PORT
                        '''
                    }

                }
            }                           
        }
        post{
            always {
                script
                {
                    slackNotifier currentBuild.result
                }   
            }
        }
    }
}                
             
