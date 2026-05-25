pipeline {
    agent any

    stages {

        // שלב 1: משיכת קוד מ-GitHub
        stage('Checkout Code') {
            steps {
                echo 'Pulling latest code from GitHub...'
                checkout scm
            }
        }

        // שלב 2: בניית כל ה-images דרך Compose (פעם אחת בלבד)
        stage('Build') {
            steps {
                echo 'Building Docker images...'
                bat 'docker compose build --no-cache'
            }
        }

        // שלב 3: הורדת המערכת הישנה (בלי -v כדי לשמור את ה-DB!)
        stage('Tear Down') {
            steps {
                echo 'Stopping old containers...'
                bat 'docker compose down'
            }
        }

        // שלב 4: הרמת המערכת החדשה
        stage('Deploy') {
            steps {
                echo 'Starting new containers...'
                bat 'docker compose up -d'
            }
        }

        // שלב 5: בדיקה שהכל עלה בהצלחה
        stage('Test') {
            steps {
                echo 'Waiting for services to start...'
                sleep(time: 15, unit: 'SECONDS')   // CRA לוקח יותר זמן לעלות

                echo 'Testing Backend...'
                bat 'curl -f http://localhost:5000/health || exit 1'

                echo 'Testing Frontend...'
                bat 'curl -f http://localhost:3000 || exit 1'

                echo 'All services are up!'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
            bat 'docker compose ps'
        }
        success {
            echo 'Deploy הצליח!'
        }
        failure {
            echo 'משהו נכשל — בודק לוגים...'
            bat 'docker compose logs --tail=30'
        }
    }
}