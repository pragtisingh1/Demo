#!/usr/bin/env groovy

/* Initialize Piper Library */
@Library(['piper-lib','piper-lib-os']) _

/* FlowInterruptedException is used for catching multiple error/exception occurred in jenkins workflows */
import org.jenkinsci.plugins.workflow.steps.FlowInterruptedException

try 
{	
    stage('Central Build') 
	{
		/* The lock step limits the number of builds running concurrently in a section of your Pipeline */
		lock(resource: "${env.JOB_NAME}/10", inversePrecedence: true)  
		{
			/* The milestone step ensures that older builds of a job will not overwrite a newer build */
			milestone 10
				
			/* Node declaration allocates an executor on Jenkins Machine */ 
			node 
			{				
				/* Clean Jenkins Workspace */
				deleteDir()
			
				/* Checkout Code from GitHub */
				checkout scm
					
				/* Reads .pipeline/config.yml & Initializes the environment for the pipeline run & storing GitHub Statistics*/
				setupPipelineEnvironment script: this, storeGithubStatistics: true
					
				/* This step is used to measure the duration of a set of steps */
				durationMeasure (script: this, measurementName: 'build_duration') 
				{
					/* Prepares and potentially updates the artifact's version before building the artifact */
					artifactPrepareVersion script: this, buildTool: 'npm', versioningType: 'library'
						
					/* Executes stashing of files after build execution. Relevant for files which are only created in the build run */
					pipelineStashFiles(script: this)
					{
						/* In this step a product standard compliant build will be executed according to SLC-29 */
						executeBuild script: this, buildType: 'stage'
					}
				}
			}
		}
	}
}
catch (Throwable err) 
{ 
    /* Catching multiple error/exception occurred in jenkins workflows */
    globalPipelineEnvironment.addError(this, err)
    throw err
} 
finally 
{
    /* Node declaration allocates an executor on Jenkins Machine */
    node
    {
        /* Sending build metrics to InfluxDB servers */
        influxWriteData script: this

        /* Sends notifications to all potential culprits of a current or previous build failure and to fixed list of recipients */
        //mailSendNotification script: this
    }
}
