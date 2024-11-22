---
title: Harness Expressions - CEL
description: Learn how to use Harness Expressions in the new format, CEL.
sidebar_position: 30
---

[Harness Expressions] are getting an update. The old syntax involved using `<+ >` to signify a Harness expression, but now the platform is switching to standardize on the Common Expression Language or CEL. To learn more about CEL, go to the [CEL spec](https://github.com/google/cel-spec).

In this document, we will go over the parity matrix between old expressions and new, as well as point at key new functionalities. 

## Parity Matrix

| Component         | NG Expression                                                         | Github Actions Expression                  | Unified Pipeline Expression                                             |
| ----------------- | --------------------------------------------------------------------- | ------------------------------------------ | ----------------------------------------------------------------------- |
| Pipeline          | `<+pipeline.identifier>`                                            | `jobs.<job_id>`                              | `$ {{ [pipeline.i](http://pipeline.id/)d }} `                         |
|                   | `<+pipeline.name>`                                                  | `github.workflow`                            | `$ {{ [pipeline.nam](http://pipeline.name/)e }}`                      |
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
| Stage             | `<+stage.name>`                                                     | `jobs.<job_id>.name`                         | `$ {[{stage.nam](http://stage.name/)e}}`                              |
|                   | `<+stage.description>`                                              | `jobs.<job_id>.outputs.<output_id>`          | `$ {{ stage.description }}`                                           |
|                   | `<+stage.tags>`                                                     | NA                                         | `$ {{ stage.tags }}`                                                   |
|                   | `<+stage.identifier>`                                               | NA                                         | `$ {{ [stage.i](http://stage.id/)d }}`                                 |
|                   | `<+stage.output.hosts>`                                             | `jobs.<job_id>.outputs.<output_id>`          | ` $ {{ stage.output.hosts }}`                                         |
|                   | `<+stage.executionUrl>`                                             | `jobs.<job_id>.outputs.<output_id>`          | `$ {{ stage.execution_url }}`                                         |
|                   | `<+stage.delegateSelectors>`                                        | NA                                         | `$ {{ stage.delegate_selectors }}`                                     |
|                   | `<+pipeline.stages.STAGE_ID.status>`                                | `github.action_status` \| `job.status`         | ``                                                                    |
| Matrix / Looping  | `<+strategy.currentStatus>`                                         | `github.action_status` \| `job.status`         | ``                                                                    |
|                   | `<+strategy.node.STRATEGY_NODE_IDENTIFIER.currentStatus>`           | `github.action_status` \| `job.status`         | ``                                                                    |
|                   | `<+<+strategy.node>.get("STRATEGY_NODE_IDENTIFIER").currentStatus>` | `github.action_status` \| `job.status`         | ``                                                                    |
| Step Group        | `<+stepGroup.variables>`                                            | `${{ vars.USE_VARIABLES }}`                  | `$ {{ group.steps.variables.var_name }}`                              |
|                   | `<+stepGroup.getParentStepGroup>`                                   | NA                                         | `$ {{ group.steps.variables.var_name }}`                               |
| Step              | `<+step.name>`                                                      | `jobs.<job_id>.steps.name`                   | `$ {[{step.nam](http://step.name/)e }}`                               |
|                   | `<+step.identifier>`                                                | NA                                         | `$ {{ [step.i](http://step.id/)d }}`                                  |
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