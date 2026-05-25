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
                // --no-cache: מבטיח שהקוד החדש נכנס לimage, לא גרסה ישנה מה-cache
            }
        }

        // שלב 3: הורדת המערכת הישנה (בלי -v כדי לשמור את ה-DB!)
        stage('Tear Down') {
            steps {
                echo 'Stopping old containers...'
                bat 'docker compose down'
                // ⚠️ אסור להוסיף -v כאן — זה ימחק את נתוני ה-DB
            }
        }

        // שלב 4: הרמת המערכת החדשה
        stage('Deploy') {
            steps {
                echo 'Starting new containers...'
                bat 'docker compose up -d'
                // -d = detached, רץ ברקע
                // לא צריך --build כי כבר בנינו בשלב 2
            }
        }

        // שלב 5: בדיקה שהכל עלה בהצלחה
        stage('Test') {
            steps {
                echo 'Waiting for services to start...'
                sleep(time: 8, unit: 'SECONDS')   // נותן זמן לקונטיינרים לעלות

                echo 'Testing Backend...'
                bat 'curl -f http://localhost:5000/health || exit 1'
                // -f = fail אם מקבלים שגיאה
                // || exit 1 = גורם ל-Jenkins לסמן FAILED אם ה-curl נכשל

                echo 'Testing Frontend...'
                bat 'curl -f http://localhost:5173 || exit 1'

                echo 'All services are up!'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
            // מציג לוגים תמיד — שימושי לדיבוג
            bat 'docker compose ps'
        }
        success {
            echo '✅ Deploy הצליח — המערכת רצה!'
        }
        failure {
            echo '❌ משהו נכשל — בודק לוגים...'
            bat 'docker compose logs --tail=30'
        }
    }
}