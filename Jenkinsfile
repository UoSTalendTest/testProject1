#!groovy

def mailto = "h.babraa@sheffield.ac.uk, h.babraa@sheffield.ac.uk"

node {

  try {

    // Mark the code checkout 'stage'....
    stage 'Checkout'

    // Checkout code from repository and update any submodules
    checkout scm

    //use Talend CI builder to generate java sources from xml files and compile
    stage 'Generate and Compile Sources'
    
    stage 'Test Coverage'
    
    stage 'Tests'
    
    stage 'Package and Publish'

    stage 'Deploy'

    stage 'Verify Deploy'

  } catch (e) {

    currentBuild.result = "FAILED"
    step([$class: 'Mailer', notifyEveryUnstableBuild: true, recipients: "$mailto", sendToIndividuals: true])
    throw e

  }

}
