---
title: Harness Expressions - CEL
description: Learn how to use Harness Expressions in the new format, CEL.
sidebar_position: 30
---

[Harness Expressions] are getting an update. The old syntax involved using `<+ >` to signify a Harness expression, but now the platform is switching to standardize on the Common Expression Language or CEL. To learn more about CEL, go to the [CEL spec](https://github.com/google/cel-spec).

Both types of Harness Expressions will be supported, but since CEL is forming into the industry standard, CEL will be the recommended format going forward.  

For a partial list of expressions and their parity from Harness NextGen to Harness Unified Pipeline, go to the [parity matrix](#parity-matrix) at the bottom of the page.

## Harness CEL Expression Support

CEL offers many benefits that you can utilize in your pipelines as well as carrying over many of the benefits of NG Expressions. Read this topic to learn how to use CEL expressions.

### Basic Expression Syntax

Every expression must be enclosed in `${{ }}`. This will let Harness know to use what's inside as an expression and not a generic string. 

#### Example: Set the pipeline name as an environment variable

If you wanted to store the pipeline name as an environment variable you could do:

```yaml
env:
    PIPELINE_NAME: ${{ pipeline.name }}
```

### Operators

CEL allows you to use operators to compare expressions to each other.

#### Supported Arithmetic Operators

| Operator | Description   |
|----------|---------------|
| `+`        | Addition      |
| `-`        | Subtraction   |
| `*`        | Multipication |
| `/`        | Division      |

#### Supported Comparison and Logical Operators

| Operator | Description              |
|----------|--------------------------|
| `==  `     | Equals                   |
| `!=  `     | Not equals               |
| `<   `     | Less than                |
| `<=  `     | Less than or equal to    |
| `>   `     | Greater than             |
| `>=  `     | Greater than or equal to |
| `&&  `     | And                      |
| `\|\|`     | Or                       |


#### Example: Using Operators

```yaml
env: 
    isMain: ${{ pipeline.branch == 'main' }}
```

The simple expression above evaluates to `true` if the pipeline branch is equal to `main` and false otherwise. 

### Functions

Harness offers the use of built in functions that will help you evaluate and use your expressions. Each function follows the format of ??? [Q]

<!-- `<literal>.<function_name>(<inputs)`. and can return any of the literal types supported by CEL.  -->

#### Supported Functions

[ADD FUNCTION SIGNATURES AND EXAMPLES]

| Function      | Function Signature | Return Type       | Example |
|---------------|--------------------|-------------------|---------|
| `startsWith`  |                    | Boolean           |         |
| `endsWith`    |                    | Boolean           |         |
| `contains`    |                    | Boolean           |         |
| `toUpperCase` |                    | String            |         |
| `toLowerCase` |                    | String            |         |
| `join`        |                    | String            |         |
| `format`      |                    | String            |         |
| `replace`     |                    | String            |         |
| `trim`        |                    | String            |         |
| `split`       |                    | String            |         |
| `min`         |                    | Number            |         |
| `max`         |                    | Number            |         |
| `length`      |                    | Number            |         |
| `toJSON`      |                    | String            |         |
| `fromJSON`    |                    | Array of literals |         |

### Contextual Data Access

Harness CEL expressions will also you to access information form various contexts. Each context can be dereferenced with the `.` operator to access the data inside. 

#### Supported Contexts

| Context    | Description                                                                                 |
|------------|---------------------------------------------------------------------------------------------|
| `pipeline` | Provides information about the pipeline, including variables defined in the pipeline.       |
| `env`      | Provides information about environment variables set in the workflow or runner environment. |
| `stage`    | Provides information about the current job, such as its status and outputs.                 |
| `steps`    | Provides details about individual steps within the current job.                             |

#### Example: Using Contexts

If I wanted to access the name of the current job/stage I could use the expression:

```
${{ stage.name }}
```

## Parity Matrix

| Component         | NG Expression                                                         | Github Actions Expression                  | Unified Pipeline Expression                                             |
| ----------------- | --------------------------------------------------------------------- | ------------------------------------------ | ----------------------------------------------------------------------- |
| Pipeline          | `<+pipeline.identifier>`                                            | `jobs.<job_id>`                              | `$ {{ pipeline.id }} `                         |
|                   | `<+pipeline.name>`                                                  | `github.workflow`                            | `$ {{ pipeline.name }}`                      |
|                   | `<+pipeline.tags>`                                                  | NA                                         | `$ {{ pipeline.tags }}`                                                |
|                   | `<+pipeline.executionId>`                                           | `github.run_number` \| `github.run_id`         | `$ {{ pipeline.execution_id }}`                                       |
|                   | `<+pipeline.resumedExecutionId>`                                    | NA                                         | `$ {{ pipeline.resume_execution_id }}`                                 |
|                   | `<+pipeline.executionMode>`                                         | NA                                         | `$ {{ pipeline.execution_mode }}`                                      |
|                   | `<+pipeline.startTs>`                                               | NA                                         | `$ {{ pipeline.start_time }} `                                         |
|                   | `<+pipeline.selectedStages>`                                        | NA                                         | `$ {{ pipeline.selected_stages }}`                                     |
|                   | `<+pipeline.delegateSelectors>`                                     | NA                                         | `$ {{ pipeline.delegate_selectors }} `                                 |
|                   | `<+pipeline.storeType>`                                             | NA                                         | `$ {{ pipeline.store_type }} `                                         |
|                   | `<+pipeline.repo>`                                                  | `github.workflow_ref`                        | `$ {{ pipeline.repo }}`                                               |
|                   | `<+pipeline.branch>`                                                | `github.base_ref`                            | `$ {{ pipeline.branch }}`                                             |
|                   | `<pipeline.orgIdentifier>`                                          | NA                                         | `$ {{ pipeline.org_id }}`                                              |
|                   | `<+pipeline.stages.STAGE_ID.spec.execution.steps.STEP_ID.status>`   | `github.action_status` \| `job.status`         | `$ {{ pipeline.stages.stage_id.execution.step.status }}`              |
|                   | `<+pipeline.stages.STAGE_ID.status>`                                | `github.action_status` \| `job.status`         | ``                                                                    |
|                   | `<+pipeline.stages.STAGE_ID.currentStatus>`                         | `github.action_status` \| `job.status`         | ``                                                                    |
|                   | `<+pipeline.stages.stage1.liveStatus>`                              | `github.action_status` \| `job.status`         | ``                                                                    |
| Secrets           | `<+secrets.getValue("SECRET_ID")>`                                  | `secrets.<secret_name>`                      | `$ {{ secret.getValue(secret.id) }}`                                  |
| Service           | `<+service.name>`                                                   | NA                                         | `$ {{ service.name }}`                                                 |
|                   | `<+service.description>`                                            | NA                                         | `$ {{ service.description }}`                                          |
|                   | `<+service.tags>`                                                   | NA                                         | `$ {{ service.tags }}`                                                 |
|                   | `<+service.identifier>`                                             | NA                                         | `$ {{ service.id }}`                                                   |
|                   | `<+service.type>`                                                   | NA                                         | `$ {{ service.type }}`                                                 |
|                   | `<+service.gitOpsEnabled>`                                          | NA                                         | `Deprecate - This is for UI to help users configure a GitOps Service.` |
|                   | `<+serviceVariables.VARIABLE_NAME>`                                 | `${{ vars.USE_VARIABLES }}` \| `github.env`    | `$ {{ service.variables.<var_name> }}`                             |
|                   | `<+serviceVariableOverrides.VARIABLE_NAME>`                         | `${{ vars.USE_VARIABLES }}` \| `github.env`    | `$ {{ service.variables_override.<var_name> }}`                    |
|                   | `<artifacts.primary.image>`                                         | NA                                         | `$ {{ artifacts.image }}`                                              |
|                   | `<+artifacts.primary.tag>`                                          | NA                                         | `$ {{ artifacts.tag }}`                                                |
|                   | `<+artifacts.primary.metadata.SHA>`                                 | NA                                         | `$ {{ artifacts.SHA }}`                                                |
|                   | `<+artifacts.primary.metadata.SHAV2>`                               | NA                                         | `$ {{ artifacts.SHAV2 }}`                                              |
|                   | `<+artifact.metadata.fileName>`                                     | NA                                         | `$ {{ artifacts.filename }}`                                           |
|                   | `<+artifact.metadata.url>`                                          | NA                                         | `$ {{ artifacts.repo_url }}`                                           |
|                   | `<+artifacts.primary.imagePullSecret>`                              | NA                                         | `$ {{ artifacts.image_pull_secret }}`                                  |
|                   | `<+artifacts.primary.dockerConfigJsonSecret>`                       | NA                                         | `$ {{ artifacts.docker_config_json_secret }}`                          |
|                   | `<+rollbackArtifact.metadata.image>`                                | NA                                         | `$ {{ rollback.artifact.image }}`                                      |
|                   | `<+configFile.getAsString("CONFIG_FILE_ID")>`                       | NA                                         | `$ {{ config.fromString() }}`                                          |
|                   | `<+artifacts.sidecars.SIDECAR_IDENTIFIER.connectorRef>`             | NA                                         | `$ {{ sidecar.<sidecar_name>.connector_id}}`                           |
|                   | `<+artifacts.sidecars.SIDECAR_IDENTIFIER.tag>`                      | NA                                         | `$ {{ sidecar.<sidecar_name>.tag }}`                                   |
|                   | `<+artifacts.sidecars.SIDECAR_IDENTIFIER.type>`                     | NA                                         | `$ {{ sidecar.<sidecar_name>.type }}`                                  |
|                   | `<+artifacts.sidecars.SIDECAR_IDENTIFIER.image>`                    | NA                                         | `$ {{ sidecar.<sidecar_name>.image }}`                                 |
|                   | `<+artifacts.sidecars.SIDECAR_IDENTIFIER.imagePath>`                | NA                                         | `$ {{ sidecar.<sidecar_name>.image_path }}`                            |
|                   | `<+manifests.MANIFEST_ID.helm.name>`                                | NA                                         | `$ {{ manifest.name }}`                                                |
|                   | `<+manifests.MANIFEST_ID.helm.description>`                         | NA                                         | `$ {{ manifest.description }}`                                         |
|                   | `<+manifests.MANIFEST_ID.helm.version>`                             | NA                                         | `$ {{ manifest.version }}`                                             |
|                   | `<+manifests.MANIFEST_ID.helm.apiVersion>`                          | NA                                         | `$ {{ manifest.api_version }}`                                         |
|                   | `<+manifests.MANIFEST_ID.helm.appVersion>`                          | NA                                         | `$ {{ manifest.app_version }}`                                         |
|                   | `<+manifests.MANIFEST_ID.helm.kubeVersion>`                         | NA                                         | `$ {{ manifest.kube_version }}`                                        |
|                   | `<+manifests.MANIFEST_ID.helm.metadata.url>`                        | NA                                         | `$ {{ manifest.chart_url }}`                                           |
|                   | `<+manifests.MANIFEST_ID.helm.metadata.basePath>`                   | NA                                         | `$ {{ manifest.base_path }}`                                           |
|                   | `<+manifests.MANIFEST_ID.helm.metadata.bucketName>`                 | NA                                         | `$ {{ manifest.bucket_name }}`                                         |
|                   | `<+manifests.MANIFEST_ID.helm.metadata.commitId>`                   | NA                                         | `$ {{ manifest.commit_id }}`                                           |
|                   | `<+manifests.MANIFEST_ID.helm.metadata.branch>`                     | NA                                         | `$ {{ manifest.branch }}`                                              |
| Stage             | `<+stage.name>`                                                     | `jobs.<job_id>.name`                         | `$ {{stage.name}}`                              |
|                   | `<+stage.description>`                                              | `jobs.<job_id>.outputs.<output_id>`          | `$ {{ stage.description }}`                                           |
|                   | `<+stage.tags>`                                                     | NA                                         | `$ {{ stage.tags }}`                                                   |
|                   | `<+stage.identifier>`                                               | NA                                         | `$ {{ stage.id }}`                                 |
|                   | `<+stage.output.hosts>`                                             | `jobs.<job_id>.outputs.<output_id>`          | ` $ {{ stage.output.hosts }}`                                         |
|                   | `<+stage.executionUrl>`                                             | `jobs.<job_id>.outputs.<output_id>`          | `$ {{ stage.execution_url }}`                                         |
|                   | `<+stage.delegateSelectors>`                                        | NA                                         | `$ {{ stage.delegate_selectors }}`                                     |
|                   | `<+pipeline.stages.STAGE_ID.status>`                                | `github.action_status` \| `job.status`         | ``                                                                    |
| Matrix / Looping  | `<+strategy.currentStatus>`                                         | `github.action_status` \| `job.status`         | ``                                                                    |
|                   | `<+strategy.node.STRATEGY_NODE_IDENTIFIER.currentStatus>`           | `github.action_status` \| `job.status`         | ``                                                                    |
|                   | `<+<+strategy.node>.get("STRATEGY_NODE_IDENTIFIER").currentStatus>` | `github.action_status` \| `job.status`         | ``                                                                    |
| Step Group        | `<+stepGroup.variables>`                                            | `${{ vars.USE_VARIABLES }}`                  | `$ {{ group.steps.variables.var_name }}`                              |
|                   | `<+stepGroup.getParentStepGroup>`                                   | NA                                         | `$ {{ group.steps.variables.var_name }}`                               |
| Step              | `<+step.name>`                                                      | `jobs.<job_id>.steps.name`                   | `$ {{step.name }}`                               |
|                   | `<+step.identifier>`                                                | NA                                         | `$ {{ step.id }}`                                  |
|                   | `<+step.executionUrl>`                                              | `jobs.<job_id>.outputs.<output_id>`          | `$ {{ step.execution_url }}`                                          |
|                   | `<+steps.STEP_ID.retryCount>`                                       | `github.run_attempt`                         | ``                                                                    |
| Trigger           | `<+trigger.type>`                                                   | NA                                         | `$ {{ trigger.type }}`                                                 |
|                   | `<+trigger.event>`                                                  | `github.event`                               | `$ {{ trigger.event }}`                                               |
|                   | `<+trigger.artifact.build>`                                         | NA                                         | `$ {{ trigger.artifact.build }}`                                       |
|                   | `<+trigger.artifact.source.connectorRef>`                           | NA                                         | `$ {{ trigger.artifact.source.connector.id }}`                         |
|                   | `<+trigger.artifact.source.imagePath>`                              | NA                                         | `$ {{ trigger.artifact.source.image_path }}`                           |
|                   | `<+pipeline.triggeredBy.name`                                       | `github.triggering_actor` \| `github.actor_id` | `$ {{ pipeline.triggered_by.name }}`                                  |
|                   | `<+pipeline.triggeredBy.email`                                      | `github.triggering_actor` \| `github.actor_id` | `$ {{ pipeline.triggered_by.email }}`                                 |
|                   | `<+pipeline.triggerType>`                                           | NA                                         | ``                                                                     |
| Codebase          | `<+codebase.branch>`                                                | `github.base_ref`                            | `$ {{ codebase.branch }}`                                             |
|                   | `<+codebase.tag>.`                                                  | `github.ref_name`                            | `$ {{ codebase.tag }}`                                                |
|                   | `<+codebase.sourceBranch>`                                          | `github.head_ref`                            | `$ {{ codebase.source_branch }}`                                      |
|                   | `<+codebase.targetBranch>`                                          | `github.base_ref`                            | `$ {{ codebase.target_branch }}`                                      |
|                   | `<+codebase.prNumber>`                                              | `github.event`                               | `$ {{ codebase.pr_number }}`                                          |
|                   | `<+trigger.prNumber>`                                               | `github.event`                               | `$ {{ trigger.pr_number }}`                                           |
|                   | `<+codebase.prTitle>`                                               | `github.event`                               | `$ {{ codebase.pr_title }}`                                           |
|                   | `<+trigger.prTitle>`                                                | `github.event`                               | `$ {{ trigger.pr_title }}`                                            |
|                   | `<+codebase.pullRequestBody>`                                       | `github.event`                               | `$ {{ codebase.pull_request_body }}`                                  |
|                   | `<+codebase.pullRequestLink>`                                       | `github.event`                               | `$ {{ codebase.pull_request_link }}`                                  |
|                   | `<+trigger.sourceRepo>`                                             | `github.event`                               | `$ {{ trigger.source_repo }}`                                         |
|                   | `<+codebase.state>`                                                 | NA                                         | `$ {{ codebase.state }}`                                               |
|                   | `<+codebase.repoUrl>`                                               | `github.action_repository`                   | `$ {{ codebase.repo_url }}`                                           |
|                   | `<+codebase.gitUserId>`                                             | `github.triggering_actor` \| `github.actor_id` | `$ {{ codebase.git_user_id }}`                                        |
|                   | `<+codebase.gitUserEmail>`                                          | `github.triggering_actor` \| `github.actor_id` | `$ {{ codebase.git_user_email }}`                                     |
|                   | `<+codebase.gitUserAvatar>`                                         | `github.triggering_actor` \| `github.actor_id` | `$ {{ codebase.git_user_avatar }}`                                    |
|                   | `<+codebase.gitUser>`                                               | `github.triggering_actor` \| `github.actor_id` | `$ {{ codebase.git_user }}`                                           |
|                   | `<+codebase.shortCommitSha>`                                        | `github.sha` \| `github.workflow_sha`          | `$ {{ codebase.short_commit_sha }}`                                   |
|                   | `<+codebase.mergeSha>`                                              | `github.sha` \| `github.workflow_sha`          | `$ {{ codebase.merge_sha }}`                                          |
|                   | `<+codebase.commitSha>`                                             | `github.sha` \| `github.workflow_sha`          | `$ {{ codebase.commit_sha }}`                                         |
|                   | `<+codebase.commitRef>`                                             | `github.event`                               | `$ {{ codebase.commit_ref }}`                                         |
|                   | `<+codebase.commitMessage>`                                         | `github.event`                              | `$ {{ codebase.commit_message }}`                                     |
|                   | `<+codebase.baseCommitSha>`                                         | `github.sha` \| github.workflow_sha          | `$ {{ codebase.base_commit_sha }}`                                    |
|                   | `<+codebase.tag>`                                                   | NA                                         | `$ {{ codebase.tag }}`                                                 |
| Account Variables | `<+variable.account.VARIABLE_NAME>`                                 | `${{ vars.USE_VARIABLES }}`                  | `$ {{ variable.account.var_name }} `                                  |
|                   | ``                                                                  |                                            | ``                                                                     |
| Org Variables     | `<+variable.org.VARIABLE_NAME>`                                     | `${{ vars.USE_VARIABLES }}`                  | `$ {{ variable.org.var_name }} `                                      |
|                   | ``                                                                  |                                            | ``                                                                     |
| Project Variables | `<+variable.VARIABLE_NAME>`                                         | `${{ vars.USE_VARIABLES }}`                  | `$ {{ variable.var_name }} `                                          |