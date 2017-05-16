*#!groovy
//新的jenkinsfile代码
mvnHome=tool 'M3'
stage('SCMCheckout'){
   agent {
      //get the code from a github repository.
      git 'https://github.com/fuchu/parameterizedTestsWithRules.git'
      dir(${WORKSPACE}){
      stash includes: '**', name: 'SourceCode'
      }
   }
}
stage('Build'){
   agent {
      if (isUnix()) {
         sh "'${mvnHome}/bin/mvn' clean cobertura:cobertura -Dcobertura.report.format=xml -Dmaven.test.failure.ignore package"
      } else {
         bat(/"${mvnHome}\bin\mvn" clean cobertura:cobertura -Dcobertura.report.format=xml -Dmaven.test.failure.ignore package/)
      }   
   }
}
stage('test'){
   agent {
      parallel 'CoberturaPublisher':{
         step([$class: 'CoberturaPublisher', autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'target/site/cobertura/*.xml', failNoReports: false, failUnhealthy: false, failUnstable: false, maxNumberOfBuilds: 0, onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false])     
      },'testResults':{
         junit keepLongStdio: true, testResults: 'target/surefire-reports/*.xml'
      }
   }
}
stage('Deploy'){
   agent {
      archive 'target/*.jar'
      unstash 'SourceCode'
   }
}
