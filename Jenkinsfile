template = '''
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: packer
  name: packer
spec:
  containers:
  - command:
    - sleep
    - "3600"
    image: hashicorp/packer
    name: packer
    '''

def buildNumber = env.BUILD_NUMBER

podTemplate(cloud: 'kubernetes', label: 'packer', yaml: template) {
    node("packer") {
        container("packer") {
            withCredentials([usernamePassword(credentialsId: 'aws-creds', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
            withEnv(['AWS_REGION=us-east-2']) {

            stage("Checkout SCM") {
                git branch: 'main', url: 'https://github.com/zazuwka/packer-jenkins-april.git'
            }
            
            stage("Packer build") {
                sh "packer build -var jenkins_build_number=${buildNumber} packer.pkr.hcl"
            }
        }
    }
}
    }
}
