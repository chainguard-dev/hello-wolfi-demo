# hello-wolfi-demo
Demo for the [Hello Wolfi Webinar](https://speakerdeck.com/erikaheidi/hello-wolfi)

## Demo Steps Overview
_These steps were executed on an Ubuntu 22.04 host Linux machine._

1. Make sure you have Docker installed on your machine
2. Download the `cgr.dev/chainguard/melange` and `cgr.dev/chainguard/apko` images with `docker pull`
3. Generate melange signing keys
4. Build the `melange-php.yaml` package
5. Build the `composer-php.yaml` package
6. Build the `melange-app.yaml` package
7. Build the `apko.yaml` container image
8. Load the image with `docker load`
9. Run the image with `docker run --rm <image-name>`

## Detailed Steps

### Download melange and apko images

```shell
docker pull cgr.dev/chainguard/melange
docker pull cgr.dev/chainguard/apko
```
### Generate melange signing keys

```shell
docker run --rm -v "${PWD}":/work cgr.dev/chainguard/melange keygen
```
### Build the php package

```shell
docker run --privileged --rm -v "${PWD}":/work -- \                                                                              
  cgr.dev/chainguard/melange build melange-php.yaml \
  --arch x86_64 \
  --signing-key melange.rsa --keyring-append melange.rsa.pub
```

### Build the composer package

```shell
docker run --privileged --rm -v "${PWD}":/work -- \                                                                              
  cgr.dev/chainguard/melange build melange-composer.yaml \
  --arch x86_64 \
  --signing-key melange.rsa --keyring-append melange.rsa.pub
```

### Build the app package

```shell
docker run --privileged --rm -v "${PWD}":/work -- \                                                                              
  cgr.dev/chainguard/melange build melange-app.yaml \
  --arch x86_64 \
  --signing-key melange.rsa --keyring-append melange.rsa.pub
```

### Build the container image

```shell
docker run --rm -v ${PWD}:/work cgr.dev/chainguard/apko build --debug apko.yaml hello-wolfi:latest hello-wolfi.tar -k melange.rsa.pub
```

### Load the container image

```shell
docker load < hello-wolfi.tar
```

### Run the image

```shell
docker run --rm hello-wolfi
```