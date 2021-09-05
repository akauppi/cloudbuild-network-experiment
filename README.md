# cloudbuild-network-experiment

Trying to replicate [this SO entry](https://stackoverflow.com/questions/52046609/communicate-between-two-containers-in-google-cloud-build/58991131#58991131), where:

- a Docker Compose service is launched, from CloudBuild CI run
- a later step can interact with said service

Does it work, in 2021???


## Requirements

- `gcloud` installed, and a project activated (with CloudBuild)

   ```
   $ gcloud config get-value project
   Your active configuration is: [cicp-proto]
   ci-builder
   ```

## Steps

```
$ gcloud builds submit --config=ci/cloudbuild.yaml
```

### Expected / wished for

- Port 6768 would respond with 200

### Actual

```
Step #1: Already have image (with digest): gcr.io/cloud-builders/curl
Step #1: * Rebuilt URL to: abc:6768/
Step #1:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Step #1:                                  Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0* Could not resolve host: abc
Step #1: * Closing connection 0
Step #1: curl: (6) Could not resolve host: abc
```
