**************************
Linux Image Creation Tools
**************************

This document describes a number of alternatives in the ecosystem for
creating Linux operating system images. In the Linux ecosystem, there
are a number of tools that support an array of workflows and targets.
Depending on what kind of systems and flexibility required, different
tools are available to handle this.

KIWI
====

-  Vendor: openSUSE Project
-  Site: http://osinside.github.io/kiwi/
-  Supported host distributions: Fedora, CentOS/RHEL, (open)SUSE
-  Supported target distributions: Fedora, CentOS/RHEL, (open)SUSE, Debian/Ubuntu
-  Definition format: XML, YAML, or JSON
-  Creates: filesystem trees, container base images, disk images, live ISOs, PXE images
-  Implemented in: Python

The KIWI image creation tool is used by SUSE for producing media for
openSUSE and SUSE Linux Enterprise. It can create bare filesystem trees,
container base images (for Docker or other OCI runtimes to consume and
layer on), disk images (targeted for VMs or physical hardware), ISOs
(with persistent live USB), and images for network-based provisioning
through PXE. The advantage is the high degree of customizability,
however it does come with the cost of time as it is always built from
scratch (typical builds are around 5-15 minutes). It is likely the most
complete tool available. KIWI also sports integration with OBS and Koji,
in which the image configuration can be pushed to a build system
instance and it will build the images accordingly. When in an Open Build
Service instance, it will get auto-rebuilt as updates are detected.

mkosi
=====

-  Vendor: systemd
-  Site: http://mkosi.systemd.io, https://github.com/systemd/mkosi
-  Supported host distributions: Fedora, CentOS/RHEL, (open)SUSE, Debian/Ubuntu, Arch, Gentoo, NixOS
-  Supported target distributions: Fedora, CentOS/RHEL, (open)SUSE, Debian/Ubuntu, Arch
-  Definition format: INI-style (systemd)
-  Creates: filesystem trees, disk images, archives, initrds, UKIs, sysexts/confexts/portables
-  Implemented in: Python

The mkosi tool originates from the systemd project and was originally
created to simplify making opinionated “legacy-free” images for
systemd development. Those images were initially UEFI-only, formatted
as GPT disks with no protective MBR, and used systemd-boot as the UEFI
boot manager with boot entries configured using the `bootloader
specification <https://systemd.io/BOOT_LOADER_SPECIFICATION/>`__, and
partitions labeled with UUIDs per the `discoverable partition
specification <https://systemd.io/DISCOVERABLE_PARTITIONS/>`__.
Currently UEFI and BIOS images are supported, using sd-boot (only for
UEFI) or grub, as well as direct kernel boots for Qemu. SecureBoot is
supported both using self-enrolled certificates and shim. Images are
built from scratch, but through leveraging systemd tooling and caching
of downloaded packages and package manager metadata, image builds take
less time than competitors (less than one minute for small images when
caches are populated). Incremental builds and overlays can also reduce
image build times. Images can be built by unprivileged users (in
particular, without access to loop devices or virtualization).

virt-builder / virt-install (libguestfs)
========================================

-  Vendor: Red Hat
-  Site: http://libguestfs.org/
-  Supported host distributions: Fedora, CentOS/RHEL, (open)SUSE, Debian/Ubuntu
-  Supported target distributions: Fedora, CentOS/RHEL, (open)SUSE, Debian/Ubuntu
-  Definition format: custom text format (lines of commands)
-  Creates: virtual machine disk images
-  Implemented in: C

The virt-builder and virt-install tools are part of the libguestfs suite
created by Red Hat. The virt-builder tool uses fully bootable and
pre-configured images as templates as the base for further
customization. This is faster than building from scratch (~3 minutes per
typical build), while significantly increasing the image size (~2x in
size vs from scratch).

The virt-install tool builds images using install media, while
leveraging the native installer for configuration. Customization
flexibility is similar to building from scratch. However, this is much
slower (~25 minutes per typical build).

Packer
======

-  Vendor: Hashicorp
-  Site: https://www.packer.io/
-  Supported host distributions: N/A (uses cloud system)
-  Supported target distributions: Fedora, CentOS/RHEL, (open)SUSE, Debian/Ubuntu
-  Definition format: JSON
-  Creates: cloud virtual machine disk images
-  Implemented in: Go

Packer is a tool made by Hashicorp as part of the Hashicorp system
management suite to create cloud virtual machines in OpenStack, AWS, or
GCP environments. Packer connects to a hypervisor and orchestrates it
with defined actions to customize an existing base image provided by a
distribution. Packer does not provide built-in customization
functionality, and relies on configuration management tools like
Ansible, Salt, Puppet, or Chef to customize the system. Thus Packer can
only produce cloud images, comparable in speed and size to virt-builder.

diskimage-builder
=================

-  Vendor: OpenStack
-  Site: https://docs.openstack.org/diskimage-builder/
-  Supported host distributions: Fedora, RHEL/CentOS, (open)SUSE, Debian/Ubuntu
-  Supported target distributions: Fedora, RHEL/CentOS, (open)SUSE, Debian/Ubuntu
-  Definition format: YAML
-  Creates: OpenStack cloud virtual machine disk images
-  Implemented in: Python

This tool was created by the OpenStack project for creating VM images
for OpenStack systems. It supports either customizing a base image or
building from scratch. Build times are similar to virt-builder and
virt-install depending on the image creation type. Advanced knowledge of
how a system is assembled is required to correctly configure an image.
This can only produce cloud images.

OSBuild / Image Builder
=======================

-  Vendor: Red Hat
-  Site: https://www.osbuild.org/
-  Supported host distributions: Fedora, RHEL/CentOS
-  Supported target distributions: Fedora, RHEL/CentOS
-  Definition format: TOML
-  Creates: cloud virtual machine disk images, container images, live ISOs, OSTrees
-  Implemented in: Python and Go

OSBuild is a tool used by Red Hat to power their Image Builder service.
It can produce disk images, container images, and live ISOs, as well as
bare file system trees for processing by other tools. OSBuild operates
on a philosophy of image building with a fully declarative configuration
of a pipeline of discrete tasks that are designed to limit/control
changes to the image to what is expected in the task. It only works on
Linux distributions that are directly programmed into the codebase.
Customizing the image build process is done by writing new code to
define more stages that can be invoked during an image build pipeline.
Typical builds take ~5 minutes.

Lorax
=====

-  Vendor: Red Hat
-  Site: https://weldr.io/
-  Supported host distributions: Fedora, RHEL/CentOS
-  Supported target distributions: Fedora, RHEL/CentOS
-  Definition format: Kickstart or TOML
-  Creates: filesystem trees, disk images, live/install ISOs, PXE images
-  Implemented in: Python

Lorax is the tool used by Red Hat and Fedora to produce installation and
live media. It can produce disk images, netboot, live, and install ISOs,
as well as bare file system trees for processing by other tools. It only
works on Linux distributions that use the Anaconda installer. Typical
builds take ~20 minutes.

ImageFactory
============

-  Vendor: Red Hat
-  Site: http://imgfac.org/
-  Supported host distributions: N/A (uses virtual machine infrastructure)
-  Supported target distributions: Fedora, RHEL/CentOS
-  Definition format: XML and Kickstart
-  Creates: virtual machine disk images
-  Implemented in: Python

ImageFactory is the tool used by Red Hat and Fedora to produce virtual
machine disk images. It works by leveraging virtual machine templates
from libguestfs to run an installation process to create images. Typical
builds take ~40 minutes. This tool is no longer maintained.

appliance-creator / livecd-creator
==================================

-  Vendor: Fedora Project
-  Site: https://github.com/livecd-tools
-  Supported host distributions: Fedora, RHEL/CentOS
-  Supported target distributions: Fedora, RHEL/CentOS
-  Definition format: Kickstart
-  Creates: filesystem tree, disk images, live ISOs
-  Implemented in: Python

The Fedora LiveCD Tools project provides a set of tools for creating and
modifying live media and appliance images. This is an alternative to the
Lorax tooling that does not use Anaconda. Instead, it uses the DNF
package manager directly. The build times are ~5-10 minutes shorter and
images are smaller. However, advanced understanding of the platform is
required for configuration.

RPM-OSTree
==========

-  Vendor: Red Hat
-  Site: https://rpm-ostree.readthedocs.io/
-  Supported host distributions: Fedora, RHEL/CentOS
-  Supported target distributions: Fedora, RHEL/CentOS
-  Definition format: JSON
-  Creates: filesystem OSTree images
-  Implemented in: C and Rust

Tooling to create OSTree images based on immutable layers, centered
around a different image building paradigm.

live-build
==========

-  Vendor: Debian Live / Ubuntu
-  Site: https://www.debian.org/devel/debian-live/
-  Supported host distributions: Debian, Ubuntu
-  Supported target distributions: Debian, Ubuntu
-  Definition format: Shell script
-  Creates: live ISOs
-  Implemented in: Shell

The live-build tool is a product of the Debian Live project to support
creating live media. It builds the system from scratch and uses shell
scripts to do customization of the image being built. The configuration
structure requires advanced knowledge of how a Debian or Ubuntu system
is assembled to properly configure it. Builds with this system are
somewhat slower than with similar processes to build from scratch
(~15-20 minutes per build) because of how the construction process is
broken up into stages that run some actions in a less than optimal way.
This tool can only be used to create ISO images. The Ubuntu version is a
fork that behaves differently from the Debian version, so they are not
interchangeable.

vmdebootstrap / vmdb2
=====================

-  Developer: Lars Wirzenius
-  Site: https://liw.fi/vmdebootstrap/
-  Supported host distributions: Debian, Ubuntu
-  Supported target distributions: Debian, Ubuntu
-  Definition format: YAML with Shell scripts
-  Creates: virtual machine disk images
-  Implemented in: Python and Shell

This tool is a thin wrapper around debootstrap to produce a disk image
for virtual machines. It requires advanced understanding of how a Debian
system is assembled to configure correctly. Build times are similar to
live-build. This project was recently abandoned for a rewrite called
“vmdb2” that operates similarly.
