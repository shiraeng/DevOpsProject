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

        // שלב 2: בניית הבקנד (Flask) כדי לוודא שאין שגיאות
        stage('Build Backend') {
            steps {
                echo 'Building Backend Docker Image...'
                dir('backend') { 
                    sh 'docker build -t backend-test:latest .'
                }
            }
        }

        // שלב 3: בניית הפרונטנד (Vite/React) כדי לוודא שההתקנה והבנייה עוברות חלק
        stage('Build Frontend') {
            steps {
                echo 'Building Frontend Docker Image...'
                dir('frontend') { 
                    sh 'docker build -t frontend-test:latest .'
                }
            }
        }

        // שלב 4: הרמת כל הפרויקט יחד בעזרת Docker Compose
        stage('Deploy Application') {
            steps {
                echo 'Deploying application with Docker Compose...'
                sh 'docker compose up -d --build'
            }
        }
    }

    // סיכום הריצה - הדפסת הודעות בהתאם לתוצאה
    post {
        always {
            echo 'Pipeline execution finished.'
        }
        success {
            echo '🎉 The build and deployment succeeded perfectly!'
        }
        failure {
            echo '❌ The build failed. Please check the steps above to see what broke.'
        }
    }
}