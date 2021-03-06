pipeline {
    agent any
    stages {
        stage('Build Backend') {
            steps {
                sh 'mvn clean package -DskipeTest=true'
            }
        }
        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Deploy Backend') {
            steps {
                deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage('API Test') {
            steps {
                dir('api-teste') {
                    git 'https://github.com/Leandrofdx/apitasks'
                    sh 'mvn test'
                }
            }
        }
        stage('Deploy Front-end') {
            steps {
                dir('tasks-frontend') {
                    git 'https://github.com/Leandrofdx/tasks-frontend'
                    sh 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }
        stage('Funcional Test') {
            steps {
                dir('funci-teste') {
                    git 'https://github.com/Leandrofdx/funcional-tasks'
                    sh 'mvn test'
                }
            }
        }
        stage('Deploy PRD') {
            steps {                
                sh 'docker-compose build'
                sh 'docker-compose up -d'
            }
        }
    }
    post {
        always {                
            junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml, target/surefire-reports/*.xml, funci-teste/target/surefire-reports/*.xml'
        }
    }
}