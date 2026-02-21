pipeline {
    agent {
        label "java-slave"
    }

    //tools section
    tools {
        maven "maven:latest"
        jdk "JDK:latest"
    }

    //environment
    environment {
        APPLICATION_NAME = "eureka"
    } 
    stages {
        stage ("build") {
            steps {
                echo "***building ${env.APPLICATION_NAME} application***"
                sh "mvn clean package -Dskiptests=true"
            }
        }
        stage ("sonar") {
            steps {
                echo "*****starting sonar scan******"
                sh """
                   mvn clean verify sonar:sonar \
                     -Dsonar.projectKey=Eureka-Application \
                     -Dsonar.host.url=http://34.51.5.154:9000 \
                     -Dsonar.login=sqp_cecc443965ddfedaf8e98a448b674622e0a1b5d9 
                """
            }
        }
    }
}