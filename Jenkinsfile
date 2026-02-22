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
        POM_VERSION = readMavenPom().getVersion()
        POM_PACKAGING = readMavenPom().getPackaging()
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
        stage ("Formatting") {
            steps {
                //existing i27-eureka-0.0.1-SNAPSHOT.jar
                //existing i27-eureka-buildnumber-branchname.jar
                echo "testing jar source:i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING}"
                echo "testing jar destination:i27-${env.APPLICATION_NAME}-${BUILD_NUMBER}-${BRANCH_NAME}.${env.POM_PACKAGING}"


            }
        }
        stage ("DockerBuildANDPush") {
            steps {
                echo "*** Building the Docker Image *****"
                sh "cp /home/jenkins/workspace/Eureka_application_main/target/i27-${env.APPLICATION_NAME}-${env.POM_VERSION}-${POM_PACKAGING} ./.cicd"
                sh "docker build --build-arg JAR_SOURCE=i27-${env.APPLICATION_NAME}-${env.POM_VERSION}-${POM_PACKAGING} -t eureka:v1 ./.cicd"
            }
        }
    }
}