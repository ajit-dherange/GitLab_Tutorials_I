# What is cache?

It's a set of files that a job can download before running and upload after execution. By default, the cache is stored in the same place where GitLab Runner is installed. If the distributed cache is configured, S3 works as storage.

Let's suppose you run a Pipeline for the first time with a local cache. The job will not find the cache but will upload one after the execution to runner01. The second job will execute on runner02, it won't find the cache on it either and will work without it. The result will be saved to runner02. Lint, the third job, will find the cache on runner01 and use it (pull). After execution, it will upload the cache back (push).

# What are artifacts?

Artifacts are files stored on the GitLab server after a job is executed. Subsequent jobs will download the artifact before script execution.

Build job creates a DEF artifact and saves it on the server. The second job, Test, downloads the artifact from the server before running the commands. The third job, Lint, similarly downloads the artifact from the server.

To compare the artifact is created in the first job and is used in the following ones. The cache is created within each job

https://safe.menlosecurity.com/https://dev.to/drakulavich/gitlab-ci-cache-and-artifacts-explained-by-example-2opi

# Create a release when a commit is merged to the default branch

In this CI/CD example, the release is triggered when you merge a commit to the default branch. You can use this method if your release workflow does not create a tag manually.

Key points in the following extract of an example .gitlab-ci.yml file:

* The Git tag, description, and reference are created automatically in the pipeline.
* If you manually create a tag, the release_job job does not run.


```
release_job:
  stage: release
  image: registry.gitlab.com/gitlab-org/release-cli:latest
  rules:
    - if: $CI_COMMIT_TAG
      when: never                                  # Do not run this job when a tag is created manually
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH  # Run this job when commits are pushed or merged to the default branch
  script:
    - echo "running release_job for $TAG"
  release:                                         # See https://docs.gitlab.com/ee/ci/yaml/#release for available properties
    tag_name: 'v0.$CI_PIPELINE_IID'                # The version is incremented per pipeline.
    description: 'v0.$CI_PIPELINE_IID'
    ref: '$CI_COMMIT_SHA'                          # The tag is created from the pipeline SHA.
```

https://safe.menlosecurity.com/https://docs.gitlab.com/ee/user/project/releases/release_cicd_examples.html
