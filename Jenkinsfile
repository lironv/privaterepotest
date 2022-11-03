pipeline{

	agent any

	environment {
		DOCKERHUB_CREDENTIALS=credentials('1')
		TESTSERVER_CREDENTIALS=credentials('ssh-key-test')
	}

	stages {
         stage('Pull') {
            steps {
                git branch: 'main',
                 credentialsId: '831181b1-4a6f-47bf-8c0c-076d1732d795',
                 url: 'git@github.com:lironv/privaterepotest.git'
            }
        }
        
		stage('Build') {

			steps {
			   dir("app") {
                sh "pwd"
                sh 'docker build -t lironv/attendance:latest .'
			   }
			}
		}

		stage('Login') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Push') {

			steps {
				sh 'docker push lironv/attendance:latest'
			}
		}
		stage('Send scp files') {
			steps {
				shagent(credentials: ['ssh-key-test']) {
				 sh '''
      				[ -d ~/.ssh ] || mkdir ~/.ssh && chmod 0700 ~/.ssh
          			ssh-keyscan -t rsa,dsa testserver >> ~/.ssh/known_hosts
				scp -r "${WORKSPACE}/db" ec2-user@tes
				scp "${WORKSPACE}/docker-compose.yml" ec2-user@testserver:
				'''
				}
			}
		}
	}

	post {
		always {
			sh 'docker logout'
		}
	}

}
