pipeline{
    agent any
    triggers{
        pollSCM('*/1 * * * *')
    }
    parameters{
        choice(name: 'DEPLOY_TO_PRODUCTION', choices: ['Yes', 'No'], description: "Would you like to deploy application to PRD as well?")

    }
    environment{
        DOCKER_PASSWORD = credentials('docker-password')
        DOCKER_USERNAME = credentials('docker-username')
    }
    stages{
        stage('build-app'){
            steps{
                script{
                    echo "Build ${GIT_COMMIT}"
                    echo "build python-greetings-app"
                    build("marcisvitols/python-greetings-app:${GIT_COMMIT}", "Dockerfile")
                } 
            }
        }
        stage('deploy-dev'){

            steps{
                script{
                    deploy("dev")
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

            steps {
                script{
                echo "Manual approval before deployment to PROD.."
                def deploymentSleepDelay = input id: 'Deploy', message: 'Should we procced with deployment to production?', submitter:'marcis,admin',
                                            parameters: [choice(choices: ['0','1', '5', '10'], description: 'Minutes to delay (sleep) deployment:', name: 'DEPLOYMENT_DELAY')]
                sleep time: deploymentSleepDelay.toInteger(), unit: 'MINUTES'
                }
            }
        }
        
        stage('deploy-prod'){
            when{
                expression{params.DEPLOY_TO_PRODUCTION == 'Yes'}
            }
            steps{
                script{
                    deploy("prod")
                }
            }
        }
        stage('test-prod'){
            when{
                expression{params.DEPLOY_TO_PRODUCTION == 'Yes'}
            }
            steps{
                script{
                    test("PRD")
                }
            }
        }
    }
    post {
        always{
            script{
                discordSend description: "Jenkins Pipeline Build - Marcis", footer: "Footer Text", link: env.BUILD_URL, result: currentBuild.currentResult, title: JOB_NAME, webhookURL: "https://discord.com/api/webhooks/1139668547946168561/ZHUF4ww-auCPadJknX44uhBS1utUe0SzBEWXrVmMZVFujKLfMY6a6l7L33fEQd9G-_4p"
            }
        }
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
def test(String test_environment){
        echo "Testing of python-greetings-app on ${test_environment} is starting.."
    //     sh "docker run --network=host -t -d --name api_tests_runner_${test_environment} marcisvitols/api-tests-runner:latest"
    // try{
    //     sh "docker exec api_tests_runner_${test_environment} ls"
    //     sh "docker exec api_tests_runner_${test_environment} cucumber PLATFORM=${test_environment} --format html --out test-output/report.html"     }
    // finally{
    //     sh "docker cp api_tests_runner_${test_environment}:/api-tests/test-output/report.html report_${test_environment}.html"
    //     sh "docker ps"
    //     sh "docker rm -f api_tests_runner_${test_environment}"
    // }

}

def deploy(String deploy_environment){
    echo "Deployment of python-greetings-app on ${deploy_environment} is starting"
    sh "kubectl set image deployment python-greetings-${deploy_environment} python-greetings-${deploy_environment}-pod=marcisvitols/python-greetings-app:${GIT_COMMIT}"
    sh "kubectl scale deploy python-greetings-${deploy_environment} --replicas=0"
    sh "kubectl scale deploy python-greetings-${deploy_environment} --replicas=2"

}