pipeline {
    agent {
        label "java-slave"
    }
    tools {
        maven "maven:latest"
        jdk "JDK:latest"
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