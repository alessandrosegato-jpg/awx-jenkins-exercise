pipeline {
    agent { label 'docker-agent-1' }

    triggers {
        GenericTrigger(
            genericVariables: [[key: 'AWX_JOB_ID', value: '$.id']],
            token: 'awx-java-build'
        )
    }

    stages {
        stage('Preleva il file da AWX') {
            steps {
                withCredentials([string(credentialsId: 'awx-token', variable: 'TOKEN')]) {
                    sh '''
                        curl -s -H "Authorization: Bearer $TOKEN" http://10.0.2.2:8043/api/v2/jobs/$AWX_JOB_ID/ -o job.json
                        grep -o '"source_b64":"[^"]*"' job.json | cut -d'"' -f4 | base64 -d > Example.java
                        cat Example.java
                    '''
                }
            }
        }

        stage('Crea artefatto') {
            steps {
                archiveArtifacts 'Example.java'
            }
        }

        stage('Build') {
            steps {
                sh 'javac Example.java'
            }
        }
    }
}