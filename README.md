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

## Ansible process explained

![End result](./openVPN.drawio.svg)

Ansible will connect to the pre-created VM and install both `openvpn` and `easy-rsa`. Easy RSA will be used to create the required certificates, which are:

1. `CA.crt` is the Certificate Authority file. This is the base for the rest of the certificates.

1. `<HOSTNAME>.crt` and `<HOSTNAME>.key`, used to identify the server (like when https web is used). This pair of certificates is created using the `server` type.

1. `<USER>.crt` and `<USER>.key`, used to identify the user that connects to the OpenVPN server. This pair of certificates is created using `client` type. Each user gets a different pair.

1.`dh2048.pem` is used for Key Exchange. The DH parameters are sent to the client allowing it to generate a shared pre-master secret key. Then a master secret will be generated from that pre-master secret and used to encrypt communication data.

Then Ansible generates a single `server.conf` file referencing the aforementioned certificates files, and enables and starts the service. Also one `ovpn` file per user is generated with all the configuration and the necessary certificates embedded. This means that a given user only needs that ovpn in order to connect to the VPN. Finally Ansible will enable forwarding. This makes accessible from the client computer any host in the same network that the VPN server. The Security groups and local VM firewall will need to be opened to the VPN server in order to enable the connections.
