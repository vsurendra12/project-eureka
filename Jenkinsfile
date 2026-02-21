pipeline {
    agent {
        label "java-slave"
    }
    stages {
        stage ("build") {
            steps {
                echo "***building eureka application***"
                sh "mvn clean package"
            }
        }
    }
}