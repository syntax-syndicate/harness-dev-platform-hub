---
title: Pipeline FAQs
description: Frequently asked questions about Harness Pipeline.
sidebar_position: 1000
---


#### How can users prevent a pipeline from failing halfway due to missing permissions?
Harness remote pipelines don’t fail fast, meaning authorization errors occur at execution time rather than at the start. Consider using inline pipelines with fail-fast configurations to prevent this issue.

#### How can users improve JSON response handling in Harness API calls?
Large API responses may be truncated due to log size limits. Consider fetching data in smaller batches or increasing logging limits if possible.

