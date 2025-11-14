pipeline {
    agent any

    environment {
        JMETER_HOME = "C:\\JMeter\\apache-jmeter-5.6.3"
        TEST_PLAN = "Assignment2_Performance_Test.jmx"
        RESULTS = "results.jtl"
        LOGFILE = "jmeter.log"
        REPORT_DIR = "Report"
        DATAFILE = "C:\\Users\\dell 7490\\Documents\\SCHOOL WORK\\SOFTWARE TESTING PROGRAM\\2ND SEMESTER COURSES\\NON FUNCTIONAL TESTING\\PerformanceTestingProject\\demo-products.csv"
    }

    stages {
        stage('Checkout') {
            steps {
                // Pulls code from your repo (if linked)
                checkout scm
            }
        }

        stage('Run JMeter Test') {
            steps {
                // Clean up previous run files and execute JMeter in non-GUI mode
                bat """
                echo Cleaning old results...

                if exist "%WORKSPACE%\\%RESULTS%" del "%WORKSPACE%\\%RESULTS%"
                if exist "%WORKSPACE%\\%LOGFILE%" del "%WORKSPACE%\\%LOGFILE%"
                if exist "%WORKSPACE%\\%REPORT_DIR%" rmdir /s /q "%WORKSPACE%\\%REPORT_DIR%"

                echo Starting JMeter performance test...

                "%JMETER_HOME%\\bin\\jmeter.bat" ^
                    -n ^
                    -t "%WORKSPACE%\\%TEST_PLAN%" ^
                    -l "%WORKSPACE%\\%RESULTS%" ^
                    -j "%WORKSPACE%\\%LOGFILE%" ^
                    -Jdatafile="%DATAFILE%"
                """
            }
        }

        stage('Generate HTML Report') {
            steps {
                bat """
                echo Generating JMeter HTML report...

                "%JMETER_HOME%\\bin\\jmeter.bat" ^
                    -g "%WORKSPACE%\\%RESULTS%" ^
                    -o "%WORKSPACE%\\%REPORT_DIR%"
                """
            }
        }

        stage('Archive & Publish Reports') {
            steps {
                // Save artifacts and make HTML report visible in Jenkins UI
                archiveArtifacts artifacts: '*.jtl, *.log', fingerprint: true

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
            echo " JMeter test completed successfully."
        }
    }
}
