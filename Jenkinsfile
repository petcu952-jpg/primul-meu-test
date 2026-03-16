pipeline {
    agent any // Poți specifica un agent de Docker aici dacă vrei izolare completă

    environment {
        // Definim o variabilă pentru mediul virtual
        VENV = "venv"
    }

    stages {
        stage('Checkout') {
            steps {
                // Trage automat codul din repository-ul configurat în Jenkins
                checkout scm
            }
        }

        stage('Setup Environment') {
            steps {
                echo 'Pregătim mediul de lucru Python...'
                sh '''
                    python3 -m venv ${VENV}
                    . ${VENV}/bin/activate
                    pip install --upgrade pip
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Instalăm pachetele din requirements.txt...'
                sh '''
                    . ${VENV}/bin/activate
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Linting') {
            steps {
                echo 'Verificăm calitatea codului (cu flake8, opțional)...'
                sh '''
                    . ${VENV}/bin/activate
                    pip install flake8
                    flake8 . --max-line-length=100
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Rulăm testele cu pytest...'
                sh '''
                    . ${VENV}/bin/activate
                    pytest --junitxml=reports/test-results.xml
                '''
            }
        }
    }

    post {
        always {
            echo 'Acest bloc se rulează mereu la final (ex: curățare).'
            // Poți salva rapoartele de testare ca artefacte în Jenkins
            junit 'reports/*.xml'
        }
        success {
            echo 'Super! Pipeline-ul a rulat cu succes! 🎉'
        }
        failure {
            echo 'Ceva nu a mers bine. Trimitem o alertă! 🚨'
        }
    }
}
