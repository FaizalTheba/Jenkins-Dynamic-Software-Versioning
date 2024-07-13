def gv

pipeline {
    agent any
    tools {
        maven 'Maven-3.9.8'
    }
    stages {
        stage('increment version') {
            steps {
                script {
                        echo "incrementing app version..."
                        sh 'mvn build-helper:parse-version versions:set \
                                -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                                versions:commit'
                        def matcher = readFile('pom.xml') =~ '‹version> (.+)</version>'
                        def version = matcher [0][1]
                        env.IMAGE_NAME = "$version-$BUILD_NUMBER"
                }
            }
        }
        stage('build app') {
            steps {
                script {
                    echo "Building the application..."
                    sh 'mvn clean package'
                }
            }
        }
        stage('build image') {
            steps {
                script {
                    echo "Building the docker image..."
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "docker build -t <username>/java-maven-app-1.0:${IMAGE_NAME} ."
                        sh 'echo $PASS | docker login -u $USER --password-stdin'
                        sh "docker push <username>/java-maven-app-1.0:${IMAGE_NAME}"
                    }

                }
            }
        }
        stage('deploy') {
            steps {
                script {
                    echo "Deploying the application..."
                }
            }
        }
    }
}