# cloudbuild-network-experiment

Minimal reproducible repo for studying sharing of network, in Cloud Build CI.

The goal:

- to be able to launch background servers
- ..and use them in following Cloud Build steps


## Found clues

Cloud Build [documentation](https://cloud.google.com/build/docs/overview) states:

>Each build step is run with its container attached to a local Docker network named `cloudbuild`. This allows build steps to communicate with each other and share data.

However, it does not give a sample on how this is done!

The only source found by the author is:

- [Communicate between two containers in Google cloud build](https://stackoverflow.com/questions/52046609/communicate-between-two-containers-in-google-cloud-build/58991131#58991131) (SO)

Does it work, in 2021???


## Requirements

- `gcloud` installed, and a project activated (with CloudBuild APIs enabled)

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


## Work-around

I can place also the tests into Docker Compose, and run them that way from Cloud Build. But this is non-ideal since it outsources CI-specific details to the `docker-compose.yml`.

I would rather have the CI just launch the background services, then be able to run tests on the `npm` image - much like the real developer would work.

