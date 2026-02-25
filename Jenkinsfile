pipeline {
    agent {
        label "java-slave"
    }

    tools {
        maven "maven:latest"
        jdk "JDK:latest"
    }

    environment {
        POM_VERSION = readMavenPom().getVersion()
        POM_PACKAGING = readMavenPom().getPackaging()
        APPLICATION_NAME = "eureka"
        SONAR_URL = "http://34.51.5.154:9000"
        SONAR_TOKEN = credentials("sonar_creds")
        DOCKER_HUB = "docker.io/surendra1520"
    }

    stages {

        stage ("Build") {
            steps {
                echo "*** Building ${env.APPLICATION_NAME} application ***"
                sh "mvn clean package -DskipTests=true"
            }
        }

        stage ("Sonar") {
            steps {
                echo "***** Starting Sonar Scan *****"

                withSonarQubeEnv("SonarQube") {
                    sh """
                        mvn clean verify sonar:sonar \
                        -Dsonar.projectKey=Eureka-Application \
                        -Dsonar.host.url=${env.SONAR_URL} \
                        -Dsonar.login=${env.SONAR_TOKEN}
                    """
                }

                timeout(time: 2, unit: "MINUTES") {
                    script {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }

        stage ("Formatting") {
            steps {
                echo "Jar source: i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING}"
                echo "Jar destination: i27-${env.APPLICATION_NAME}-${env.BUILD_NUMBER}.${env.POM_PACKAGING}"
            }
        }

        stage ("DockerBuildAndPush") {
            steps {
                echo "*** Building the Docker Image *****"

                // Copy jar into .cicd folder
                sh """
                    cp target/i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING} ./.cicd/
                    ls -l ./.cicd
                """

                // Build Docker image
                sh """
                    docker build --no-cache \
                    --build-arg JAR_SOURCE=i27-${env.APPLICATION_NAME}-${env.POM_VERSION}.${env.POM_PACKAGING} \
                    -t ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:${env.BUILD_NUMBER} ./.cicd
                """

                echo "**** Docker Login *****"

                // Secure Docker Login & Push
                withCredentials([usernamePassword(
                    credentialsId: 'Docker_creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                        echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                        docker push ${env.DOCKER_HUB}/${env.APPLICATION_NAME}:${env.BUILD_NUMBER}
                    """
                }
            }
        }

        stage ("Deploy to Dev") {
            steps {
                echo "Deploying to dev env"
                sshpass
                withCredentials([usernamePassword(credentialsId: 'Docker_vm_creds', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                // some block
                sh "sshpass -P $PASSWORD -v ssh -o StrictHostKeyChecking=no $USERNAME@$DOCKER_VM_IP \"whoami\""
               }
            }
        }
    }
}