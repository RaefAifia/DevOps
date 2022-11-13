pipeline {
    agent any
    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }
    environment{
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "192.168.33.10:8081/"
        NEXUS_REPOSITORY = "maven_releases"
        NEXUS_CREDENTIAL_ID = "nexus_user_credentials"
        dockerhub = credentials('dockerhub')
    }
    stages {
        stage('GIT Checkout') {
            steps {
                echo 'Pulling code from Git'
                git branch: 'medwassimhabibi', 
                url: 'https://github.com/JmSkan141195/RepoDevOpsTest.git'
            }
        }
        stage('Building JAR') {
            steps {
                echo 'Building with Maven'
                sh 'mvn clean install -DskipTests'
            }
        }
        stage('SonarQube') {
            steps {
                echo 'SonarQube'
                sh 'mvn sonar:sonar -Dsonar.login=8201febc1c220f337223156baef397799143a4fb'
            }
        }
        stage('JUnit') {
            steps {
                echo 'Testing Facture Services'
                sh 'mvn test'
            }
        }
        stage('Nexus Deployment') {
            steps {
                echo 'Nexus ...'
                sh 'mvn deploy:deploy-file -DgroupId=com.esprit.examen -DartifactId=tpAchatProject -Dversion=1.0 -DgenerationPom=true -Dpackaging=jar -DrepositoryId=deploymentRepo -Durl=http://192.168.33.10:8081/repository/maven-releases -Dfile=target/tpAchatProject-1.0.jar'
            }
        }
        //login to dockerhub
        stage('Login to DockerHub') {
            steps {
                echo 'Login to DockerHub'
                sh "docker login -u $dockerhub_USR -p $dockerhub_PSW"
            }
        }

        //build docker image
        stage('Build Docker Image') {
            steps {
                echo 'Build Docker Image'
                sh "docker build -t medwassimhabibi/examen_devops:1.0 ."
            }
        }

        //push docker image 
        stage('Push Docker Image') {
            steps {
                echo 'Push Docker Image'
                sh "docker push medwassimhabibi/examen_devops:1.0"
            }
        }
    }
}