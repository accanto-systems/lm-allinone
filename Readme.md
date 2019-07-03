# All-in-One Stratoss&trade; Lifecycle Manager Environment

This project provides a set of scripts to automatically create an All-in-One Stratoss&trade; Lifecycle Manager environment suitable for development, demonstration or proof of concept purposes.

The following software components are installed on a target single (virtual) server:

- **Docker**: Required to run containers for later components
- **Kubeadm**: Kubernetes minimal distribution tailored to run in a single machine small footprint
- **Stratoss&trade; Lifecycle Manager**: A minimal deployment Stratoss LM
- **Ansible RM**: Ansible Resource Manager installed and attached to Stratoss LM

Scripts are provided to create AIO environments using Vagrant, supporting Virtualbox, libvirt and hyperkit. Alternatively existing servers can be provisioned directly using the Ansible playbooks provided.

# Install

Follow the [Install Instructions](./docs/install_instructions.md) to get started
