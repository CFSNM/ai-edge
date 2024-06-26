# AI Edge testing

Place for testing and to verify the MLOps pipelines are working correctly. These tests use the `k8s.io/client-go` package to interact with the cluster. Using `oc login` to log into the cluster as normal should mean little setup is needed to run the tests.

A local install of the Go compiler is needed to run the tests. Go version `1.21` is required to run the tests.

## Setup
- Log into the target cluster using `oc login`. This will update your default `kubeconfig` for the tests to use
- Create a S3 bucket with the models in the root directory

The following enviroment variables are required to run the test setup and the tests themselves. If any of these are not set then the tests will not run. Read [here](../../pipelines/README.md#ai-edge-end-to-end-pipeline) for more context on how to set these up.

- `AWS_SECRET_ACCESS_KEY` - Secret from AWS
- `AWS_ACCESS_KEY_ID` - Access key from AWS
- `S3_REGION` - Region of the bucket used to store the model
- `S3_ENDPOINT` - Endpint of the bucket
- `IMAGE_REGISTRY_USERNAME` - quay.io username
- `IMAGE_REGISTRY_PASSWORD` - quay.io password
- `GIT_TOKEN` - Auth token used by the git ops pipeline to make a pull request, [see info here](../../pipelines/README.md#git-repository-and-credentials)
- `GIT_USERNAME` - Username linked to the `GIT_TOKEN`

The following enviroment variables are optional. You may still need to set them for the tests to pass depending on your setup. Read [here](../../pipelines/README.md#ai-edge-end-to-end-pipeline) for more context on how to set these up.

- `GIT_SELF_SIGNED_CERT` - Self-signed cert to be used by git when cloning
- `S3_SELF_SIGNED_CERT` - Self-signed cert to be used for S3 when pulling models

Run `go-test-setup` to get the namespace ready for testing. This target is only expected to be run once, if you want to re-run the tests in the same namespace you can just re-run the `go-test` target, read the next section for more detail.

```bash
make go-test-setup
```

## Run tests locally

The following enviroment variables are required to run the tests. If any of these are not set then the tests will not run. Read [here](../../pipelines/README.md#ai-edge-end-to-end-pipeline) for more context on how to set these up.

- `S3_BUCKET` - Name of S3 bucket that contains the models
- `NAMESPACE` - Cluster namespace that tests are run in
- `TARGET_IMAGE_TAGS_JSON` - JSON array of image tags that the final image will be pushed to. E.g. '["quay.io/user/model-name:e2e-test"]'
- `GIT_REPO` - Git repo URL used to make a pull request in the git ops pipeline (https://github.com/org/repo)
- `GIT_API_SERVER` - Git API server (api.github.com)
- `GIT_BRANCH` - Base branch used for pull request in git ops pipeline

The following enviroment variables are optional. You may still need to set them for the tests to pass depending on your setup. Read [here](../../pipelines/README.md#ai-edge-end-to-end-pipeline) for more context on how to set these up.

- `GIT_SELF_SIGNED_CERT` - Self-signed cert to be used by git when cloning
- `S3_SELF_SIGNED_CERT` - Self-signed cert to be used for S3 when pulling models
- `GO` - Custom Go installation path that is not set in your `PATH`

```bash
make go-test
```
Set the go binary used for testing that is in your `PATH`
```bash
make GO=go1.19 go-test
```

If you want to re-run the tests in the same namespace you can just re-run the `go-test` target. The tests fail if there are **any** failed pipeline runs. Therefore if you have a failed run and want to re-test make sure to delete any that have failed.

## CI/CD with Github Actions
Not yet implemented
