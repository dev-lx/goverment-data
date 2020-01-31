pipeline{
    //agent { label 'slav01' }
     agent any
     parameters {
        choice(choices: [ 'master', 'dev', 'stage' ], description: 'Select deployment branch', name: 'BRANCH')
        // gitParameter branchFilter: 'origin/(.*)', defaultValue: 'master', name: 'BRANCH'
}
     environment{
        dockerImage = ''
        registryurl = 'alwaysavail/gitops'
        dockercred = 'hub' 
        RECEPIENT_ID = 'aliabbas.kothawala@calsoftinc.com'

}
     stages{
         stage("Buildstarted"){
            steps{
                slackSend channel: '#jenkins_build',
                    color: 'good',
                    message: "* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL} is started"
            }
        }
         stage('cleanWorkspace'){
            steps{
                step([$class: 'WsCleanup'])
}
}
         stage('EchoChoice'){
             steps{
                 echo "${params.BRANCH}"
             }
}
         stage('clone'){
             steps{
                checkout ([$class: 'GitSCM', branches: [[name: "*/${params.BRANCH}" ]], userRemoteConfigs: [[url: 'https://github.com/dev-lx/goverment-data.git']]])
}
}
         stage('UnitTest'){
             steps{
                script{
                    withEnv(["PATH=PATH=$PATH:/usr/local/go/bin", "GOPATH=${JENKINS_HOME}/workspace/${JOB_NAME}"]) {
                        sh 'go version'
                        sh 'go test --cover'
}
}
}
}
         stage ('build'){
             steps{
                script{
                    withEnv(["PATH=PATH=$PATH:/usr/local/go/bin", "GOPATH=${JENKINS_HOME}/workspace/${JOB_NAME}"]) {
                        sh 'go version'
                        sh 'go build'
                 } 
                }
             }
         }
        stage('Artifacts_Deploy'){
            steps{
                script{
                     echo "Generating artifacts"0
                    }
                }
            }
        stage('buildStatus'){
            steps{
                slackSend channel: '#jenkins_build',
                    color: 'good',
                    message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}"
            }
        }
}
        
        post {
          failure {
            emailext body: 'Check console output at $BUILD_URL to view the results. \n\n ${CHANGES} \n\n -------------------------------------------------- \n${BUILD_LOG, maxLines=100, escapeHtml=false}', 
                    to: '${RECEPIENT_ID}', 
                    subject: 'Build failed in Jenkins: $PROJECT_NAME - #$BUILD_NUMBER'
        }
         success {
             emailext body: 'Check console output at $BUILD_URL to view the results. \n\n ${CHANGES} \n\n -------------------------------------------------- \n${BUILD_LOG, maxLines=100, escapeHtml=false}',                  to: '${RECEPIENT_ID}', 
                    subject: 'Unstable build in Jenkins: $PROJECT_NAME - #$BUILD_NUMBER'
        }
         changed {
             emailext body: 'Check console output at $BUILD_URL to view the results.', 
                    to: '${RECEPIENT_ID}', 
                    subject: 'Jenkins build is back to normal: $PROJECT_NAME - #$BUILD_NUMBER'
        }
    }
}
