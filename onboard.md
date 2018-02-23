# How to onboard to a01 testing

## Docker container

A01 runs tests inside a container. The container should look like this:

- Test code sohuld be included in the container, of course, ready to run.

- Include a test list. As a small example, a two test list for the Go SDK looks like this:

``` json
[
  {
    "ver": "1.0",
    "execution": {
      "command": "go test -v github.com\\Azure-Samples\\azure-sdk-for-go-samples\\sql -run ^ExampleDatabaseQueries$"
    },
    "classifier": {
      "identifier": "sql/DatabaseQueries",
      "type": "Live"
    }
  },
  {
    "ver": "1.0",
    "execution": {
      "command": "go test -v github.com\\Azure-Samples\\azure-sdk-for-go-samples\\storage -run ^ExampleUploadBlockBlob$"
    },
    "classifier": {
      "identifier": "storage/UploadBlockBlob",
      "type": "Live"
    }
  }
]
```

This example is showing all the required fileds. However, the test list might also contain other fields.

- Have an `app` folder with:
  - `get_index` program. This program should print the test list.
  - `a01droid` binary, you can get it in https://a01tools.blob.core.windows.net/droid/linux/a01droid

- Have the following labels:

``` dockerfile
LABEL a01.product="<your-product-name>"
LABEL a01.index.schema="v2"
```

Other labels can be added, for example, if tests run with specific credentials set as environment variables:

``` dockerfile
LABEL a01.env.AZ_CLIENT_SECRET="secret:<your-secret-found-in-kubeconfig-file>"
```

- And at the end, run the a01droid:

``` dockerfile
CMD app/droid
```

The container image should be pushed to our container registry before creating a run. This can be done as part of CI when checking in code to your product's repo, so tests can be run with all the newest changes.