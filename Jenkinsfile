/*#!groovy
node {
   def mvnHome
   stage('Preparation') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/fuchu/parameterizedTestsWithRules.git'
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'M3'
   }
   stage('Build') {
      // Run the maven build
      if (isUnix()) {
         sh "'${mvnHome}/bin/mvn' clean cobertura:cobertura -Dcobertura.report.format=xml -Dmaven.test.failure.ignore package"
      } else {
         bat(/"${mvnHome}\bin\mvn" clean cobertura:cobertura -Dcobertura.report.format=xml -Dmaven.test.failure.ignore package/)
      }
   }
   stage('Results') {
      step([$class: 'CoberturaPublisher', autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'target/site/cobertura/*.xml', failNoReports: false, failUnhealthy: false, failUnstable: false, maxNumberOfBuilds: 0, onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false])
      junit keepLongStdio: true, testResults: 'target/surefire-reports/*.xml'
      archive 'target/*.jar'
   }
}*/


//新的jenkinsfile代码
mvnHome=tool 'M3'
node {
   stage('SCMCheckout'){
      //get the code from a github repository.
      git 'https://github.com/fuchu/parameterizedTestsWithRules.git'
      stash includes: '**', name: 'SourceCode'
   }
}
node {
   stage('Build'){
         if (isUnix()) {
            sh "'${mvnHome}/bin/mvn' clean cobertura:cobertura -Dcobertura.report.format=xml -Dmaven.test.failure.ignore package"
         } else {
            bat(/"${mvnHome}\bin\mvn" clean cobertura:cobertura -Dcobertura.report.format=xml -Dmaven.test.failure.ignore package/)
         }   
   }
}
node {
   stage('test'){
      parallel 'CoberturaPublisher':{
         step([$class: 'CoberturaPublisher', autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'target/site/cobertura/*.xml', failNoReports: false, failUnhealthy: false, failUnstable: false, maxNumberOfBuilds: 0, onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false])     
      },'testResults':{
         junit keepLongStdio: true, testResults: 'target/surefire-reports/*.xml'
      }
   }
}
node {
   stage('Deploy'){
      archive 'target/*.jar'
      unstash 'SourceCode'
   }
}