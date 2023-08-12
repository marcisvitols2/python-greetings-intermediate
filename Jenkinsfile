pipeline{
    agent any
    triggers{
        pollSCM('*/1 * * * *')
    }
    stages{
        stage('build application'){

            steps{
                script{
                    echo "build python-greetings-app"
                    build("marcisvitols/python-greetings-app:latest", "Dockerfile")
                } 
            }
        }
        stage('deploy-dev'){

            steps{
                script{
                    deploy("DEV")
                }
            }
        }
        stage('test-dev'){

            steps{
                script{
                    test("DEV")
                }
            }
        }
        stage('approval'){

            steps{
                echo "Manual approval before deployment to PROD"
            }
        }
        
        stage('deploy-prod'){

            steps{
                script{
                    deploy("PROD")
                }
            }
        }
        stage('test-prod'){

            steps{
                script{
                    test("PROD")
                }
            }
        }
    }
    post {
        failure{
            script{
                echo "Pipeline failure..sending notif"
                //invoke discord plugin
            }
        }
        cleanup{
            echo "Cleanup"
        }
    }
}
def build(String tag, String dockerfile){
    echo "Building ${tag} image for api-tests base ${dockerfile}"
    sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
    sh "docker build -t ${tag} . -f ${dockerfile}"
    sh "docker push ${tag}"
}
def test(String environment){
    echo "Testing of python-greetings-app on ${environment} is starting"
}

def deploy(String environment){
    echo "Deployment of python-greetings-app on ${environment} is starting"
    
}