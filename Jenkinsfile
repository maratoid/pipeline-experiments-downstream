#!/usr/bin/env groovy
@Library('pipeline')
import net.zonarsystems.pipeline.ApplicationPipeline

node {
  applicationPipeline = new ApplicationPipeline(
    'loadbalancer',
    steps
  )
  applicationPipeline.pipelineRun()
}