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

properties([
    parameters([
        choice(
            choices: ['dev', 'qa', 'stage', 'prod'], description: 'Pick environment', name: 'region')
            ])
            ])
if (params.region == "dev") {
    regions = "us-east-1"
}
else if (params.region="qa") {
    regions = "us-east-2"
}
else if (params.region="stage") {
    regions = "us-west-1"
}
else {
    regions = "us-west-2"
}

podTemplate(cloud: 'kubernetes', label: 'packer', yaml: template) {
    node("packer") {
        container("packer") {
            withCredentials([usernamePassword(credentialsId: 'aws-creds', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
            withEnv(["AWS_REGION={$region}"]) {

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
