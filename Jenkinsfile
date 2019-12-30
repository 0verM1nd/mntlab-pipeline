node('master') {
    stage('Preparation') {
        echo "Preparation stage"
        git branch: 'sromanchenko', url: 'https://github.com/0verM1nd/mntlab-pipeline.git'
    }
    
    stage('Building code') {
        echo "==> Building stage begins."
        sh ' /opt/gradle/gradle-5.2.1/bin/gradle build'
    }

    stage('Testing code'){
        echo "Testing stage begins."
        parallel (
                phase1: {echo "Unit Test  stage begins."; sh ' /opt/gradle/gradle-5.2.1/bin/gradle test' },
                phase2: {echo "Jacoco Test stage begins."; sh '/opt/gradle/gradle-4.0.1/bin/gradle  jacocoTestReport' },
                phase3: {echo "Cucumber Test stage begins."; sh '/opt/gradle/gradle-5.2.1/bin/ gradle cucumber' }
        )
    }
    stage('Triggeringg job and fetching artefact after finishing'){
        echo "Trigger stage begins."
        build job: "MNTLAB-sromanchenko-child-1-build-job", parameters: [string(name: 'BRANCH_NAME', value: 'sromanchenko' )]
        step ([$class: 'CopyArtifact',
        projectName: "MNTLAB-sromanchenko-child-1-build-job",
        filter: 'sromanchenko_dsl_script.tar.gz']);
    }
    stage('Packaging and Publishing') {
        echo "Packaging and Publishing stage begins."
        sh 'tar xvf sromanchenko_dsl_script.tar.gz'
        sh 'cp build/libs/mntlab-ci-pipeline.jar gradle-simple.jar'
        sh 'tar -czvf pipeline-sromanchenko-${BUILD_NUMBER}.tar.gz jobs.groovy Jenkinsfile gradle-simple.jar'
        archiveArtifacts 'pipeline-sromanchenko-${BUILD_NUMBER}.tar.gz'
    }
    stage('Asking for manual approval') {
                input message: 'Please, approve to continue.', ok: 'Yes'
                echo ('Finished: Asking for manual approval')
    }
    stage('Deployment') {
        echo "Deployment stage begins."
        sh "java -jar gradle-simple.jar"
    }

    stage('Sending status') {
    echo "SUCCESS!"
   }
    

}
