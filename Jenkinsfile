pipeline {
    agent {
        label "java-slave"
    }
    stages {
        stage ("build") {
            steps {
                mvn clean package
            }
        }
    }
}