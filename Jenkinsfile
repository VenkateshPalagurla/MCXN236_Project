pipeline {
    agent any

    environment {
        MCUXPRESSO = 'C:\\NXP\\MCUXpressoIDE_25.6.136'
        PROJECT_NAME = 'frdmmcxn236_gpio_led_output_frdmmcxn236'
        PROJECT_PATH = 'MCXN236_Project\\frdmmcxn236\\gpio_led_output_frdmmcxn236'
        BUILD_CONFIG = 'Debug'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Create Workspace') {
            steps {
                bat '''
                if exist workspace rmdir /S /Q workspace
                mkdir workspace
                '''
            }
        }

        stage('Import Project') {
            steps {
                bat '''
                "%MCUXPRESSO%\\ide\\mcuxpressoidec.exe" ^
                -nosplash ^
                -application org.eclipse.cdt.managedbuilder.core.headlessbuild ^
                -data "%WORKSPACE%\\workspace" ^
                -import "%WORKSPACE%\\%PROJECT_PATH%"
                '''
            }
        }

        stage('Build Project') {
            steps {
                bat '''
                "%MCUXPRESSO%\\ide\\mcuxpressoidec.exe" ^
                -nosplash ^
                -application org.eclipse.cdt.managedbuilder.core.headlessbuild ^
                -data "%WORKSPACE%\\workspace" ^
                -build "%PROJECT_NAME%/%BUILD_CONFIG%"
                '''
            }
        }

        stage('Archive Firmware') {
            steps {
                archiveArtifacts artifacts: 'MCXN236_Project/frdmmcxn236/gpio_led_output_frdmmcxn236/Debug/**/*.*',
                                 fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Build completed successfully.'
        }

        failure {
            echo 'Build failed.'
        }
    }
}