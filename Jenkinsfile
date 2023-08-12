pipeline{
    agents any
    triggers{
        pollSCM('*/1 * * * *')
    }
    stages{
        stage('build application'){

            steps{
                echo "build python-greetings-app"
            }
        }
        stage('deploy-dev'){

            steps{
                echo "Deploying python-greetings-app on dev"
            }
        }
        stage('test-dev'){

            steps{
                echo "Testing python-greetings-app on dev"
            }
        }
        stage('approval'){

            steps{
                echo "Manual approval before deployment to PROD"
            }
        }
        
        stage('deploy-prod'){

            steps{
                echo "Deploying python-greetings-app on prod"
            }
        }
        stage('test-prod'){

            steps{
                echo "Testing python-greetings-app on prod"
            }
        }
    }
}