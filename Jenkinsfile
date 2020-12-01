
pipeline {
    agent any 
	environment {

		MOBSF_FILE="file=@/home/ubuntu/projetos/InsecureBankv2/app/build/outputs/apk/debug/app-debug.apk"
		MOBSF_APIKEY="6c0d5755f7e85a2c92ddddaf7dcee0d4dd73e7260d99869fb06feea3d9a8afc3"
	}
    stages {

 		stage('SAST') {
            steps {
	            echo "horus"
   		sh 'curl -fsSL https://horusec.io/bin/install.sh | bash'
                sh 'horusec start -p="./" -e="true"'

            }
        }

		stage ('Build') {
            steps { 
			    sh """ 
					 cd /home/ubuntu/projetos/InsecureBankv2 && gradle clean assembleDebug --no-daemon -x lintVitalRelease

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
