This directory contains `*.yml` files which are Kubernetes resources
that can be interpreted by `podman play kube`.

**IMPORTANT:** each file must start with `---` and every filename
must match the regular expression `^\d\d-.+\.yml$`

Read: https://github.com/FNNDSC/miniChRIS-podman/wiki/Podman-initContainers
