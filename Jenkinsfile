pipeline {

    agent {
        // label "" also could have been 'agent any' - that has the same meaning.
        label "master"
    }

    environment {
        // Global Vars
        NAMESPACE_PREFIX="labs"
        GIT_DOMAIN = "gogs-labs-ci-cd.apps.via-varejo.latam-1.rht-labs.com"
        GIT_ORG = "simulador"

        PIPELINES_NAMESPACE = "${NAMESPACE_PREFIX}-ci-cd"

        JENKINS_TAG = "${JOB_NAME}.${BUILD_NUMBER}".replace("/", "-")
        JOB_NAME = "${JOB_NAME}".replace("/", "-")

        GIT_SSL_NO_VERIFY = true
        GIT_CREDENTIALS = credentials("${GIT_ORG}-ci-cd-gitlab-auth")

        DISPLAY = 0
    }

    // The options directive is for configuration that applies to the whole job.
    options {
        buildDiscarder(logRotator(numToKeepStr:'10'))
        timeout(time: 15, unit: 'MINUTES')
        ansiColor('xterm')
        timestamps()
    }

    stages {
        stage("Ansible") {
            agent {
                node {
                    label "jenkins-slave-ansible"
                }
            }
            stages{
                stage("Ansible Galaxy") {
                    steps {
                        echo '### Ansible Galaxy Installing Requirements ###'
                        sh "ansible-galaxy install -r requirements.yml --roles-path=roles"
                    }
                }
                stage("Apply Inventory using Ansible-Playbook") {
                    steps {
                        echo '### Apply Inventory using Ansible-Playbook ###'
                        sh "ansible-playbook site.yml -i inventory/ -e 'filter_tags=${params.tags}'"
                    }
                }
            }
        }
    }
}
