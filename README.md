# bdellegrazie/docker-centos-systemd

A Docker image based on `centos` that runs `systemd` with a minimal set of
services.

Intended for use testing Ansible roles with Molecule

**Development use only. Do not use in production!**

## Supported tags

* `7`

## But why?

Ansible roles often provide services. Testing these properly requires a service manager.

## Running

You need to add a couple of flags to the `docker run` command to make `systemd`
play nice with Docker.

We must disable seccomp because `systemd` uses system calls that are not
allowed by Docker's default seccomp profile:

    --security-opt seccomp=unconfined

CentOS's `systemd` expects `/run` and `/run/lock` to be `tmpfs` file systems,
but it can't mount them itself in an unprivileged container:

    --tmpfs /run
    --tmpfs /run/lock
    --tmpfs /tmp

`systemd` needs read-only access to the kernel's cgroup hierarchies:

    -v /sys/fs/cgroup:/sys/fs/cgroup:ro

Allocating a pseudo-TTY is not strictly necessary, but it gives us pretty
color-coded logs that we can look at with `docker logs`:

    -t

## Testing

This image is for Ansible testing with Molecule. You can still create a container from it. Run the following command:

    docker run -d --name systemd --security-opt seccomp=unconfined --tmpfs /tmp --tmpfs /run --tmpfs /run/lock -v /sys/fs/cgroup:/sys/fs/cgroup:ro -t bdellegrazie/centos-systemd

## Known issues

## Contributors

Based on the work done by [Solita](https://github.com/solita/docker-systemd/)

## License

Copyright © 2019 [bdellegrazie](https://github.com/bdellegrazie). Licensed under [the MIT license](https://github.com/docker-centos-systemd/blob/master/LICENSE).
