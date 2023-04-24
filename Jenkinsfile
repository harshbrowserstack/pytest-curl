/* Requires the Docker Pipeline plugin */
def params = []

pipeline {
    agent any
        parameters {
            password(name: 'TEST_MANAGEMENT_API_TOKEN', defaultValue: '****', description: 'API Token of your Test Management Account - You can find here: https://test-management.browserstack.com/settings')
            string(name: 'TEST_MANAGEMENT_PROJECT_NAME', defaultValue: 'XYZ Banking Corporation', description: 'Project Name where you want to upload test results, NOTE: If any new project name is defined, Test Management will create a project for you')
            string(name: 'TEST_RUN_NAME', defaultValue: 'Test Run - PyTest cURL - $BUILD_NUMBER', description: 'Name of your Test Run')
            string(name: 'USER_EMAIL', defaultValue: 'test.management23@gmail.com', description: 'User Email')
        }
    stages {
        stage('Run Pytest') {
            steps {
                catchError {
                    sh '''
                        export PATH=$PATH:/usr/local/bin
                        pip3 install pytest
                        pip3 show pytest
                        cd pytest-curl # cd to your repo
                        python3 -m pytest --junitxml=./test.xml
                    '''
                }
            }
        }
        stage('Upload Reports in Test Management') {
            steps {
                sh '''
                    export JUNIT_XML_FILE_PATH="$(pwd)/pytest-curl/test.xml"

                    curl -k -X POST https://test-management.browserstack.com/api/v1/import/results/xml/junit \
                    -H "API-TOKEN:$TEST_MANAGEMENT_API_TOKEN" \
                    -F project_name="$TEST_MANAGEMENT_PROJECT_NAME" \
                    -F "file_path=@$JUNIT_XML_FILE_PATH" \
                    -F test_run_name="$TEST_RUN_NAME" \
                    -F user_email=$USER_EMAIL
                '''
            }
        }
    }
}
