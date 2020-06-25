# All-in-One Stratoss&trade; Lifecycle Manager and CICDHub Environment

This project provides a set of scripts to automatically create an All-in-One Stratoss&trade; Lifecycle Manager and CICDHub environment suitable for development, demonstration or proof of concept purposes.

The following software components are installed on a target single (virtual) server:

- **Docker**: Required to run containers for later components
- **MicroK8s**: Kubernetes minimal development distribution tailored to run in a single machine small footprint (not production)
- **Stratoss&trade; Lifecycle Manager**: A minimal deployment Stratoss LM
- **CICDHub**: A minimal deployment CICDHub (https://github.com/accanto-systems/lm-cicdhub)
- **Ansible RM**: Ansible Resource Manager installed and attached to Stratoss LM

Scripts are provided to create AIO environments using Vagrant, supporting Virtualbox, libvirt and hyperkit. Alternatively existing servers can be provisioned directly using the Ansible playbooks provided.

# Install

Clone this repository and follow the [Install Instructions](./docs/install_instructions.md) to get started

**Note:** do not use lm-allinone in production, nor on a public cloud or a system open to the Internet (microk8s is not secure and is not intended to run in these environments)

# Troubleshoot

Having issues with the environment? This [troubleshooting guide](./docs/troubleshoot.md) may help (Note: this guide is very new so is currently lacking in content. Please submit PRs to add to it if you discover and issue and a fix).
