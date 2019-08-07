# Mac OS X - Hyperkit

To use the Hyperkit provider on Mac OS X, you will need a running Hyperkit installation with qcow support. Simply follow the build and installation instructions here: https://github.com/moby/hyperkit and then place the built hyperkit binary somewhere in your PATH (/usr/local/bin/hyperkit is the default expected in Vagrantfile). Also, make sure the qcow module is installed:

```
> brew search opam
> brew search libev

If not installed:

> brew install opam libev
> opam init
> eval `opam config env`
> opam install uri qcow.0.10.3 conduit.1.0.0 lwt.3.1.0 qcow-tool mirage-block-unix.2.9.0 conf-libev logs fmt mirage-unix prometheus-app
```

The other (probably easier) option is to install Docker for Mac (https://docs.docker.com/docker-for-mac/install/, https://hub.docker.com/editions/community/docker-ce-desktop-mac) which runs on top of Hyperkit (untested). It will install Hyperkit with qcow support.

To use Vagrant to provision the box (with Ansible), install sshpass:

```
brew install http://git.io/sshpass.rb
```

Install Vagrant Hyperkit plugin:

```
vagrant plugin install vagrant-hyperkit
```

Install the Vagrant Hyperkit box:

```
vagrant box add accanto/hyperkit
```

Edit Vagrantfile and make sure that `hyperkit.xhyve_binary` points to where you installed your hyperkit binary.

Edit the `ansible/ansible-variables.yml` file and change the advertise address and flannel interface to:
```
advertise_address: 192.168.64.2
```

Now return to the [AIO Install Instructions](install_instructions.md) to continue with the installation.