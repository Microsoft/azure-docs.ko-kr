---
title: Azure에서 Linux VM용 SSH 키 쌍 만들기 및 사용 | Microsoft Docs
description: Azure에서 인증 프로세스의 보안을 개선하기 위해 Linux VM용 SSH 공용-개인 키를 만들고 사용하는 방법입니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/11/2018
ms.author: cynthn
ms.openlocfilehash: d442d09c8c8ded3aa50faf74e28c8d95ded24a5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614414"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>빠른 단계: Azure에서 Linux VM용 SSH 공개-개인 키 쌍 만들기 및 사용

SSH(보안 셸) 키 쌍을 사용하면 인증을 위해 SSH 키를 사용하는 VM(가상 머신)을 Azure에서 만들 수 있으므로 로그인할 때 암호가 필요하지 않습니다. 이 문서에서는 Linux VM용 SSH 공개-개인 키 파일 쌍을 신속하게 생성하고 사용하는 방법을 보여줍니다. Linux에서 Azure Cloud Shell, macOS 또는 Linux 호스트, Windows 하위 시스템 및 OpenSSH를 지원하는 기타 도구를 사용하여 이러한 단계를 완료할 수 있습니다. 

> [!NOTE]
> SSH 키를 사용하여 만든 VM은 기본적으로 암호가 비활성화된 상태로 구성되므로 무차별 추측 공격의 어려움이 크게 증가합니다. 

자세한 배경 및 예제는 [SSH 키 쌍을 만드는 자세한 단계](create-ssh-keys-detailed.md)를 참조하세요.

Windows 컴퓨터에서 SSH 키를 생성하고 사용하는 추가적인 방법은 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](ssh-from-windows.md)을 참조합니다.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>SSH 키 쌍 만들기

`ssh-keygen` 명령을 사용하여 SSH 공용 및 개인 키 파일을 생성합니다. 기본적으로 이러한 파일은 ~/.ssh 디렉터리에 만들어집니다. 다른 위치와 선택적 암호(*암호*)를 지정하여 개인 키 파일에 액세스 할 수 있습니다. 이름 같은 SSH 키 쌍이 주어진 위치에 있으면 해당 파일이 덮어쓰여집니다.

다음 명령은 RSA 암호화 및 2048 비트 길이를 사용하여 SSH 키 쌍을 만듭니다.

```bash
ssh-keygen -t rsa -b 2048
```

[Azure CLI](/cli/azure)를 사용하여 [az vm create](/cli/azure/vm#az-vm-create) 명령으로 VM을 만드는 경우 `--generate-ssh-keys` 옵션을 사용하여 SSH 공개 및 개인 키 파일을 선택적으로 생성할 수 있습니다. 키 파일은 `--ssh-dest-key-path` 옵션으로 달리 지정하지 않는 한 ~/.ssh 디렉터리에 저장됩니다. `--generate-ssh-keys` 옵션은 기존 키 파일을 덮어쓰지 않습니다. 그 대신 오류를 반환합니다. 다음 명령에서 *VMname*과 *RGname*을 자신의 값으로 바꿉니다.

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>VM을 배포하는 경우 SSH 공개 키 제공

인증을 위해 SSH 키를 사용하는 Linux VM을 만들려면 Azure Portal, Azure CLI, Azure Resource Manager 템플릿 또는 기타 방법을 사용하여 VM을 만들 때 SSH 공개 키를 지정합니다.

* [Azure Portal을 사용하여 Linux 가상 머신 만들기](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI를 사용하여 Linux 가상 머신 만들기](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure 템플릿을 사용하여 Linux VM 만들기](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

SSH 공개 키 형식에 익숙하지 않은 경우, 다음 `cat` 명령을 사용하고 필요한 경우 `~/.ssh/id_rsa.pub`을 자신의 공개 키 파일의 경로와 파일 이름으로 바꿔서 자신의 공개 키를 표시할 수 있습니다.

```bash
cat ~/.ssh/id_rsa.pub
```

일반적인 공개 키 값의 예는 다음과 같습니다.

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Azure Portal 또는 Resource Manager 템플릿에서 사용할 공개 키 파일의 내용을 복사하여 붙여넣으려는 경우 후행 공백은 복사하지 않아야 합니다. macOS에서 공개 키를 복사하려면 공개 키 파일을 **pbcopy**로 파이프하면 됩니다. 마찬가지로 Linux에서 공개 키 파일을 **xclip**과 같은 프로그램에 파이프할 수 있습니다

Azure에서 Linux VM에 배치하는 공개 키는 키 쌍을 만들 때 다른 위치를 지정하지 않는다면, 기본적으로 ~/.ssh/id_rsa.pub에 저장됩니다. 기존 공개 키를 사용하여 VM을 만들기 위해 [Azure CLI 2.0](/cli/azure)을 사용하는 경우 `--ssh-key-value` 옵션과 함께 [az vm create](/cli/azure/vm#az-vm-create) 명령을 사용하여 이 공개 키의 값과 위치(선택 사항)을 지정합니다. 다음 명령에서 *VMname*, *RGname* 및 *keyFile*을 자신의 값으로 바꿉니다.

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-value @keyFile
```

## <a name="ssh-into-your-vm"></a>VM에 SSH 수행

Azure VM에 공개 키를 배포하고 로컬 시스템에 개인 키를 배포하여 VM의 IP 주소 또는 DNS 이름을 사용하여 VM에 SSH를 수행합니다. 다음 명령에서 *azureuser*와 *myvm.westus.cloudapp.azure.com*을 관리자 사용자 이름과 정규화된 도메인 이름(또는 IP 주소)으로 바꿉니다.

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

키 쌍을 만들 때 암호를 지정한 경우 로그인 프로세스 중에 메시지가 표시되면 해당 암호를 입력합니다. VM이 ~/.ssh/known_hosts 파일에 추가되고, Azure VM의 공개 키가 변경되거나 ~/.ssh/known_hosts에서 서버 이름이 제거될 때까지 다시 연결하라는 메시지가 표시되지 않습니다.

VM이 Just-In-Time 액세스 정책을 사용하는 경우에는 액세스 권한을 요청해야 VM에 연결할 수 있습니다. Just-In-Time 정책과 관련된 자세한 내용은 [Just-In-Time 정책을 사용하여 가상 머신 액세스 관리](../../security-center/security-center-just-in-time.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* SSH 키 쌍 작업에 대한 자세한 정보는 [SSH 키 쌍을 만들고 관리하는 자세한 단계](create-ssh-keys-detailed.md)를 참조하세요.

* Azure VM에 SSH를 연결하는 데 어려움이 있으면 [Azure Linux VM에 SSH 연결 문제 해결](troubleshoot-ssh-connection.md)을 참조하세요.
