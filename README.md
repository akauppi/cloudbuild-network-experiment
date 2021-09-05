# cloudbuild-network-experiment

Minimal reproducible repo for studying sharing of network, in Cloud Build CI.

The goal:

- to be able to launch background servers
- ..and use them in following Cloud Build steps


## TL;DR

Solved this, for my use case (running Firebase Emulators within Cloud Build run, and then accessing them in later steps).

The solution is based on wrapping whatever tools you want to use (e.g. `npm`) into a Docker (or Docker Compose) step. This is acceptable to the author.


## Found clues

Cloud Build [documentation](https://cloud.google.com/build/docs/overview) states:

>Each build step is run with its container attached to a local Docker network named `cloudbuild`. This allows build steps to communicate with each other and share data.

However, it does not give a sample on how this is done!

The only source found by the author is: [Communicate between two containers in Google cloud build](https://stackoverflow.com/questions/52046609/communicate-between-two-containers-in-google-cloud-build/58991131#58991131) (SO)

Does it work, in 2021???


## Requirements

- `gcloud` installed, and a project activated (with CloudBuild APIs enabled)

## Steps

```
$ gcloud builds submit --config=ci/cloudbuild.yaml
```

### Expected / wished for

- Port 6768 would respond with 200; the Cloud Build run will pass

### Actual

```
Could not resolve host: abc
```


## Conclusion

Studying the said SO entry 3-5 times, and trying different approaches the author concludes (Sep 2021):

1. Wasn't able to:

  - get a "native" Cloud Build step, following Docker Compose `up -d`, to reach such service

  In particular, the mention of `cloudbuild` network in the Cloud Build docs is confusing. There are no samples, on how it would be used.

2. Was able to:

   - listen to the server, from `cloudbuild.yaml`, if the step is wrapped like this:

   ```
     # Listen from Docker. Works
  - name: docker
    args: ['run', '--network', 'workspace_default', 'gcr.io/cloud-builders/curl', 'abc:6768']
   ```
   
   Note that the name of the network is `workspace_default`, derived from the working directory of Cloud Build runs (`/workspace`).
   

This is enough.

It's a bit clumsy, but means that *any* steps can be declared within the `cloudbuild.yaml`, without help from the Docker Compose file. This is one of the goals of the author, to keep CI definitions away from cluttering the developer's folder.

