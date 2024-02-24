pipeline {
    agent any
    environment {
        APP_PORT='9090'
        APP_NAME="${env.JOB_NAME}"
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B package -DskipTests'
            }
        }
        stage('Integration Test') {
            parallel{
                stage('Running Application') {
                    agent any
                    options {
                        timeout(time: 60, unit: 'SECONDS')
                    }
                    steps {
                        script {
                            try {
                                dir("${env.WORKSPACE}/target"){
                                    sh "pwd"
                                    sh "java -jar contact.war"
                                }
                            } catch (Throwable e) {
                                echo "Caught ${e.toString()}"
                                currentBuild.result = "SUCCESS"
                            }
                        }
                    }
                }
                stage('Running Test') {
                    steps {
                        sleep(time:30, unit: 'SECONDS')
                        sh 'mvn test -Dtest=RestIT'
                    }
                }
            }
        }        
    }
}
