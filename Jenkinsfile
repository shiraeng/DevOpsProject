pipeline {
    agent any 

    stages {
        // שלב 1: משיכת הקוד מ-GitHub
        stage('Checkout Code') {
            steps {
                echo 'Pulling latest code from GitHub...'
                checkout scm
            }
        }

        // שלב 2: בניית הבקנד (Flask) 
        stage('Build Backend') {
            steps {
                echo 'Building Backend Docker Image...'
                dir('backend') { 
                    bat 'docker build -t backend-test:latest .'
                }
            }
        }

        // שלב 3: בניית הפרונטנד (Vite/React)
        stage('Build Frontend') {
            steps {
                echo 'Building Frontend Docker Image...'
                dir('frontend') { 
                    bat 'docker build -t frontend-test:latest .'
                }
            }
        }

        // שלב 4: הרמת כל הפרויקט יחד בעזרת Docker Compose
        stage('Deploy Application') {
            steps {
                echo 'Cleaning up old containers and deploying...'
                
                // עצירת קונטיינרים ישנים ומניעת שגיאות קונפליקט בשמות
                bat 'docker compose down'
                
                // הרמה מחדש של האפליקציה ברקע
                bat 'docker compose up -d --build'
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution finished.'
        }
        success {
            echo 'The build and deployment succeeded perfectly!'
        }
        failure {
            echo 'The build failed. Please check the steps above to see what broke.'
        }
    }
}