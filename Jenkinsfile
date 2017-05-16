#!groovy

//新的jenkinsfile代码
stage('SCMCheckout'){
   node {
      //get the code from a github repository.
      git 'https://github.com/fuchu/parameterizedTestsWithRules.git'
      stash includes: '**', name: 'SourceCode'
   }
}
stage('Build'){
   node {
      def mvnHome
      mvnHome=tool 'M3'
      if (isUnix()) {
         sh "'${mvnHome}/bin/mvn' clean cobertura:cobertura -Dcobertura.report.format=xml -Dmaven.test.failure.ignore package"
         dir('target'){
            stash includes:'**', name: 'Target'
         }
      } else {
         bat(/"${mvnHome}\bin\mvn" clean cobertura:cobertura -Dcobertura.report.format=xml -Dmaven.test.failure.ignore package/)
         dir('target'){
            stash includes:'**', name: 'Target'
         }
      }   
   }
}
stage('test'){
   node('slave01') {
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
