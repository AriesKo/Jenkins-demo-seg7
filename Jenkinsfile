pipeline {
  agent any

  stages {
    stage('Create project') {
      steps {
        deleteDir() // clean up workspace
        checkout([$class: 'GitSCM', branches: [[name: '*/master']],
          doGenerateSubmoduleConfigurations: false,
          extensions: [[$class: 'SubmoduleOption',
            disableSubmodules: false,
            parentCredentials: false,
            recursiveSubmodules: true,
            reference: '',
            trackingSubmodules: true]],
          submoduleCfg: [],
          userRemoteConfigs: [[
            url: 'https://github.com/AriesKo/Jenkins-demo-seg7']]])
      bat 'cd vivado && vivado -mode batch -source create_vivado_proj.tcl'
      }
    }
    stage('Run simulation') {
      steps {
        bat 'cd vivado && vivado -mode batch -source run_simulation.tcl'
      }
    }
    stage('Run synthesis') {
      steps {
        bat 'cd vivado && vivado -mode batch -source run_synthesis.tcl'
      }
    }
    stage('Run implementation') {
      steps {
        bat 'cd vivado && vivado -mode batch -source run_implementation.tcl'
      }
    }
    stage('Generate bitstream') {
      steps {
        bat 'cd vivado && vivado -mode batch -source generate_bitstream.tcl'
      }
    }
  }
  post {
    archiveArtifacts artifacts: "./seg7.runs/impl_1/top.bit", fingerprint: true
    failure {
      emailext attachLog: true,
      body: '''Project name: $PROJECT_NAME
Build number: $BUILD_NUMBER
Build Status: $BUILD_STATUS
Build URL: $BUILD_URL''',
      recipientProviders: [culprits()],
      subject: 'Project \'$PROJECT_NAME\' is broken'
    }
  }
}
