pipeline {
    agent {
        label "java-slave"
    }
    stages {
        stage ("build") {
            steps {
                sh "mvn clean package"
            }
        }
    }
}