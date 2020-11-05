
pipeline {
    agent any 
	environment {

		MOBSF_FILE="file=@/home/jm/devops/pivaa/app/build/outputs/apk/debug/app-debug.apk"
		WORK_DIR="/home/jm/devops/pivaa/app/build/outputs/apk/debug/"
		WORK_FILE="app-debug.apk"
		MOBSF_APIKEY="3f1e7dc5ce2fd7b2dc44931eebbe357151ed5081f39c279f13419bc80b352ee9"
	}
    stages {

 		stage('SAST') {
            steps {
	            echo "horus"
				//sh 'curl -fsSL https://horusec-cli.s3.amazonaws.com/install.sh | bash'
                //sh 'horusec start -p="./"'

            }
        }

		stage ('Build') {
            steps { 
			    sh """ 
					gradle clean assembleDebug --no-daemon -x lintVitalRelease
	 			   """
            }
        }

		stage ('MobSF') {
			steps {
				sh '''curl -F \''$MOBSF_FILE\'' http://localhost:8000/api/v1/upload -H "X-Mobsf-Api-Key:$MOBSF_APIKEY" | awk -F\'[/"]\' \'{print $8}\' > hash.txt'''
				sh 'curl -X POST --url http://localhost:8000/api/v1/scan --data "scan_type=apk&file_name=app-debug.apk&hash=$(cat hash.txt)" -H "X-Mobsf-Api-Key:$MOBSF_APIKEY"'
				sh 'curl -X POST --url http://localhost:8000/api/v1/download_pdf --data "hash=$(cat hash.txt)" -H "X-Mobsf-Api-Key:$MOBSF_APIKEY" -o MobSF${BUILD_ID}.pdf'
			}
		}
	}

}