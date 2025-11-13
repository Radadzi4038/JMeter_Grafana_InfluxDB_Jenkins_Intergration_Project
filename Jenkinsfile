pipeline {
    agent any

    environment {
        JMETER_HOME = "C:\\JMeter\\apache-jmeter-5.6.3"
        TEST_PLAN = "Assignment2_Performance_Test.jmx"
        RESULTS = "results.jtl"
        LOGFILE = "jmeter.log"
    }

    stages {
        stage('Checkout') {
            steps {
                // This clones the repo (if from GitHub)
                // or loads workspace if local
                checkout scm
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat """
                "%JMETER_HOME%\\bin\\jmeter.bat" -n -t "%WORKSPACE%\\%TEST_PLAN%" -l "%WORKSPACE%\\%RESULTS%" -j "%WORKSPACE%\\%LOGFILE%"
                """
            }
        }

        stage('Archive Results') {
            steps {
                archiveArtifacts artifacts: '*.jtl, *.log', fingerprint: true
            }
        }

        stage('Publish HTML Report') {
            steps {
                bat """
                "%JMETER_HOME%\\bin\\jmeter.bat" -g "%WORKSPACE%\\%RESULTS%" -o "%WORKSPACE%\\Report"
                """
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'Report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter HTML Report'
                ])
            }
        }
    }

    post {
        always {
            echo "JMeter test completed. Check Grafana for live metrics."
        }
    }
}
