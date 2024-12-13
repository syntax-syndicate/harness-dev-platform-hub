---
title: Guide to Pre-Process and Validate Workflows Input 
description: A Step-by-Step Guide to Pre-Process and Validate Workflows input using a Separate Harness Pipeline
sidebar_position: 7
---

## Introduction

In this tutorial, we will use a Harness pipeline to validate the Workflow inputs. Upon successful validation, this pipeline will trigger another pipeline that serves as the orchestrator for the Workflow.

## Pre-Requisite:
 
- Make sure you are assigned the [IDP Admin Role](https://developer.harness.io/docs/internal-developer-portal/rbac/resources-roles#1-idp-admin) or another role that has full access to all IDP resources. 
- Create a **GitHub** connector named `democonnector` at the account scope. This connector should be configured for a GitHub organization (personal accounts are currently not supported by this tutorial). 

## Create a pipeline 

Begin by creating a pipeline for onboarding a new service

### Create an IDP Stage




