# Managing Kubernetes at Scale

## Introduction

During this workshop, you will learn through the use of customer resource definitions, controllers and APIs how to manage and operate Kubernetes at scale.

* [Introduction](#introduction)
* [Prerequisites](#prerequisites)
* [1. Multi-cloud](#1-Multi-cloud-lab)
* [2. Multi-cluster)](#2-Multi-cluster)
* [3. Multitenancyt](#3-Multitenancy)


## Prerequisites

You need either a Linux, MacOS or a Windows client with the ability to ssh to an AWS IP address.


## Jumpserver

Jumpservers have been deployed for each student with all prerequisites installed. First, go to the student data spreadsheet and select a host by entering your name.  Then, download the ssh-private-key (id_rsa_student#) and change the file permissions.  Finally, ssh to the ipaddress of your assigned jumpserver using the -i option to specify the identity file to be used.  The username for the Jumpserver is "centos".

### Linux/MAC
For Mac and Linux clients you must change the permission on the file.
```
chmod 400 id_rsa_student#
```
```
ssh -i id_rsa_student# centos@jumpserver-ip-address
```
### Windows
#### Log in to PuTTY by using your SSH private key
Use the following steps to log in to PuTTY by using your SSH private key:

1. Specify the IP address of your assigned jumpserver then enter a name for the session and click Save.
2. Click Connection > Data in the left navigation pane and set the Auto-login username to "centos".
3. Click Connection > SSH > Auth in the left navigation pane and configure the SSH private key to use by clicking Browse under Private key file for authentication.
4. Navigate to the location where you saved your SSH private key file, select the file, and click Open.
5. The file path for the SSH private key file now displays in the Private key file for authentication field.
6. Click Session in the left navigation pane, then click Save in the Load, save or delete a stored session section.
7. Click Open to begin your session with the server.

### Clients with local policy or VPN issues
>Use the [Google Cloud Shell](https://console.cloud.google.com/cloudshell). (Requires a Google account)
Once your Google Cloud Shell has started, you will have to copy the contents of you id_rsa_student#.pem file to a local file in the cloud shell.  Then change the permission on the file and ssh into the jump host.


```
vi id_rsa_student#
```
```
chmod 400 id_rsa_student#
```
```
ssh -i id_rsa_student# centos@jumpserver-ip-address
```
