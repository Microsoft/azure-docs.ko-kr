---
title: Azure의 FreeBSD 소개 | Microsoft Docs
description: Azure에서 FreeBSD 가상 머신을 사용하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: 1f2d3c40352d60d3cc7366aca6f38a8255a7a629
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627710"
---
# <a name="introduction-to-freebsd-on-azure"></a>Azure의 FreeBSD 소개
이 문서에서는 Azure에서 FreeBSD 가상 머신을 실행하는 방법의 개요를 제공합니다.

## <a name="overview"></a>개요
Microsoft Azure용 FreeBSD는 최신 서버, 데스크톱 및 포함된 플랫폼을 작동하는 데 사용되는 고급 컴퓨터 운영 체제입니다.

Microsoft Corporation은 Azure에서 사용 가능한 [Azure VM 게스트 에이전트](https://github.com/Azure/WALinuxAgent/)가 미리 구성된 FreeBSD 이미지를 만들고 있습니다. 현재 다음 FreeBSD 버전이 Microsoft에서 이미지로 제공됩니다.

- FreeBSD 10.3 릴리스
- FreeBSD 10.4 릴리스
- FreeBSD 11.1 릴리스

이 에이전트는 처음 사용 시 VM을 프로비전(사용자 이름, 암호 또는 SSH 키, 호스트 이름 등)하고 선택적 VM 확장 기능을 사용하도록 설정하는 것과 같은 작업을 위해 FreeBSD VM과 Azure 패브릭 간의 통신을 담당합니다.

FreeBSD 후속 버전에서는 제품을 최신 상태로 유지하고, FreeBSD 릴리스 엔지니어링 팀에서 게시한 후에 바로 해당 최신 릴리스를 사용할 수 있도록 하는 전략을 따릅니다.

## <a name="deploying-a-freebsd-virtual-machine"></a>FreeBSD 가상 머신 배포
FreeBSD 가상 머신 배포 작업은 Azure Portal에서 Azure Marketplace의 이미지를 사용하는 간단한 프로세스입니다.

- [Azure Marketplace의 FreeBSD 10.4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
- [Azure Marketplace의 FreeBSD 11.2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112)

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>FreeBSD에서 Azure CLI를 통해 FreeBSD VM 만들기
먼저 FreeBSD 컴퓨터에서 다음 명령을 사용하여 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 설치해야 합니다.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

bash가 FreeBSD 컴퓨터에 설치되지 않은 경우 설치 전에 다음 명령을 실행합니다. 

```bash
sudo pkg install bash
```

python이 FreeBSD 컴퓨터에 설치되지 않은 경우 설치 전에 다음 명령을 실행합니다. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

설치하는 동안 `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)` 프롬프트가 표시됩니다. `y`로 답변하고 `a path to an rc file to update`로 `/etc/rc.conf`를 입력하면 문제 `ERROR: [Errno 13] Permission denied`가 발생할 수 있습니다. 이 문제를 해결하려면 현재 사용자에게 `etc/rc.conf` 파일에 대한 쓰기 권한을 부여해야 합니다.

이제 Azure에 로그인한 후 FreeBSD VM을 만들 수 있습니다. 다음은 FreeBSD 11.0 VM을 만드는 예제입니다. 새로 만든 공용 IP의 정규화된 DNS 이름을 사용하여 `--public-ip-address-dns-name` 매개 변수를 추가할 수도 있습니다. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

그 후 위의 배포 출력에 인쇄된 IP 주소를 통해 FreeBSD VM에 로그인할 수 있습니다. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>FreeBSD에 대한 VM 확장
FreeBSD에서 지원되는 VM 확장은 다음과 같습니다.

### <a name="vmaccess"></a>VMAccess
[VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) 확장으로 다음을 수행할 수 있습니다.

* 원래 sudo 사용자의 암호를 다시 설정합니다.
* 지정된 암호를 사용하여 새 sudo 사용자를 만듭니다.
* 지정된 키로 공개 호스트 키를 설정합니다.
* 호스트 키가 제공되지 않은 경우 VM 프로비전 중에 제공된 공개 호스트 키를 다시 설정합니다.
* SSH 포트(22)를 열고, reset_ssh가 true로 설정된 경우 복원합니다.
* 기존 사용자를 제거합니다.
* 디스크를 확인합니다.
* 추가된 디스크를 복구합니다.

### <a name="customscript"></a>CustomScript
[CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) 확장으로 다음을 수행할 수 있습니다.

* 제공될 경우 Azure Storage 또는 외부 공용 스토리지(예: GitHub)에서 사용자 지정된 스크립트를 다운로드합니다.
* 진입점 스크립트를 실행합니다.
* 인라인 명령을 지원합니다.
* 셸 및 Python 스크립트에서 Windows 스타일 줄 바꿈 문자를 자동으로 변환합니다.
* 셸 및 Python 스크립트의 BOM을 자동으로 제거합니다.
* CommandToExecute의 중요 데이터를 보호합니다.

> [!NOTE]
> FreeBSD VM은 현재 CustomScript 버전 1.x만 지원합니다.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>인증: 사용자 이름, 암호 및 SSH 키
Azure 포털을 사용하여 FreeBSD 가상 머신을 만들 때 사용자 이름, 암호 또는 SSH 공개 키를 제공해야 합니다.
Azure에서 FreeBSD 가상 머신을 배포하기 위한 사용자 이름은 가상 머신에 이미 있는 시스템 계정 이름(UID &lt;100)(예: "root")과 일치해서는 안 됩니다.
현재는 RSA SSH 키만 지원됩니다. 여러 줄 SSH 키는 `---- BEGIN SSH2 PUBLIC KEY ----`로 시작하고 `---- END SSH2 PUBLIC KEY ----`로 끝나야 합니다.

## <a name="obtaining-superuser-privileges"></a>Superuser 권한 얻기
Azure에서 가상 머신 인스턴스를 배포하는 동안 지정한 사용자 계정이 권한 있는 계정입니다. sudo의 패키지는 게시된 FreeBSD 이미지에 설치되어 있습니다.
이 사용자 계정을 통해 로그인하면 명령 구문을 사용하여 루트 권한으로 명령을 실행할 수 있습니다.

```
$ sudo <COMMAND>
```

선택적으로 `sudo -s`를 사용하여 루트 셸을 얻을 수 있습니다.

## <a name="known-issues"></a>알려진 문제
[Azure VM 게스트 에이전트](https://github.com/Azure/WALinuxAgent/) 버전 2.2.2에는 Azure의 FreeBSD VM에 프로비전 오류를 유발하는 [알려진 문제](https://github.com/Azure/WALinuxAgent/pull/517)가 있습니다. [Azure VM 게스트 에이전트](https://github.com/Azure/WALinuxAgent/) 버전 2.2.3 및 이후 릴리스에서는 해결책이 확보될 것입니다. 

## <a name="next-steps"></a>다음 단계
* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112) 로 가서 FreeBSD VM을 만듭니다.
