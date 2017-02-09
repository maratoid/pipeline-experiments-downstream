#!/usr/bin/env groovy
@Library('pipeline')
import net.zonarsystems.pipeline.ApplicationPipeline

applicationPipeline = new ApplicationPipeline(steps, 'loadbalancer')
applicationPipeline.init()
applicationPipeline.pipelineRun()