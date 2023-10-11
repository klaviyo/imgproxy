
import com.klaviyo.jenkins.Git
import com.klaviyo.jenkins.KlaviyoDocker
import com.klaviyo.jenkins.KlaviyoDocker.Env
import com.klaviyo.jenkins.KlaviyoDockerImages
import com.klaviyo.jenkins.KlaviyoDockerImages.Image
import com.klaviyo.jenkins.AppfileUtils

// Utility Libraries
def klaviyoDocker = new KlaviyoDocker(this)
def git = new Git(this)

// Shared Variables
def String imageName = "reviews_imgproxy"
def String ciImage = "${imageName}_ci"

def appfileUtils = new AppfileUtils(this)

// Main Pipeline
pipeline {
    agent any

    options {
        ansiColor('xterm')
    }

    stages {

        stage('Track Basic Facts') {
            steps {
                script {
                    echoColor("Commit Hash: ${git.commitHash()}", 'blue')
                }
            }
        }

        stage('Build Image') {
                steps {
                getVaultSecrets([
                    [envVar: 'ARTIFACTORY_URL', vaultKey: 'klaviyo-artifactory-url'],
                    [envVar: 'ARTIFACTORY_USER', vaultKey: 'klaviyo-temp-artifactory-user'],
                    [envVar: 'ARTIFACTORY_PASS', vaultKey: 'klaviyo-temp-artifactory-password']
                ], {
                    script {
                        echoColor("URL: ${ARTIFACTORY_URL}", 'red')
                            echoColor("URL: ${pyInstallPipConfFromVault()}", 'blue')
                            klaviyoDocker.build(
                                    imageName,
                                    git.commitHash(),
                                    'docker/Dockerfile',
                                    "-t ${imageName}:${git.commitHash()} \
                                    --build-arg=ARTIFACTORY_PASS=${ARTIFACTORY_PASS} \
                                    --build-arg=ARTIFACTORY_USER=${ARTIFACTORY_USER}"
                            )

                    }
                })
            }
        }

        


    }
}
