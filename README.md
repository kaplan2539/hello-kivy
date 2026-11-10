# hello-kivy

A minimal Kivy Hello World app packaged for Android with
[python-for-android](https://python-for-android.readthedocs.io/).

## Build

The app source is in `src/`. You can build locally with the same Docker image
used by CI, without installing python-for-android and its Android build
dependencies on your host. From the repository root, run:

```sh
docker run --rm \
  -v "$PWD:/workspace" \
  -w /workspace \
  ghcr.io/kaplan2539/p4a-container \
  p4a apk \
    --private src \
    --name hello_kivy \
    --dist_name hello_kivy \
    --package de.deac0.hello_kivy \
    --version 0.1 \
    --bootstrap=sdl2 \
    --requirements=python3,kivy \
    --arch=armeabi-v7a
```

The repository is mounted into the container, so the generated APK is
available in the repository directory on the host. This requires Docker and
access to the `ghcr.io/kaplan2539/p4a-container` image.

GitHub Actions uses this image and build command too. It uploads the APK as an
artifact and publishes it on GitHub Pages with a QR code.
