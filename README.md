
# About Branches

| Branch name     | Use for|
|-----------------| ------|
| master          | Unreleased version https://docs-pcf-staging.cfapps.io/svc-sdk/service-metrics/1-n/|
| v1.5.x         |live, https://docs.pivotal.io/svc-sdk/service-metrics/1-5/|
| v1.4.x         |live, https://docs.pivotal.io/svc-sdk/service-metrics/1-4/ |


# Book repo for publishing this content

Book repo: **docs-book-service-metrics**

* **edge:** book branch that publishes the next unreleased version, from the **master** content branch. <br>**Pipeline:** https://concourse.run.pivotal.io/teams/cf-docs/pipelines/cf-services-sdk-edge

* **master:** book branch that publishes all the live versions in production. <br>**Pipeline:** https://concourse.run.pivotal.io/teams/cf-docs/pipelines/cf-services-sdk

# Process for working in this repo

## Submit changes to the docs through PRs

Instructions on doing a PR: https://docs.google.com/a/pivotal.io/document/d/14Go0uCj20BFMBzL2ddEKsZp-GONhVp0yr2cEFSskWnQ/edit?usp=sharing

## New (unpublished) releases

1. Commit new feature docs to **master** only.

2. When the release is ready to publish, the docs team will create a new live/production branch from master, for example, v1.6.x.

## Fixes and enhancements on master

1. For fixes and enhancement, make PRs to **master**.

2. Tell the docs team in the PR, if it should be applied to other specific branches (or make additional PRs to those branches).

## Fixes on branch only

If the fix or enhancement is only relevant to a particular branch, then apply the change to that branch only.
