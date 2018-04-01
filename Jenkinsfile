#!groovy
node {
    stage('code checkout') {
        wrap([$class: 'AnsiColorBuildWrapper']) {
           print "\u001B[32m Cheking code source code, please wait..."
         checkout poll: false, scm: [$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'Git_Creds', url: 'https://github.com/navdeepmanchanda/java_sample_project.git']]]  
        }   
    }
    stage('Compile') {
        echo "INFO => compiling maven project, please wait..."
        sh 'mvn clean install -Dmaven.test.skip=true '
    }
    stage('Unit Tests') {
        echo "INFO => running java unit tests, please wait..."
        sh "mvn test"
    }
    stage('Sonar Analysis') { 
        withSonarQubeEnv {
           sh "mvn -e -B sonar:sonar -Dsonar.projectKey='guggu' -Dsonar.projectName='XYZ'"
        }
    }
    stage("Quality Gate") { 
        sleep 120
        timeout(time: 2, unit: 'MINUTES') {
           def qg = waitForQualityGate() 
           if (qg.status != 'OK') {
             error "Pipeline aborted due to quality gate failure: ${qg.status}"
           }
        }
    }
    stage("nexus upload") {
      echo "INFO => uploading artifacts to nexus, please wait..."
      sh "mvn deploy"
    }
}
