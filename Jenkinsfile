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
        SONAR_URL = "http://34.51.5.154:9000"
        SONAR_TOKEN = credentials("sonar_creds")
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
                echo "*****starting sonar scan*******"

                withSonarQubeEnv ("SonarQube") {

                    sh """
                       mvn clean verify sonar:sonar \
                        -Dsonar.projectKey=Eureka-Application \
                        -Dsonar.host.url=${env.SONAR_URL} \
                        -Dsonar.login=${env.SONAR_TOKEN} 
                    """
                }
                timeout (time: 2, unit: "MINUTES"){
                    script {
                        waitForQualityGate abortPipeline: true
                    }
                }    
            }
        }
    }
}