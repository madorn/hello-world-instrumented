#!groovy
  
def project = 'madorn'
def appName = 'hello-world-instrumented'
def feSvcName = "${appName}"
def namespace = 'monitoring-demo'
def imageTag = "quay.io/${project}/${appName}:v${env.BUILD_NUMBER}"
def prevImageTag = ''
def prevBuildNum = ''
def firstDeploy = false

node {
  // Check if there's a previous deployment, if so, get the image version so we can rollback if needed
  try {
    prevImageTag = sh(
      script: "kubectl get deployment hello-world-canary -n ${namespace} -o jsonpath='{.spec.template.spec.containers[0].image}'",
      returnStdout: true
    ).trim()
    echo "Previous Image: ${prevImageTag}"
    prevBuildNum = prevImageTag.split(':')[1]
    echo "Previous Build Version: ${prevBuildNum}"
  } catch (err) {
    echo "No Previous Deployment"
    firstDeploy = true
  }
	
  checkout scm
  sh("printenv")

  stage('Login to Quay.io') {
            when {
                branch 'canary' 
            }
            steps {
                  sh("docker login -u=\"${env.quay_username}\" -p=\"${env.quay_password}\" quay.io")
            }
        }
}
