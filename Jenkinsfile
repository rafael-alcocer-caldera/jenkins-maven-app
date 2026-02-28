pipeline {
    agent any
    tools {
        // Usa el nombre que configuraste en "Global Tool Configuration"
        maven 'maven-3.9'
    }
    stages {
        stage('Checkout') {
            steps {
                // Jenkins descarga el código automáticamente desde SCM
                checkout scm
            }
        }
        stage('Build & Test') {
            steps {
                // Comando estándar de Maven para compilar y probar
                sh 'mvn clean install'
            }
        }
    }
}