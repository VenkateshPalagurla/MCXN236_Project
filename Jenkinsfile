pipeline {
    agent any

    environment {
        MCUXPRESSO = 'C:\\NXP\\MCUXpressoIDE_25.6.136'
        PROJECT_NAME = 'frdmmcxn236_gpio_led_output_frdmmcxn236'
        BUILD_CONFIG = 'Debug'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Repository') {
            steps {
                bat '''
                echo ============================================
                echo Jenkins Workspace
                echo ============================================
                cd

                echo.
                echo ===== Root Directory =====
                dir

                echo.
                echo ===== Complete Repository Tree =====
                tree /F
                '''
            }
        }

        stage('Find Project') {
            steps {
                script {
                    if (fileExists('frdmmcxn236/gpio_led_output_frdmmcxn236/.project')) {

                        env.PROJECT_PATH = 'frdmmcxn236\\gpio_led_output_frdmmcxn236'

                    } else if (fileExists('MCXN236_Project/frdmmcxn236/gpio_led_output_frdmmcxn236/.project')) {

                        env.PROJECT_PATH = 'MCXN236_Project\\frdmmcxn236\\gpio_led_output_frdmmcxn236'

                    } else {

                        error "MCUXpresso project not found!"

                    }

                    echo "Project Path = ${env.PROJECT_PATH}"
                }
            }
        }

        stage('Create Eclipse Workspace') {
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
                echo ============================================
                echo Importing MCUXpresso Project
                echo ============================================

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
                echo ============================================
                echo Building Project
                echo ============================================

                "%MCUXPRESSO%\\ide\\mcuxpressoidec.exe" ^
                  -nosplash ^
                  -application org.eclipse.cdt.managedbuilder.core.headlessbuild ^
                  -data "%WORKSPACE%\\workspace" ^
                  -build "%PROJECT_NAME%/%BUILD_CONFIG%"
                '''
            }
        }

        stage('Show Build Output') {
            steps {
                bat '''
                echo ============================================
                echo Build Output
                echo ============================================

                dir "%WORKSPACE%\\%PROJECT_PATH%\\Debug"

                echo.
                echo ===== Firmware Files =====
                dir /s *.axf
                dir /s *.elf
                dir /s *.hex
                dir /s *.bin
                '''
            }
        }

        stage('Archive Firmware') {
            steps {
                archiveArtifacts artifacts: '**/Debug/**/*.*', fingerprint: true
            }
        }

    }

    post {

        success {
            echo '========================================'
            echo 'BUILD SUCCESSFUL'
            echo '========================================'
        }

        failure {
            echo '========================================'
            echo 'BUILD FAILED'
            echo '========================================'
        }

        always {
            echo 'Pipeline Finished'
        }
    }
}