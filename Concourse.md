## Concourse

Resources are the heart and soul of Concourse. They represent all external inputs to and outputs of jobs in the pipeline.

Resources are listed under the `resources:` - key in the pipeline configuration. Each configured resource consists of the following fields:

- `name:` - Short anme to be used as a reference for the build.
- `type:` - Just a docker image which has 3 stages - an in an  out and a check - This mayb e docker/git or we can make our own types.

## Optional Parameters

- `source:` - (Optional) - The location of the resource.
- `version` - Makes it possible to version/pin resources.

- `check_every` - Time interval on which to check for upadates to resources.


- `tags` - A list of tags to determine which worjers the checks will be performed on. You'll want to specify this if the source is internal to a worker's network,
- `webhook_token` - If specified, web hooks can be sent to trigger an immediate check of the resource, specifying this value as a primitive form of authentication via query params.

After configuring this value, you would then configure your hook sender with the following painfully long path appended to your external URL

	/api/v1/teams/TEAM_NAME/pipelines/PIPELINE_NAME/resources/RESOURCE_NAME/check/webhook?webhook_token=WEBHOOK_TOKEN

Note that the request payload sent to this API endpoint is entirely ignored. You should configure the resource as if you're not using web hooks, as the resource config is still the "source of truth."


	resources:
	- name: booklit
	  type: git
	  source: {uri: "https://github.com/vito/booklit"}
	
	jobs:
	- name: unit
	  plan:
	  - get: booklit
	    trigger: true
	  - task: test
	    file: booklit/ci/test.yml





## FLY

Useful fly commands:

- login 

`fly -t local login -c http://localhost:8080`


- See builds for pipeline-name/job-name 

`fly -t local builds -j KTA/build-jamies_feature`

- Run a task and then enter the finished task's container

`fly -t example execute`

`fly -t example intercept --step build`

Containers are around for a short time after a build finishes in order to allow people to intercept them.

`fly -t example intercept -j some-pipeline/some-job -b some-build -s some-step`

All fly commands:

```
abort-build

builds:

Displays information on all builds contained in specified instance - 
This includes the number of builds, the status of last build, 
times started/finished/taken and the team that the build belongs to.

check-resource, 
check-resource-type, 
checklist, 
clear-task-cache, 
containers, 
curl, 
destroy-pipeline, 
destroy-team, 
execute, 
expose-pipeline, 
format-pipeline, 
get-pipeline, 
help, 
hide-pipeline, 
hijack, 
jobs, 
land-worker, 
login, 
logout, 
order-pipelines, 
pause-job, 
pause-pipeline, 
pipelines, 
prune-worker, 
rename-pipeline, 
rename-team, 
resource-versions, 
resources, 
set-pipeline, 
set-team, 
status, 
sync, 
targets, 
teams, 
trigger-job, 
unpause-job, 
unpause-pipeline, 
userinfo, 
validate-pipeline, 
volumes, 
watch or 
workers```