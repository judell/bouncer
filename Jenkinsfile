#!groovy

@Library('pipeline-library') _

def img

node {
    stage('build') {
        checkout(scm)
        img = buildApp(name: 'hypothesis/bouncer')
    }

    stage('test') {
        testApp(image: img, runArgs: '-u root') {
            sh 'pip install -q tox'
            sh 'cd /var/lib/bouncer && tox'
        }
    }

    onlyOnMaster {
        stage('release') {
            releaseApp(image: img)
        }
    }
}

onlyOnMaster {
    milestone()
    stage('qa deploy') {
        deployApp(image: img, app: 'bouncer', env: 'qa')
    }

    milestone()
    stage('prod deploy') {
        input(message: "Deploy to prod?")
        milestone()
        deployApp(image: img, app: 'bouncer', env: 'prod')
    }
}
