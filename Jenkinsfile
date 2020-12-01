
pipeline {
    agent any 
	environment {

		MOBSF_FILE="file=@/home/ubuntu/projetos/InsecureBankv2/app/build/outputs/apk/debug/app-debug.apk"
		MOBSF_APIKEY="5237211d003aae0d396cc69c59ec3ece04c4f49c2ae4b31ef29a22c197b2ac36"
	}
    stages {

 		stage('SAST') {
            steps {
	            echo "horus"
   		sh 'curl -fsSL https://horusec.io/bin/install.sh | bash'
                sh 'horusec start -p="./"'

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
