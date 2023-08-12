# ![ChRIS logo](https://raw.githubusercontent.com/FNNDSC/ChRIS_ultron_backEnd/master/docs/assets/logo_chris.png) miniChRIS-k8s

[![CI badge](https://github.com/FNNDSC/miniChRIS-k8s/workflows/CI/badge.svg)](https://github.com/FNNDSC/miniChRIS-k8s/actions?query=workflow%3ACI)
[![MIT license](https://img.shields.io/github/license/FNNDSC/miniChRIS-k8s)](LICENSE)

Run [_ChRIS_](https://chrisproject.org/) using [Podman](https://podman.io).

## Project Goals

_miniChRIS-k8s_ provides YAML files which can be read by `podman play kube` to run _ChRIS_.
It is a no-fuss solution for running _ChRIS_ locally for demoing or testing.

_miniChRIS-k8s_ is insecure and not be used in production. However, we aim for its code to be as simple as possible,
so that it can be used as a learning resource and starting point for writing production-ready configurations
of _ChRIS_.

_miniChRIS-k8s_ runs applications in "production mode" (where applicable)
so it's not great for the purpose of hacking _ChRIS_ backend services themselves.
For that, consult the project source repositories instead, e.g. https://github.com/FNNDSC/ChRIS_ultron_backEnd

Image tags are pinned to stable versions, so _miniChRIS_ might be
out-of-date with development versions of _ChRIS_ components.

See also: https://github.com/FNNDSC/miniChRIS-docker

### System Requirements

_miniChRIS-k8s_ requires Podman version 4.3 or above.
We aim to support "out-of-the-box" setups of rootless Podman (using slirp4netns and aardvark-dns).

Supported OS: Fedora Silverblue 37, Ubuntu 22.04, Arch Linux

<details>
<summary>
(Click to expand) Notes about installing Podman on Arch Linux.
</summary>

On Arch Linux, please consult the wiki: https://wiki.archlinux.org/title/Podman

Here's what worked for me (possibly helpful, definitely outdated info)

```shell
sudo pacman -Syu podman aardvark-dns
sudo usermod --add-subuids 100000-165535 --add-subgids 100000-165535 $USER
```

</details>

## Preconditions

Whether you're using Podman or Kubernetes, make sure your system is mostly not
running anything which might interfere with _miniChRIS_.

- Existing container/pod names might clash with _miniChRIS_.
  Make sure the output of `podman ps -a` or `kubectl get pods` is empty-ish.
- Running servers might clash with _miniChRIS_, which wants to bind TCP ports
  5005, 5010, 8000, 8010, 8080, 8020, and 8021.

## Podman: Quick Start

```bash
./podman/minichris.sh up
```

## Podman: Tear Down

```bash
./podman/minichris.sh down
```

## Using Podman Desktop

It is necessary to feed to Podman Desktop a single YAML file defining all _ChRIS_ resources.
The YAML file is produced by the command `./minichris.sh cat`.

```shell
./podman/minichris.sh cat > chris-all-in-one.yml
```

In the sidebar of Podman Desktop, click the "Containers" icon and then
in the top-right, click "Play Kubernetes YAML". Select the `chris-all-in-one.yml`
file and then click "Play."

![Screenshot of Podman Desktop: selecting a file](podman/Screenshots/podman_desktop_select_file.png)

Wait 1-5 minutes for _ChRIS_ to start up. Optionally, you can monitor the progress from a terminal with the command

```shell
./podman/minichris.sh watch-migration
```

When it's done, return the Containers screen and you should see
_ChRIS_ containers running.

![Screenshot of Podman Desktop: running _ChRIS_ containers](podman/Screenshots/podman_desktop_created_containers.png)

At this point _ChRIS_ is running, but it is empty. To add users and plugins to _ChRIS_,
go back to the terminal and run

```shell
podman/minichris.sh chrisomatic
```

Finally, you're ready to log in to http://localhost:8020 with username `chris`, password `chris1234`.

## Using Quadlet

```shell
systemctl --user start podman.service
./podman/minichris.sh cat > ~/.config/containers/systemd/minichris-aio.yml
cp podman/quadlet/minichris.kube ~/.config/containers/systemd/minichris.kube
systemctl --user daemon-reload
systemctl --user start minichris.service
```

> [!WARNING]  
> Friendly reminder to not use _miniChRIS_ in production.

## What to expect: performance

On a fast computer with good internet speed, running `./podman/minichris.sh up`
for the first time (pulls images) takes about 1.5 to 2 minutes.
Subsequent runs will be faster, about 40 seconds.

## Kubernetes

WIP

## Next Steps

A default superuser `chris:chris1234` is created.
You can access the admin dashboard at
http://localhost:8000/chris-admin/
and the *ChRIS\_ui* at http://localhost:8020

### Adding Plugins

[_chrisomatic_](https://github.com/FNNDSC/chrisomatic) is an easy way to
add plugins to _CUBE_. Currently, only adding plugins from https://chrisstore.co
is supported.

To add plugins, append the name of the plugin to `podman/chrisomatic.yml`
and then run

```shell
podman/minichris.sh chrisomatic
```

<!--
## ChRIS URLs

website        | URL
---------------|-----
ChRIS_ui       | http://localhost:8020/
ChRIS admin    | http://localhost:8000/chris-admin/
ChRIS_store_ui | http://localhost:8021/
Orthanc        | http://localhost:8042/
-->

<!--
### Add Plugins to CUBE

Plugins are added to _ChRIS_ via the Django admin dashboard.

https://github.com/FNNDSC/ChRIS_ultron_backEnd/wiki/%5BHOW-TO%5D-Register-a-plugin-via-Django-dashboard

Alternatively, plugins can be added declaratively.
A common use case would be to run locally built Python
[`chris_plugin`](https://github.com/FNNDSC/chris_plugin)-based
_ChRIS_ plugins. These can be added using `chrisomatic` by
listing their (docker) image tags. For example, if your local image
was built with the tag `localhost/myself/pl-workinprogress` by running

```shell
podman build -t localhost/myself/pl-workinprogress .
```

The bottom of your `podman/chrisomatic.yml` file should look like

```yaml
  plugins:
    - name: pl-dircopy
      version: 2.1.1
    - name: pl-tsdircopy
      version: 1.2.1
    - name: pl-topologicalcopy
      version: 0.2
    - name: pl-simpledsapp
      version: 2.1.0
    - localhost/myself/pl-workinprogress
```

After modifying `chrisomatic.yml`, apply the changes by running `./chrisomatic.sh`

For details, see https://github.com/FNNDSC/chrisomatic#plugins-and-pipelines
-->

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
- https://github.com/FNNDSC/miniChRIS-k8s/wiki/Podman-initContainers

#### Nuking miniChRIS on Podman

In case `./podman/minichris.sh down` does not work, run:

```shell
podman pod rm -af
podman volume prune -f
```

## Contact

- Github Issues: https://github.com/FNNDSC/miniChRIS-k8s/issues
- Matrix: https://matrix.to/#/#chris-general:fedora.im
