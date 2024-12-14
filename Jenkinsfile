pipeline {
    agent any
    // Checks changes in repo each minute. If there are changes it performs test execution
    triggers {
        pollSCM('*/1 * * * *')
    }
    stages {
        stage('build-docker-image') {
            steps {
                buildDockerImage()
            }
        }
        stage('deploy-dev') {
            steps {
                deploy("DEV")
            }
        }
        stage('test-dev') {
            steps {
                runApiTests("DEV")
            }
        }
        stage('deploy-stg') {
            steps {
                deploy("STG")
            }
        }
        stage('test-stg') {
            steps {
                runApiTests("STG")
            }
        }
        stage('deploy-prd') {
            steps {
                deploy("PRD")
            }
        }
        stage('test-prd') {
            steps {
                runApiTests("PRD")
            }
        }
    }
}

def buildDockerImage(){
    echo "Building docker image..."
    sh "docker build -t kvinnika/sample-book-app ."

    echo "Pushing image to docker registry.."
    sh "docker push kvinnika/sample-book-app"
}

def deploy(String environment){
    echo "Deployment triggered on ${environment} env.."
    String lowercaseEnv = environment.toLowerCase()
    sh "docker compose stop sample-book-app-${lowercaseEnv}"
    sh "docker compose rm sample-book-app-${lowercaseEnv}"
    sh "docker compose up -d sample-book-app-${lowercaseEnv}"
}

def runApiTests(String environment){
    echo "API tests triggered on ${environment} env.."
    sh "docker pull kvinnika/js-api-tests"
    sh "docker run --network=host --rm kvinnika/js-api-tests run BOOKS BOOKS_${environment}"
}


