pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'Maven3'
    }

    environment {
        PROD_HOST = '192.168.66.133'      // Adresse IP de la VM_Prod
        PROD_USER = 'vmprod'              // Nom d'utilisateur correct sur la VM_Prod
        APP_NAME = 'e-commerce'           // Nom de ton application (WAR)
    }

    stages {

        stage('Checkout') {
            steps {
                echo '🔹 Récupération du code source...'
                git branch: 'master', url: 'https://github.com/contact3fahmizarrougui-prog/e-commerce.git'
            }
        }

        stage('Build & Test') {
            steps {
                echo '🏗️ Compilation et exécution des tests Maven...'
                sh 'mvn clean test'
            }
        }

        stage('SAST - Analyse statique du code') {
            steps {
                echo '🔍 Analyse SAST avec Dependency-Check et SpotBugs...'
                sh 'mvn verify -DskipTests=true'
            }
            post {
                always {
                    echo '📊 Sauvegarde des rapports SAST...'
                    archiveArtifacts artifacts: 'target/dependency-check-report.html, target/spotbugsXml.xml', fingerprint: true
                }
            }
        }

        stage('Package') {
            steps {
                echo '📦 Création du package WAR...'
                sh 'mvn package -DskipTests=true -Dmaven.clean.failOnError=false'
            }
            post {
                success {
                    echo '💾 Sauvegarde du fichier WAR généré...'
                    archiveArtifacts artifacts: 'target/*.war', fingerprint: true
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                echo '🚀 Déploiement sur la VM_Prod...'
                sh '''
                    WAR_FILE=$(ls target/*.war | head -n 1)
                    echo "Transfert du fichier $WAR_FILE vers ${PROD_USER}@${PROD_HOST}..."
                    scp -o StrictHostKeyChecking=no $WAR_FILE ${PROD_USER}@${PROD_HOST}:/tmp/app.war

                    echo "Déploiement sur Tomcat en cours..."
                    ssh -o StrictHostKeyChecking=no ${PROD_USER}@${PROD_HOST} '
                        sudo mv /tmp/app.war /var/lib/tomcat9/webapps/${APP_NAME}.war &&
                        sudo chown tomcat:tomcat /var/lib/tomcat9/webapps/${APP_NAME}.war &&
                        sudo systemctl restart tomcat9
                    '
                '''
            }
        }

        stage('DAST - Analyse dynamique (ZAP)') {
            steps {
                echo '🧪 Scan DAST avec OWASP ZAP...'
                sh '''
                    docker run --rm --network=host -v $PWD:/zap/wrk/:rw -t owasp/zap2docker-stable \
                        zap-baseline.py -t http://${PROD_HOST}:8080/${APP_NAME}/ -r zap-report.html
                '''
            }
            post {
                always {
                    echo '📊 Sauvegarde du rapport ZAP...'
                    archiveArtifacts artifacts: 'zap-report.html', fingerprint: true
                }
            }
        }
    }

    post {
        always {
            echo "✅ Pipeline terminé."
        }
        success {
            echo "🎉 Tout s'est bien passé !"
        }
        failure {
            echo "❌ Le pipeline a échoué — vérifier les logs Jenkins."
        }
    }
}
