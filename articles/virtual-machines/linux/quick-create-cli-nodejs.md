---
title: Azure 클래식 CLI를 사용하여 Linux VM 만들기 | Microsoft Docs
description: Azure 클래식 CLI를 사용하여 Azure에서 Linux VM 만들기
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: facb1115-2b4e-4ef3-9905-330e42beb686
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2016
ms.author: v-livech
ms.openlocfilehash: 569e90c7908ce435689a80f7917b20275703f537
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473742"
---
# <a name="create-a-linux-vm-using-the-azure-classic-cli"></a>Azure 클래식 CLI를 사용하여 Linux VM 만들기

이 문서에서는 Azure 명령줄 인터페이스(CLI)의 `azure vm quick-create` 명령을 사용하여 Azure에서 Linux 가상 머신(VM)를 신속하게 배포하는 방법을 보여 줍니다. `quick-create` 명령은 개념을 신속하게 프로토타입하거나 테스트하는 데 사용할 수 있는 기본 인프라 내에 VM을 배포합니다.

> [!NOTE]
> Azure CLI를 사용하여 VM을 만들려면 [Azure CLI를 사용하여 VM 만들기](../windows/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

[Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 사용하여 Linux VM을 신속히 배포할 수도 있습니다.

이 문서에는 [SSH 공용 및 개인 키 파일](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)이 필요합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="quick-commands"></a>빠른 명령

다음 예제에서는 CoreOS VM을 배포하고 SSH(Secure Shell)를 연결하는 방법을 보여줍니다(사용자의 인수는 다를 수 있음).

```azurecli
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>자세한 연습

다음 연습에는 단계별로 각 단계에서 수행할 작업을 설명하면서 배포되는 UbuntuLTS VM이 있습니다.

## <a name="vm-quick-create-aliases"></a>VM quick-create 별칭

배포를 선택하는 빠른 방법은 가장 일반적인 OS 배포에 매핑된 Azure CLI 별칭을 사용하는 것입니다. 다음 테이블에 별칭이 나열되어 있습니다(Azure CLI 버전 0.10 현재). `quick-create`을 사용하는 모든 배포는 기본적으로 더 빠른 프로비전 및 고성능 디스크 액세스를 제공하는 SSD(반도체 드라이브) 저장소에서 지원되는 VM에 대해 이루어집니다. (이러한 별칭은 Azure에 사용할 수 있는 배포의 작은 부분을 나타냅니다. [웹에서](https://azure.microsoft.com/marketplace/virtual-machines/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) [PowerShell을 통해 이미지를 검색](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)하여 Azure Marketplace에 있는 이미지를 더 많이 찾거나 [고유의 사용자 지정 이미지를 업로드합니다](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).)

| Alias | 게시자 | 제안 | SKU | Version |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7.2 |최신 |
| CoreOS |CoreOS |CoreOS |Stable |최신 |
| Debian |credativ |Debian |8 |최신 |
| openSUSE |SUSE |openSUSE |13.2 |최신 |
| RHEL |Red Hat |RHEL |7.2 |최신 |
| UbuntuLTS |Canonical |Ubuntu Server |14.04.4-LTS |최신 |

다음 섹션에서는 **ImageURN** 옵션(`-Q`)에 `UbuntuLTS` 별칭을 사용하여 Ubuntu 14.04.4 LTS Server를 배포합니다.

이전 `quick-create` 예제에서는 SSH 암호를 비활성화하는 동안 업로드할 SSH 공개 키를 식별하기 위해 `-M` 플래그만 호출했으므로, 다음 인수를 묻는 메시지가 표시되었습니다.

* 리소스 그룹 이름(일반적으로 첫 번째 Azure 리소스 그룹에 대해서는 어떤 문자열이든 적합함).
* VM 이름
* 위치(좋은 기본값은 `westus` 또는 `westeurope`입니다.)
* linux(원하는 OS가 무엇인지 Azure에 알림)
* username

다음 예제는 추가 메시지 표시가 필요하지 않도록 모든 값을 지정합니다. `~/.ssh/id_rsa.pub`을 ssh-rsa 형식의 공개 키 파일로 포함하고 있으면 해당 파일은 있는 그대로 작동합니다.

```azurecli
azure vm quick-create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --admin-username myAdminUser \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --image-urn UbuntuLTS
```

출력에는 다음과 같은 출력 블록이 있습니다.

```azurecli
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>새 VM에 로그인
출력에 나열된 공용 IP 주소를 사용하여 VM에 로그인합니다. 나열된 FQDN(정규화된 도메인 이름)을 사용할 수도 있습니다.

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

로그인 프로세스는 다음 출력 블록과 유사하게 표시됩니다.

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>다음 단계
`azure vm quick-create` 명령은 Bash 셸에 로그인하고 작업할 수 있도록 VM을 신속하게 배포하는 방법입니다. 그러나 `vm quick-create` 를 사용하면 광범위한 제어가 불가능하며 더 복잡한 환경을 만들 수도 없습니다.  이러한 문서 중 하나를 수행하여 인프라에 대해 사용자 지정된 Linux VM을 배포할 수 있습니다.

* [Azure CLI 명령을 직접 사용하여 Linux VM에 대한 고유한 사용자 지정 환경 만들기](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [템플릿을 사용하여 Azure에서 SSH 보안 Linux VM 만들기](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[다양한 명령으로 `docker-machine` Azure 드라이버를 사용하여 Linux VM을 Docker 호스트로 신속하게 만들 수 있습니다](docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
