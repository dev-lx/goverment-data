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
         changed {
            script {
                if (currentBuild.currentResult == 'FAILURE') { // Other values: SUCCESS, UNSTABLE
                    // Send an email only if the build status has changed from green/unstable to red
                    emailext subject: '$DEFAULT_SUBJECT',
                        body: '$DEFAULT_CONTENT',
                        recipientProviders: [
                            [$class: 'CulpritsRecipientProvider'],
                            [$class: 'DevelopersRecipientProvider'],
                            [$class: 'RequesterRecipientProvider']
                        ]
                        to: '$DEFAULT_RECIPIENTS'
                }
            }
        }
    }
}
