This [template](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/templates?view=azure-devops#template-expressions) is used as the basis for Terraform builds. 


#### Calling Template From another Location within Azure DevOps

To call the template from anothre location first you need to create a resource:


```
resources:
  repositories:
    - repository: 'Templates' ##Name to refer to this resource within the yaml body
      type: git
      name: DevSecOps/Terraform Modules ##Name of Project\Repo
```

Then you can reference this resource:
```
template: BuildTemplate/terraform-build.yaml@Templates
```




#### Calling Template Locally

To call the template, you reference the template and pass it the solution parameters: 

```
steps:
   - template: steps/templateFile.yml
     parameters:
       param1: value
       param2: value
```
#### Params

The template has a mix of mandatory and optional params

Mandatory:

| param                  | type   | description                                                            |
|------------------------|--------|------------------------------------------------------------------------|
| serviceConnectionName  | string | Name of the preconfigured Service connection used to connect to Azure  |
| statefileBackendKey    | string | The key is the name given to the statefile stored in blob by terraform |
| stateFileRG            | string | The Resource Group the Statefile Storage Account resides in            |
| stateFileStorageAccount| string | The Storage Account the Statefile  resides in                          |
| stateFileContainer     | string | The Storage Account Container the Statefile  resides in                |



Optional:

| param             | type   | default                     | description                                                                                                                                           |
|-------------------|--------|-----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| vmImage           | string | ubuntu-20.04                | The OS of the Agent to run the template                                                                                                               |
| workingDirectory  | string | '/'                         | Default working directory for Terraform tasks                                                                                                         |
| terraformVersion  | string | 0.12.26                     | Version of Terraform to Install                                                                                                                       |
| planOptions       | string | -no-color                   | Terraform command options/switches                                                                                                                    |
| enabledPublish    | bool   | true                        | Enabled/Disable the publish of an artifact. Off for PR builds                                                                                         |
| artifactName      | string | Validated_Terraform         | The name of the artifact if published                                                                                                                 |
| artifactDirectory | string | '/'                         | The directory to publish as an artifact                                                                                                               |
| enableBuildSteps  | bool   | false                       | When a stepList param is declared, the step will always run, this switch disables the task from running automatically and polluting build reports.    |
| downloadModules   | bool   | true                        | If enabled this will download the terraform modules dir into the working directory                                                                    |


**Note:** the template also contains the buildSteps param which enables you to pass a yaml release build step as a parameter like so:

Definition in template:
```
parameters:
   - name: buildSteps # the name of the parameter is buildSteps
     type: stepList # data type is StepList
     default: [] # default value of buildSteps
```

Example Call:
```
extends:
  template: templatefile.yml
  parameters:
    buildSteps:  
      - bash: ls
        displayName: list directory
```

