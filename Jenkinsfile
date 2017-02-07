#!/usr/bin/env groovy
@Library('pipeline')
import net.zonarsystems.pipeline.PipelineHelpers
import net.zonarsystems.pipeline.PipelineBuilders
import groovy.transform.Field
import hudson.EnvVars
import org.jenkinsci.plugins.workflow.cps.EnvActionImpl
import hudson.model.Cause

def getUpstreamEnv() {
  def upstreamEnv = new EnvVars()
  def upstreamCause = currentBuild.rawBuild.getCause(Cause$UpstreamCause)

  if (upstreamCause) {
    def upstreamJobName = upstreamCause.properties.upstreamProject
    def upstreamBuild = Jenkins.instance
                            .getItemByFullName(upstreamJobName)
                            .getLastBuild()
    upstreamEnv = upstreamBuild.getAction(EnvActionImpl).getEnvironment()
  }

  return upstreamEnv
}

// add triggers
properties([
  pipelineTriggers([
    triggers: [
      [$class: 'jenkins.triggers.ReverseBuildTrigger', 
      upstreamProjects: "pipeline-experiments/master", 
      threshold: hudson.model.Result.SUCCESS]
    ]
  ])
])

podTemplate(label: "jenkins-gke-downstream", containers: [
  containerTemplate(name: 'gke', image: 'gcr.io/sds-readiness/jenkins-gke:latest', ttyEnabled: true, command: 'cat', alwaysPullImage: true),
],
volumes: []) {
  node ("jenkins-gke-downstream") {
    container('gke') { 
      checkout scm
      depVars = unpackDependencyVars {
        upstreamEnv = getUpstreamEnv()
      }
      echo depVars 

      reqVars = unpackReqVars {
        upstreamEnv = getUpstreamEnv()
      }
      
      reqVarKeys = reqVars.keySet()
      for (i = 0; i < reqVarKeys.size(); i++ ){
        echo "${reqVarKeys[i]} : ${reqVars.get(reqVarKeys[i])}"
      }
    }
  }
}
