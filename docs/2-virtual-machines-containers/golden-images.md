# Golden Images

**Golden Images** (also called base images or image templates) are machine images with pre-configured operating systems. They typically include specialized configurations, security patches and common libraries and tools. Once a Golden Image is created it can be used to easily and reliably create identical environments on different hardware or virtual machines. 

![](img2/linux.svg ':size=100x100 :class=icon')

#### Exercise 1: Manual Build

For this exercise you will create a Golden Image by manually installing and configuring CentoOS 7 in a VirtualBox VM.

1. Download the [CentOS 7](https://www.centos.org/download/) ISO for a 64 bit x86 machine.
 
2. Using [VirtualBox](https://www.virtualbox.org/wiki/Downloads), create a new virtual machine and install CentOS using the ISO you downloaded.
 
3. Log into the new virtual machine from your local machine using SSH (not VirtualBox Manager).

  ?> To connect to the SSH port of the virtual machine you may need to change the network configuration in VirtualBox. VirtualBox and can create several different [types of networks](https://www.virtualbox.org/manual/ch06.html) for the virtual machine which will affect how you connect to it. Experiment with a few different network types and compare the pros and cons.

5. Update CentOS to have the latest packages, and install `java-1.8.0-openjdk-devel`.

6. Create an SSH key pair and add the public key to the virtual machines authorized keys. Confirm you can SSH into the virtual machine using public key authentication instead of the password.

7. Clone the newly updated box, and name it Golden Image.

8. Create a snapshot of your Golden Image.

9. Using your Golden Image, create a file in it: `touch /etc/hello_world`.

10. Revert the snapshot.

11. Move back to the latest snapshot.

## Immutable Infrastructure

Golden images can be a critical part of a very important strategy for managing software environments know as **Immutable Infrastructure**. We will cover Immutable Infrastructure in more depth throughout the DevOps Bootcamp. For now watch this video of HashiCorp co-founder and CTO Armon Dadgar explaining [What is Mutable vs. Immutable Infrastructure](https://www.hashicorp.com/resources/what-is-mutable-vs-immutable-infrastructure/).

## Automation

You should now be familiar with the process for creating a Golden Image with VirtualBox. Imagine that security updates needed to be kept up to date in the Golden Image. You could repeat the steps from the first exercise to manually create a new image but doing so every time a security patch is released could be time consuming and error prone.

This kind of repetitive work is often referred to as toil. Reducing toil from various roles in software delivery (developers, operations, SRE, etc) through automation is an important part of DevOps transformations.

> **Toil** is the kind of work tied to running a production service that tends to be manual, repetitive, automatable, tactical, devoid of enduring value, and that scales linearly as a service grows. -- [Site Reliability Engineering: Eliminating Toil](https://landing.google.com/sre/sre-book/chapters/eliminating-toil/)

![](img2/packer.svg ':size=350x350 :class=icon')

> **Packer** is an open source tool for creating identical machine images for multiple platforms from a single source configuration. -- [Read more](https://www.packer.io/intro)

Since Packer defines everything needed to create an image in configuration files it can be run without manual intervention and is ideal for automation. It also makes it more convenient to maintain and update images over time. Packer has a variety of other features and use cases.

- Continuous Delivery
  - Lightweight, portable, and command-line driven.
  - New images can be launched and tested, verifying that the infrastructure changes work.
- Dev/Prod Parity
  - Keeps development, staging, and production as similar as possible.
  - Can be used to generate images for multiple platforms.
- Configuration as code
  - Image changes can be maintained and tracked using version control.
- Appliance/Demo Creation
  - Automatically create appliances with software preinstalled.

#### Exercise 2: Automated Build

In this exercise you will use Packer to create a Golden Image with the same configuration as the one you created in the first exercise.

1. [Install Packer](https://learn.hashicorp.com/packer/getting-started/install) on your machine.

2. Create a Packer template file to build the minimal CentOS image using the ISO you downloaded.
  
  ?> Read the Packer [Getting Started: Build an Image](https://learn.hashicorp.com/packer/getting-started/build-image) for general information on creating a Packer template.

  ?> Your Packer template will need to include a [VirtualBox Iso builder](https://www.packer.io/docs/builders/virtualbox/iso) to install CentoOS 7.

3. In the same folder as the Packer template create folder name `http` and copy the kickstart file `anaconda-ks.cfg` from the Golden Image you created in the first exercise into it.
  
  ?> CentOS uses the [kickstart](https://docs.centos.org/en-US/centos/install-guide/Kickstart2/) file to answer questions needed by the installer.

  ?> You may need to make some changes to the kickstart file.

4. Configure the Packer template to serve the kickstart file via HTTP and start the CentoOS installer with a link to download and use the kickstart file using the [http_directory](https://www.packer.io/docs/builders/virtualbox/iso#http-directory-configuration) and [boot_command](https://www.packer.io/docs/builders/virtualbox/iso#boot-configuration) configuration options.

  ?> Here is a hint for what the `boot_command` option should like like.

  ```JSON
    "boot_command": [
      "<tab> text ks=http://{{ .HTTPIP }}:{{ .HTTPPort }}/anaconda-ks.cfg<enter><wait>"
    ],
  ```

5. Add provisioners to you Packer template to install updates, install `java-1.8.0-openjdk-devel` and add your SSH public key to authorized keys.
  
  ?> Read the Packer [Getting Started: Provision](https://learn.hashicorp.com/packer/getting-started/provision) guide for more information.

6. Run `packer build` to create your Golden Image.

7. Import the Golden Image into VirtualBox using the OVF (Open Virtualization Format) file generated by Packer. Start and SSH into the virtual machine. Verify the machine is configured as expected.

# Deliverable

Discuss as a group the following topics.
 - What are some use cases for Golden Images and how do they relate to enterprise organizations?
 - What are some benefits of using Virtual Machines and how do they relate to enterprise organizations?
 - When might a snapshot be used?
 - What are the benefits of Immutable Infrastructure?
 - How do Golden Images relate to Immutable Infrastructure?
 - What are some examples of Toil?
 - How can a tool like Packer add value to an enterprise organization?
