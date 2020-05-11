@Library(value='iow-ecs-pipeline@2.0.0', changelog=false) _

pipeline {
    agent {
        dockerfile {
			label 'team:iow'
		}
    }
    parameters {
        choice(choices: ['TEST', 'QA', 'PROD-EXTERNAL'], description: 'Deploy Stage (i.e. tier)', name: 'DEPLOY_STAGE')
    }
    stages {
    	stage('Set Build Description') {
          steps {
            script {
              currentBuild.description = "Deploy to ${env.DEPLOY_STAGE}"
            }
          }
        }
        stage('get and install the zip file for lambda consumption') {
            agent {
                dockerfile true
            }
            steps {
                sh '''
                curl ${SHADED_JAR_ARTIFACT_URL} -Lo aqts-capture-pruner-aws.jar
                ls -al
                npm install
                ls -al
                ./node_modules/serverless/bin/serverless deploy --stage ${DEPLOY_STAGE} --taggingVersion ${SHADED_JAR_VERSION}
                '''
            }
        }
    }
    post {
        always {
            script {
                pipelineUtils.cleanWorkspace()
            }
        }
    }
}