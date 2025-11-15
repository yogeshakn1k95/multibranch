pipeline {
    agent any

    stages {

        stage("Checkout") {
            steps {
                echo "Running on branch: ${env.BRANCH_NAME}"
                checkout scm
            }
        }

        stage("Unit Tests") {
            steps {
                echo "Running tests on ${env.BRANCH_NAME}"
                sh 'mvn clean test'
            }
        }

        stage("Code Scan") {
            when {
                branch 'dev'
            }
            steps {
                echo "Running static scan on DEV branch"
                sh 'echo Running Trivy or SonarQube scan here...'
            }
        }

        stage("Build") {
            when {
                branch 'main'
            }
            steps {
                echo "Building application for MAIN branch"
                sh 'mvn clean package'
            }
        }

        stage("Deploy") {
            when {
                branch 'main'
            }
            steps {
                echo "Deploying application from MAIN branch"

                dir('target') {
                    sh '''
                        # Stop old app if running
                        if pgrep -f "java -jar java-sample-21-1.0.0.jar" > /dev/null; then
                            pkill -f "java -jar java-sample-21-1.0.0.jar"
                            echo "Old app stopped."
                        else
                            echo "No existing app running."
                        fi

                        # Start new app
                        JENKINS_NODE_COOKIE=dontkillMe nohup java -jar java-sample-21-1.0.0.jar > app.log 2>&1 &
                    '''
                }
            }
        }
    }
}
