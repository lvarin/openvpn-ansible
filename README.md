# OpenVPN

This is a simple Ansible file that deploys an OpenVPN.

## Quickstart

1. You need to pre-create a Virtual machine (For [CSC Pouta](https://pouta.csc.fi) follow the [Creating a virtual machine in Pouta](https://docs.csc.fi/cloud/pouta/launch-vm-from-web-gui/) guide).

1. The Virtual machine needs to have the **UDP port 1194** open to your machine (For [CSC Pouta](https://pouta.csc.fi) you can follow the [Creating a security group](https://docs.csc.fi/cloud/pouta/networking/#create-a-security-group) guide).

1. Edit the file `inventory` to replace the `<HOSTNAME>` value by the DNS name of the floating IP of your VM.

1. Edit the file `inventory` to replace the `<USER>` by the name of the user you want to create. You can add more users to the list if needed following the YAML
format.

1. Run ansible:

  ```sh
  ansible-playbook site.yaml -i inventory
  ```

1. Copy into your computer the `<USER>.ovpn` file that Ansible generated. The file is located in the VPN server in `/tmp`. The file contains a certificate and key user combo and a Certificate Authority (`ca`) file.

1. You can use the `.ovpn` file in the VPN configuration of your computer (If you run Linux you can test it before hand if you do something like `sudo openvpn --config <USER>.ovpn`).
