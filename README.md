# ![ChRIS logo](https://raw.githubusercontent.com/FNNDSC/ChRIS_ultron_backEnd/master/docs/assets/logo_chris.png) miniChRIS-podman

[![CI badge](https://github.com/FNNDSC/miniChRIS-podman/workflows/CI/badge.svg)](https://github.com/FNNDSC/miniChRIS-podman/actions?query=workflow%3ACI)
[![MIT license](https://img.shields.io/github/license/FNNDSC/miniChRIS-podman)](LICENSE)

Run [_ChRIS_](https://chrisproject.org/) using [Podman](https://podman.io).

Documentation: https://chrisproject.org/docs/run/podman

## Project Goals

_miniChRIS-podman_ provides YAML files which can be read by `podman play kube` to run _ChRIS_.
It is a no-fuss solution for running _ChRIS_ locally for demoing or testing.

_miniChRIS-podman_ is insecure and not be used in production. However, we aim for its code to be as simple as possible,
so that it can be used as a learning resource and starting point for writing production-ready configurations
of _ChRIS_.

_miniChRIS-podman_ runs applications in "production mode" (where applicable)
so it's not great for the purpose of hacking _ChRIS_ backend services themselves.
For that, consult the project source repositories instead, e.g. https://github.com/FNNDSC/ChRIS_ultron_backEnd

Image tags are pinned to stable versions, so _miniChRIS_ might be out-of-date with development versions of _ChRIS_ components.
Please [contact us](https://matrix.to/#/!IdRwrIqeqMgMhFOQua:fedora.im?via=fedora.im) if the images are out of date.

For other ways to run _ChRIS_, see https://chrisproject.org/docs/run

### Developer's Notes: On Podman

YAML files in `podman/kube` should be interoperable between Podman and Kubernetes.
Podman supports a subset of the Kubernetes manifest spec:
Pod, Deployment, PersistentVolumeClaim, ConfigMap

Ideally, to add _pfcon_ to _CUBE_ we should be using the pod name of pfcon
`http://minichris-pfcon:5005/api/v1/`but it won't work.
See https://github.com/FNNDSC/ChRIS_ultron_backEnd/issues/505

There is an undocumented behavior of Podman where the host is visible to the container
via the name `host.containers.internal`, and we're able to talk to pfcon via the bound
host port.

#### Recommended Reading

- https://docs.podman.io/en/stable/markdown/podman-kube-play.1.html#podman-kube-play-support
- https://github.com/containers/podman/blob/main/docs/tutorials/basic_networking.md
- https://github.com/FNNDSC/miniChRIS-podman/wiki/Podman-initContainers

## Contact

- Github Issues: https://github.com/FNNDSC/miniChRIS-podman/issues
- Matrix: https://matrix.to/#/#chris-general:fedora.im
