# Compile and build

## Persistence engine selection

Loggie provides badger engines in addition to sqlite in v1.5 and later versions.

For the configuration of the two persistence engines, sqlite and badger, please refer to [here](../reference/global/db.md).

Why introduce the badger engine? The problems with using sqlite and CGO are:

- SQLite has requirements for the c lib library version of the environment. In the host deployment scenario, there are many machines with lower versions that may not be able to run.
- Avoid some construction pitfalls introduced by CGO, such as building multi-architecture (amd64/arm64) images

## Container image build

By default, Loggie will build images for all release branches and main branches and push them to [dockerhub](https://hub.docker.com/r/loggieio/loggie/). However, pulling the image from dockerhub may have problems such as current limitation, so it is recommended that you build the image yourself or re-push it to your own image warehouse.

Two Dockerfiles are provided under the Loggie project:

- Dockerfile: The default dockerfile uses sqlite, so it will also be built using CGO.
- Dockerfile.badger: You can use the `-f` parameter during docker build to specify the use of this dockerfile, which uses badger as the persistence engine, pure Golang, and no CGO.

In addition to using docker build, you can also use make to build:

```bash
make docker-build REPO=<YourRepoHost>/loggie TAG=v1.x.x
make docker-push REPO=<YourRepoHost>/loggie TAG=v1.x.x
```

Or build and push multi-architecture images directly:

```bash
make docker-multi-arch REPO=<YourRepoHost>/loggie TAG=v1.x.x
```

Note:

- The TAG here does not need to be passed. By default, it will be automatically generated based on the tag or branch in Git.

### Multiple Architectures

All Dockerfiles in the Loggie project support multi-architecture construction. The Loggie image on dockerhub is multi-architecture of amd64 and arm64, and the local architecture will be automatically recognized when pulling.

However, if you want to modify the tag and re-push it to your own warehouse, please do not directly docker pull & docker tag & docker push. This will only push the image of your local architecture, causing the multi-architecture image in the warehouse to become invalid.
If you want to push multi-architecture images to your own warehouse, you can use some other open source tools, such as [regctl](https://github.com/regclient/regclient/blob/main/docs/regctl.md), which can be used locally Use a command like `regctl image copy loggieio/loggie:xxx <YourRepoImage>` to push.

If you connect your own build process or tools, please use `make docker-multi-arch` or `docker buildx` to build multi-architecture images.

## Binary build

Binaries are available for host deployment scenarios.

- sqlite:

  ```bash
  make build
  ```

- badger:

  ```bash
  make build-in-badger
  ```

If cross compilation is required, please add GOOS and GOARCH.

If the local build using `make build` fails, you can modify the extra_flags extra build parameters in the makefile. Please try to remove `-extldflags "-static"`.
