#!groovy

//新的jenkinsfile代码
try {
      stage('SCMCheckout'){
      node {
            //get the code from a github repository.
            git 'https://github.com/fuchu/parameterizedTestsWithRules.git'
            stash includes: '**', name: 'SourceCode'
      }
      }
      stage('Build'){
      node {
            unstash 'SourceCode'
            def mvnHome
            mvnHome=tool 'M3'
            if (isUnix()) {
            sh "'${mvnHome}/bin/mvn' clean cobertura:cobertura -Dcobertura.report.format=xml -Dmaven.test.failure.ignore package"
                  stash includes:'target/', name: 'Target'
            } else {
            bat(/"${mvnHome}\bin\mvn" clean cobertura:cobertura -Dcobertura.report.format=xml -Dmaven.test.failure.ignore package/)
                  stash includes:'target/', name: 'Target'
            }   
      }
      }
      stage('test'){
      node('slave01') {
            unstash 'Target'
            parallel 'CoberturaPublisher':{
            step([$class: 'CoberturaPublisher', autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'target/site/cobertura/*.xml', failNoReports: false, failUnhealthy: false, failUnstable: false, maxNumberOfBuilds: 0, onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false])     
            },'testResults':{
            junit keepLongStdio: true, testResults: 'target/surefire-reports/*.xml'
            }
      }
      }
      stage('Deploy'){
      node {
            archive 'target/*.jar'
            //unstash 'SourceCode'
      }
      }
      notifySuccessful()
}
catch(Exception e){
    currentBuild.result = "FAILED"
        //触发失败邮件
    notifyFailed()
    throw e
}

def notifyFailed() {
    emailext (
        subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
        body: '${JELLY_SCRIPT,template="html"}',
        mimeType: 'text/html', 
        to: 'kevin.zhou@softtek.com'
    )
}
def notifySuccessful() {
    emailext (      
        subject: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
        body: '${JELLY_SCRIPT,template="html"}',
        mimeType: 'text/html', 
        to: 'kevin.zhou@softtek.com'
    )
}
