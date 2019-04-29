---
title: Red Hat Enterprise Linux VHD 만들기 및 Azure에서 사용하도록 업로드 | Microsoft Docs
description: RedHat Linux 운영 체제가 포함된 Azure VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: szark
ms.openlocfilehash: 01acdf23c3113c3c4d185263b5cab75f3efd34a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771178"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Azure용 RedHat 기반 가상 머신 준비
이 문서에서는 Azure용 RHEL(Red Hat Enterprise Linux) 가상 머신을 준비하는 방법을 알아봅니다. 이 문서에 설명되어 있는 RHEL의 버전은 6.7+ 및 7.1+입니다. 이 문서에서 다룰 준비에 대한 하이퍼바이저는 Hyper-V, KVM(커널 기반 가상 머신) 및 VMware입니다. Red Hat 클라우드 액세스 프로그램에 참여하기 위한 자격 요구 사항에 대한 자세한 내용은 [Red Hat 클라우드 액세스 웹 사이트](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 및 [Azure에서 실행 중인 RHEL](https://access.redhat.com/ecosystem/ccsp/microsoft-azure)을 참조하세요.

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Hyper-V 관리자에서 Red Hat 기반 가상 머신 준비

### <a name="prerequisites"></a>필수 조건
이 섹션은 RedHat 웹 사이트에서 ISO 파일을 확보했으며 VHD(가상 하드 디스크)에 RHEL 이미지를 이미 설치한 것으로 가정합니다. Hyper-V 관리자를 사용하여 운영 체제 이미지를 설치하는 방법에 대한 자세한 내용은 [Hyper-V 역할 설치 및 Virtual Machine 구성](https://technet.microsoft.com/library/hh846766.aspx)을 참조하세요.

**RHEL 설치 참고 사항**

* Azure는 VHDX 형식을 지원하지 않습니다. Azure는 고정 VHD만 지원합니다. Hyper-V 관리자를 사용하여 디스크를 VHD 형식으로 변환하거나, convert-vhd cmdlet을 사용할 수 있습니다. VirtualBox를 사용하는 경우 디스크를 만들 때 기본 동적 할다 옵션과 달리 **고정 크기**를 선택합니다.
* Azure에서는 1세대 가상 머신만 지원합니다. VHDX에서 VHD 파일 형식으로, 동적 확장에서 고정된 크기의 디스크로 1세대 가상 컴퓨터를 변환할 수 있습니다. 가상 머신의 세대는 변경할 수 없습니다. 자세한 내용은 [Hyper-V에 1 또는 2세대 가상 머신을 만들어야 하나요?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)를 참조하세요.
* VHD에 허용되는 최대 크기는 1,023GB입니다.
* LVM(논리 볼륨 관리자)이 지원되며 Azure 가상 머신의 OS 디스크 또는 데이터 디스크에 사용할 수 있습니다. 그러나 일반적으로 OS 디스크에서 LVM이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이 방법은 특히 문제 해결을 위해 운영 체제 디스크를 다른 동일한 가상 머신에 연결해야 하는 경우, 복제된 가상 머신과 LVM 이름이 충돌하는 것을 방지합니다. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 및 [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 설명서도 살펴보세요.
* UDF(범용 디스크 형식) 파일 시스템을 탑재하기 위한 커널 지원이 필요합니다. Azure에서 처음 부팅 시 게스트에 연결된 UDF 형식 미디어는 프로비저닝 구성을 Linux 가상 머신에 전달합니다. Azure Linux 에이전트는 해당 구성을 읽고 가상 머신을 프로비전하기 위해 UDF 파일 시스템을 탑재할 수 있어야 합니다.
* 운영 체제 디스크에서는 스왑 파티션을 구성하지 마세요. 임시 리소스 디스크에서 스왑 파일을 만들도록 Linux 에이전트를 구성할 수 있습니다.  여기에 대한 자세한 내용은 다음 단계에서 확인할 수 있습니다.
* Azure의 모든 VHD는 가상 크기가 1MB 단위로 조정되어야 합니다. 원시 디스크에서 VHD로 변환할 때 변환하기 전에 원시 디스크 크기가 1MB의 배수인지 확인해야 합니다. 자세한 내용은 아래 단계에서 찾을 수 있습니다. 자세한 내용은 [Linux 설치 참고 사항](create-upload-generic.md#general-linux-installation-notes)을 참조하세요.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Hyper-V 관리자에서 RHEL 6 가상 머신 준비

1. Hyper-V 관리자에서 가상 머신을 선택합니다.

1. **연결** 을 클릭하여 가상 머신의 콘솔 창을 엽니다.

1. RHEL 6에서 NetworkManager는 Azure Linux 에이전트 작동을 방해할 수 있습니다. 다음 명령을 실행하여 이 패키지를 제거합니다.
   
        # sudo rpm -e --nodeps NetworkManager

1. 파일 `/etc/sysconfig/network`를 만들거나 편집하고 다음 텍스트를 추가합니다.
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. 파일 `/etc/sysconfig/network-scripts/ifcfg-eth0`를 만들거나 편집하고 다음 텍스트를 추가합니다.
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. 이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 이동(또는 제거)합니다. 이러한 규칙은 Microsoft Azure 또는 Hyper-V에서 가상 머신을 복제하는 경우 문제를 발생시킵니다.

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. 다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

        # sudo chkconfig network on

1. RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. WALinuxAgent 패키지 `WALinuxAgent-<version>`은 Red Hat 기타 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 기타 리포지토리를 사용합니다.

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이 수정 작업을 수행하려면 `/boot/grub/menu.lst`를 텍스트 편집기에서 열고 기본 커널이 다음 매개 변수를 포함하는지 확인합니다.
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다.
    
    그 밖에 다음 매개 변수를 제거하는 것이 좋습니다.
    
        rhgb quiet crashkernel=auto
    
    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다.  원할 경우 `crashkernel` 옵션은 구성된 상태로 둘 수 있습니다. 이 매개 변수는 가상 컴퓨터의 사용 가능한 메모리 양을 128MB 이상 줄입니다. 이 구성은 좀 더 작은 가상 컴퓨터 크기에서는 문제가 될 수도 있습니다.


1. SSH(보안 셸) 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. 이것이 일반적으로 기본값입니다. 다음 줄을 포함하도록 /etc/ssh/sshd_config를 수정합니다.

        ClientAliveInterval 180

1. 다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    WALinuxAgent 패키지를 설치하면 3단계에서 NetworkManager 및 NetworkManager-gnome 패키지를 아직 제거하지 않은 경우 이러한 패키지를 제거합니다.

1. 운영 체제 디스크에 스왑 공간을 만들지 마세요.

    Azure Linux 에이전트는 Azure에서 가상 컴퓨터를 프로비전한 후에 가상 컴퓨터에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크이며 가상 머신의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후에(이전 단계 참조) /etc/waagent.conf에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. 다음 명령을 실행하여 (필요한 경우) 구독에 대한 등록을 해제합니다.

        # sudo subscription-manager unregister

1. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Hyper-V 관리자에서 **작업** > **종료**를 클릭합니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Hyper-V 관리자에서 RHEL 7 가상 머신 준비

1. Hyper-V 관리자에서 가상 머신을 선택합니다.

1. **연결** 을 클릭하여 가상 머신의 콘솔 창을 엽니다.

1. 파일 `/etc/sysconfig/network`를 만들거나 편집하고 다음 텍스트를 추가합니다.
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. 파일 `/etc/sysconfig/network-scripts/ifcfg-eth0`를 만들거나 편집하고 다음 텍스트를 추가합니다.
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. 다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

        # sudo systemctl enable network

1. RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이렇게 수정하려면 텍스트 편집기에서 `/etc/default/grub`를 열고 `GRUB_CMDLINE_LINUX` 매개 변수를 편집합니다. 예를 들면 다음과 같습니다.
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 이 구성은 NIC에 대한 새 RHEL 7 명명 규칙도 해제합니다. 그 밖에 다음 매개 변수를 제거하는 것이 좋습니다.
   
        rhgb quiet crashkernel=auto
   
    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다. 원할 경우 `crashkernel` 옵션은 구성된 상태로 둘 수 있습니다. 이 매개 변수는 가상 컴퓨터의 사용 가능한 메모리 양을 128MB 이상 줄입니다. 이 방식은 좀 더 작은 가상 컴퓨터 크기에서는 문제가 될 수도 있습니다.

1. `/etc/default/grub`편집을 완료한 후에 다음 명령을 실행하여 grub 구성을 다시 빌드합니다.

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. 이것이 일반적으로 기본값입니다. 다음 줄을 포함하도록 `/etc/ssh/sshd_config` 을 수정합니다.

        ClientAliveInterval 180

1. WALinuxAgent 패키지 `WALinuxAgent-<version>`은 Red Hat 기타 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 기타 리포지토리를 사용합니다.

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. 다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. 운영 체제 디스크에 스왑 공간을 만들지 마세요.

    Azure Linux 에이전트는 Azure에서 가상 컴퓨터를 프로비전한 후에 가상 컴퓨터에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크이며 가상 머신의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후에(이전 단계 참조) `/etc/waagent.conf`에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. 구독에 대한 등록을 해제하려면 다음 명령을 실행합니다.

        # sudo subscription-manager unregister

1. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Hyper-V 관리자에서 **작업** > **종료**를 클릭합니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>KVM에서 RedHat 기반 가상 컴퓨터 준비
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>KVM에서 RHEL 6 가상 머신 준비

1. Red Hat 웹 사이트에서 RHEL 6의 KVM 이미지를 다운로드합니다.

1. 루트 암호를 설정합니다.

    암호화된 암호를 생성하고 명령 출력을 복사합니다.

        # openssl passwd -1 changeme

    guestfish 루트 암호를 설정합니다.
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   루트 사용자의 두 번째 필드를 “!!”에서 암호화된 암호로 변경합니다.

1. qcow2 이미지에서 KVM에 가상 머신을 만듭니다. 디스크 형식을 **qcow2**로 설정하고 가상 네트워크 인터페이스 디바이스 모델을 **virtio**로 설정합니다. 그 후 가상 머신을 시작하고 루트로 로그인합니다.

1. 파일 `/etc/sysconfig/network`를 만들거나 편집하고 다음 텍스트를 추가합니다.
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. 파일 `/etc/sysconfig/network-scripts/ifcfg-eth0`를 만들거나 편집하고 다음 텍스트를 추가합니다.
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. 이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 이동(또는 제거)합니다. 이러한 규칙은 Azure 또는 Hyper-V에서 가상 머신을 복제하는 경우 문제를 발생시킵니다.

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. 다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

        # chkconfig network on

1. RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이 구성을 수행하려면 `/boot/grub/menu.lst`를 텍스트 편집기에서 열고 기본 커널이 다음 매개 변수를 포함하는지 확인합니다.
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다.
    
    그 밖에 다음 매개 변수를 제거하는 것이 좋습니다.
    
        rhgb quiet crashkernel=auto
    
    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다. 원할 경우 `crashkernel` 옵션은 구성된 상태로 둘 수 있습니다. 이 매개 변수는 가상 컴퓨터의 사용 가능한 메모리 양을 128MB 이상 줄입니다. 이 방식은 좀 더 작은 가상 컴퓨터 크기에서는 문제가 될 수도 있습니다.


1. Hyper-V 모듈을 initramfs에 추가합니다.  

    `/etc/dracut.conf`를 편집하고 다음 내용을 추가합니다.

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Initramfs를 다시 빌드합니다.

        # dracut -f -v

1. Cloud-Init을 제거합니다.

        # yum remove cloud-init

1. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.

        # chkconfig sshd on

    다음 줄을 포함하도록 /etc/ssh/sshd_config를 수정합니다.

        PasswordAuthentication yes
        ClientAliveInterval 180

1. WALinuxAgent 패키지 `WALinuxAgent-<version>`은 Red Hat 기타 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 기타 리포지토리를 사용합니다.

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. 다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

        # yum install WALinuxAgent

        # chkconfig waagent on

1. Azure Linux 에이전트는 Azure에서 가상 머신을 프로비전한 후에 가상 머신에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크이며 가상 컴퓨터의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후에(이전 단계 참조) **/etc/waagent.conf**에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. 다음 명령을 실행하여 (필요한 경우) 구독에 대한 등록을 해제합니다.

        # subscription-manager unregister

1. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. KVM의 가상 머신을 종료합니다.

1. qcow2 이미지를 VHD 형식으로 변환합니다.

> [!NOTE]
> VHD 형식이 잘못 지정되는 qemu-img 버전 >=2.2.1에 알려진 버그가 있습니다. 이 문제는 QEMU 2.6에서 해결되었습니다. qemu-img 2.2.0 이하 버전을 사용하거나 2.6 이상으로 업데이트하는 것이 좋습니다. 참조: https://bugs.launchpad.net/qemu/+bug/1490611
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>KVM에서 RHEL 7 가상 머신 준비

1. Red Hat 웹 사이트에서 RHEL 7의 KVM 이미지를 다운로드합니다. 이 절차에서는 RHEL 7을 예제로 사용합니다.

1. 루트 암호를 설정합니다.

    암호화된 암호를 생성하고 명령 출력을 복사합니다.

        # openssl passwd -1 changeme

    guestfish 루트 암호를 설정합니다.

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   루트 사용자의 두 번째 필드를 “!!”에서 암호화된 암호로 변경합니다.

1. qcow2 이미지에서 KVM에 가상 머신을 만듭니다. 디스크 형식을 **qcow2**로 설정하고 가상 네트워크 인터페이스 디바이스 모델을 **virtio**로 설정합니다. 그 후 가상 머신을 시작하고 루트로 로그인합니다.

1. 파일 `/etc/sysconfig/network`를 만들거나 편집하고 다음 텍스트를 추가합니다.
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. 파일 `/etc/sysconfig/network-scripts/ifcfg-eth0`를 만들거나 편집하고 다음 텍스트를 추가합니다.
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. 다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

        # sudo systemctl enable network

1. RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이렇게 구성하려면 텍스트 편집기에서 `/etc/default/grub`를 열고 `GRUB_CMDLINE_LINUX` 매개 변수를 편집합니다. 예를 들면 다음과 같습니다.
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   이 명령은 모든 콘솔 메시지를 첫 번째 직렬 포트로 전송하므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 이 명령은 NIC에 대한 새 RHEL 7 명명 규칙도 해제합니다. 그 밖에 다음 매개 변수를 제거하는 것이 좋습니다.
   
        rhgb quiet crashkernel=auto
   
    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다. 원할 경우 `crashkernel` 옵션은 구성된 상태로 둘 수 있습니다. 이 매개 변수는 가상 컴퓨터의 사용 가능한 메모리 양을 128MB 이상 줄입니다. 이 방식은 좀 더 작은 가상 컴퓨터 크기에서는 문제가 될 수도 있습니다.

1. `/etc/default/grub`편집을 완료한 후에 다음 명령을 실행하여 grub 구성을 다시 빌드합니다.

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Hyper-V 모듈을 initramfs에 추가합니다.

    `/etc/dracut.conf` 을 편집하고 콘텐츠를 추가합니다.

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Initramfs를 다시 빌드합니다.

        # dracut -f -v

1. Cloud-Init을 제거합니다.

        # yum remove cloud-init

1. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.

        # systemctl enable sshd

    다음 줄을 포함하도록 /etc/ssh/sshd_config를 수정합니다.

        PasswordAuthentication yes
        ClientAliveInterval 180

1. WALinuxAgent 패키지 `WALinuxAgent-<version>`은 Red Hat 기타 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 기타 리포지토리를 사용합니다.

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. 다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

        # yum install WALinuxAgent

    waagent 서비스를 사용하도록 설정합니다.

        # systemctl enable waagent.service

1. 운영 체제 디스크에 스왑 공간을 만들지 마세요.

    Azure Linux 에이전트는 Azure에서 가상 컴퓨터를 프로비전한 후에 가상 컴퓨터에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크이며 가상 머신의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후에(이전 단계 참조) `/etc/waagent.conf`에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. 다음 명령을 실행하여 (필요한 경우) 구독에 대한 등록을 해제합니다.

        # subscription-manager unregister

1. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. KVM의 가상 머신을 종료합니다.

1. qcow2 이미지를 VHD 형식으로 변환합니다.

> [!NOTE]
> VHD 형식이 잘못 지정되는 qemu-img 버전 >=2.2.1에 알려진 버그가 있습니다. 이 문제는 QEMU 2.6에서 해결되었습니다. qemu-img 2.2.0 이하 버전을 사용하거나 2.6 이상으로 업데이트하는 것이 좋습니다. 참조: https://bugs.launchpad.net/qemu/+bug/1490611
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>VMware에서 RedHat 기반 가상 머신 준비
### <a name="prerequisites"></a>필수 조건
이 섹션은 VMWare에 RHEL 가상 머신이 이미 설치되어 있다고 가정합니다. VMWare에서 운영 체제를 설치하는 자세한 방법은 [VMWare 게스트 운영 체제 설치 가이드](https://partnerweb.vmware.com/GOSIG/home.html)를 참조하세요.

* Linux 운영 체제를 설치하는 경우 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 좋습니다. 이 방법은 특히 문제 해결을 위해 운영 체제 디스크를 다른 가상 머신에 연결해야 하는 경우, 복제된 가상 머신과 LVM 이름이 충돌하는 것을 방지합니다. 원하는 경우에는 데이터 디스크에서 LVM 또는 RAID를 사용할 수 있습니다.
* 운영 체제 디스크에서는 스왑 파티션을 구성하지 마세요. Linux 에이전트를 구성하여 임시 리소스 디스크에서 스왑 파일을 만들 수 있습니다. 여기에 대한 자세한 내용은 아래 단계에서 찾을 수 있습니다.
* 가상 하드 디스크를 만들 때 **가상 디스크를 단일 파일로 저장**을 선택합니다.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>VMWare에서 RHEL 6 가상 머신 준비
1. RHEL 6에서 NetworkManager는 Azure Linux 에이전트 작동을 방해할 수 있습니다. 다음 명령을 실행하여 이 패키지를 제거합니다.
   
        # sudo rpm -e --nodeps NetworkManager

1. 다음 텍스트가 포함된 **network** 파일을 /etc/sysconfig/ 디렉터리에 만듭니다.

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. 파일 `/etc/sysconfig/network-scripts/ifcfg-eth0`를 만들거나 편집하고 다음 텍스트를 추가합니다.
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. 이더넷 인터페이스에 대한 정적 규칙을 생성하지 않도록 방지하는 udev 규칙을 이동(또는 제거)합니다. 이러한 규칙은 Azure 또는 Hyper-V에서 가상 머신을 복제하는 경우 문제를 발생시킵니다.

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. 다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

        # sudo chkconfig network on

1. RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. WALinuxAgent 패키지 `WALinuxAgent-<version>`은 Red Hat 기타 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 기타 리포지토리를 사용합니다.

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이렇게 하려면 텍스트 편집기에서 `/etc/default/grub`를 열고 `GRUB_CMDLINE_LINUX` 매개 변수를 편집합니다. 예를 들면 다음과 같습니다.
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   이렇게 하면 모든 콘솔 메시지가 첫 번째 직렬 포트로 전송되므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. 그 밖에 다음 매개 변수를 제거하는 것이 좋습니다.
   
        rhgb quiet crashkernel=auto
   
    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다. 원할 경우 `crashkernel` 옵션은 구성된 상태로 둘 수 있습니다. 이 매개 변수는 가상 컴퓨터의 사용 가능한 메모리 양을 128MB 이상 줄입니다. 이 방식은 좀 더 작은 가상 컴퓨터 크기에서는 문제가 될 수도 있습니다.

1. Hyper-V 모듈을 initramfs에 추가합니다.

    `/etc/dracut.conf`를 편집하고 다음 내용을 추가합니다.

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Initramfs를 다시 빌드합니다.

        # dracut -f -v

1. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. 이것이 일반적으로 기본값입니다. 다음 줄을 포함하도록 `/etc/ssh/sshd_config` 을 수정합니다.

    ClientAliveInterval 180

1. 다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. 운영 체제 디스크에 스왑 공간을 만들지 마세요.

    Azure Linux 에이전트는 Azure에서 가상 컴퓨터를 프로비전한 후에 가상 컴퓨터에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크이며 가상 머신의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후에(이전 단계 참조) `/etc/waagent.conf`에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. 다음 명령을 실행하여 (필요한 경우) 구독에 대한 등록을 해제합니다.

        # sudo subscription-manager unregister

1. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. 가상 머신을 종료하고 VMDK 파일을 .vhd 파일로 변환합니다.

> [!NOTE]
> VHD 형식이 잘못 지정되는 qemu-img 버전 >=2.2.1에 알려진 버그가 있습니다. 이 문제는 QEMU 2.6에서 해결되었습니다. qemu-img 2.2.0 이하 버전을 사용하거나 2.6 이상으로 업데이트하는 것이 좋습니다. 참조: https://bugs.launchpad.net/qemu/+bug/1490611
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>VMWare에서 RHEL 7 가상 머신 준비
1. 파일 `/etc/sysconfig/network`를 만들거나 편집하고 다음 텍스트를 추가합니다.
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. 파일 `/etc/sysconfig/network-scripts/ifcfg-eth0`를 만들거나 편집하고 다음 텍스트를 추가합니다.
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. 다음 명령을 실행하여 부팅 시 네트워크 서비스가 시작되도록 합니다.

        # sudo systemctl enable network

1. RHEL 리포지토리에서 패키지 설치를 사용하도록 다음 명령을 실행하여 Red Hat 구독을 등록합니다.

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Azure용 커널 매개 변수를 추가로 포함하려면 grub 구성에서 커널 부팅 줄을 수정합니다. 이렇게 수정하려면 텍스트 편집기에서 `/etc/default/grub`를 열고 `GRUB_CMDLINE_LINUX` 매개 변수를 편집합니다. 예를 들면 다음과 같습니다.
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   이 구성은 모든 콘솔 메시지를 첫 번째 직렬 포트로 전송하므로 Azure 지원에서 문제를 디버깅하는 데에도 도움이 될 수 있습니다. NIC에 대한 새 RHEL 7 명명 규칙도 해제합니다. 그 밖에 다음 매개 변수를 제거하는 것이 좋습니다.
   
        rhgb quiet crashkernel=auto
   
    모든 로그를 직렬 포트로 보내려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 효율적이지 않습니다. 원할 경우 `crashkernel` 옵션은 구성된 상태로 둘 수 있습니다. 이 매개 변수는 가상 컴퓨터의 사용 가능한 메모리 양을 128MB 이상 줄입니다. 이 방식은 좀 더 작은 가상 컴퓨터 크기에서는 문제가 될 수도 있습니다.

1. `/etc/default/grub`편집을 완료한 후에 다음 명령을 실행하여 grub 구성을 다시 빌드합니다.

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Hyper-V 모듈을 initramfs에 추가합니다.

    `/etc/dracut.conf`를 편집하고 콘텐츠를 추가합니다.

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Initramfs를 다시 빌드합니다.

        # dracut -f -v

1. SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다. 이 설정이 일반적으로 기본값입니다. 다음 줄을 포함하도록 `/etc/ssh/sshd_config` 을 수정합니다.

        ClientAliveInterval 180

1. WALinuxAgent 패키지 `WALinuxAgent-<version>`은 Red Hat 기타 리포지토리에 푸시되었습니다. 다음 명령을 실행하여 기타 리포지토리를 사용합니다.

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. 다음 명령을 실행하여 Azure Linux 에이전트를 설치합니다.

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. 운영 체제 디스크에 스왑 공간을 만들지 마세요.

    Azure Linux 에이전트는 Azure에서 가상 컴퓨터를 프로비전한 후에 가상 컴퓨터에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 임시 디스크이며 가상 머신의 프로비전을 해제할 때 비워질 수 있습니다. Azure Linux 에이전트를 설치한 후에(이전 단계 참조) `/etc/waagent.conf`에서 다음 매개 변수를 적절하게 수정합니다.

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. 구독에 대한 등록을 해제하려면 다음 명령을 실행합니다.

        # sudo subscription-manager unregister

1. 다음 명령을 실행하여 가상 머신의 프로비전을 해제하고 Azure에서 프로비전할 준비를 합니다.

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. 가상 머신을 종료하고 VMDK 파일을 VHD 형식으로 변환합니다.

> [!NOTE]
> VHD 형식이 잘못 지정되는 qemu-img 버전 >=2.2.1에 알려진 버그가 있습니다. 이 문제는 QEMU 2.6에서 해결되었습니다. qemu-img 2.2.0 이하 버전을 사용하거나 2.6 이상으로 업데이트하는 것이 좋습니다. 참조: https://bugs.launchpad.net/qemu/+bug/1490611
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>자동으로 kickstart 파일을 사용하여 ISO에서 Red Hat 기반 가상 머신 준비
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>kickstart 파일에서 RHEL 7 가상 머신 준비

1.  다음 콘텐츠를 포함하는 kickstart 파일을 만들고 저장합니다. Kickstart 설치에 대한 자세한 내용은 [Kickstart 설치 가이드](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)를 참조하세요.

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

1. 설치 시스템에서 액세스할 수 있는 위치에 kickstart 파일을 배치합니다.

1. Hyper-V 관리자에서 새 가상 머신을 만듭니다. **가상 하드 디스크 연결** 페이지에서 **나중에 가상 하드 디스크 연결**을 선택하고 새 Virtual Machine 마법사를 완료합니다.

1. 가상 머신 설정을 엽니다.

    a.  새 가상 하드 디스크를 가상 머신에 연결합니다. **VHD 형식** 및 **고정된 크기**를 선택하도록 합니다.

    b.  설치 ISO를 DVD 드라이브에 연결합니다.

    다.  CD에서 부팅하도록 BIOS를 설정합니다.

1. 가상 머신을 시작합니다. 설치 가이드가 나타나면 **Tab** 키를 눌러서 부팅 옵션을 구성합니다.

1. 부팅 옵션 마지막에 `inst.ks=<the location of the kickstart file>` 을 입력하고 **Enter**키를 누릅니다.

1. 설치가 완료될 때까지 기다립니다. 완료되면 가상 머신이 자동으로 종료됩니다. 이제 Linux VHD를 Azure에 업로드할 수 있습니다.

## <a name="known-issues"></a>알려진 문제
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Hyper-V 드라이버가 Hyper-V 하이퍼바이저가 아닌 프로그램을 사용하는 경우 초기 RAM 디스크에 포함될 수 없습니다.

경우에 따라 Linux 설치 관리자는 Hyper-V 환경에서 실행 중임을 감지하지 않는 한 초기 RAM 디스크(initrd 또는 initramfs)에 Hyper-V용 드라이버를 포함하지 않을 수 있습니다.

다른 가상화 시스템(예: Virtualbox, Xen 등)을 사용하여 Linux 이미지를 준비할 경우 초기 RAM 디스크에서 최소한 hv_vmbus 및 hv_storvsc 커널 모듈을 사용할 수 있도록 initrd를 다시 작성해야 할 수 있습니다. 이는 적어도 업스트림 Red Hat 배포를 기반으로 하는 시스템의 알려진 문제입니다.

이 문제를 해결하려면 Hyper-V 모듈을 initramfs에 추가하고 다시 빌드합니다.

`/etc/dracut.conf`를 편집하고 다음 내용을 추가합니다.

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Initramfs를 다시 빌드합니다.

        # dracut -f -v

자세한 정보는 [initramfs 다시 작성](https://access.redhat.com/solutions/1958)에 대한 내용을 참조하세요.

## <a name="next-steps"></a>다음 단계
이제 Red Hat Enterprise Linux 가상 하드 디스크를 사용하여 Azure에 새 가상 머신을 만들 준비가 되었습니다. .vhd 파일을 Azure에 처음 업로드하는 경우 [사용자 지정 디스크에서 Linux VM 만들기](upload-vhd.md#option-1-upload-a-vhd)를 참조하세요.

Red Hat Enterprise Linux를 실행하기 위해 인증된 하이퍼바이저에 대한 자세한 내용은 [Red Hat 웹 사이트](https://access.redhat.com/certified-hypervisors)를 참조하세요.
