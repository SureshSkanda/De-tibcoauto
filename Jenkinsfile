// vim: ft=groovy

node {
    stage 'Checkout'
    checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'd7a65326-ec53-4ddf-a96e-72593b73db82', url: 'https://github.com/SureshSkanda/De-tibcoauto.git']]])


    stage 'Build EAR'
    def application = 'tibconow.auto.application'
    def remote = 'tibco@bw.vm'
    def domain = 'domain1'
    def appspace = 'auto'
    def profile = 'default'

	def version = getVersion(application)
    def ear = "${application}_${version}.ear"
    def appver = version - ~/\.\d+$/
    def mvnHome = tool 'M3'

	echo "Version ${version}"

    sh "${mvnHome}/bin/mvn -f ${application}.parent/pom.xml clean package"

    stage "Upload artifacts"
    sh "ssh $remote \"mkdir -p $application\""

    sh "scp ${application}/target/${application}_${version}.ear $remote:~/$application"
    sh "scp deploy_bw_app.sh $remote:$application"

    stage "Deploy"
    sh "ssh $remote \"sudo bash $application/deploy_bw_app.sh $domain $appspace $application $ear $appver $profile\""
}

def getVersion(String application) {
    def matcher = readFile("${application}/META-INF/MANIFEST.MF") =~ /Bundle-Version: (\d+\.\d+\.\d+)/
    matcher ? matcher[0][1] : null
}
