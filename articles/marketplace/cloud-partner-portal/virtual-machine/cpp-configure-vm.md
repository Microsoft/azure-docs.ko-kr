---
title: Azure Marketplace에 대한 Microsoft Azure 호스팅 VM 구성 | Microsoft Docs
description: Azure에서 호스팅되는 VM을 크기 조정, 업데이트 및 일반화하는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 9cf363bc5f4230306c2fec99eb6287b23e598a4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744349"
---
# <a name="configure-the-azure-hosted-vm"></a>Azure 호스팅 VM 구성

이 문서에서는 Azure에서 호스팅되는 VM(가상 머신)을 크기 조정, 업데이트 및 일반화하는 방법에 대해 설명합니다.  이러한 단계는 Azure Marketplace에서 배포할 VM을 준비하는 데 필요합니다.


## <a name="sizing-the-vhds"></a>VHD 크기 조정

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
운영 체제 (및 필요에 따라 추가 서비스)를 사용 하 여 사전 구성 Vm 중 하나를 선택한 경우에 설명 된 대로 표준 Azure VM 크기를을 이미 선택 했습니다 [가상 머신 Sku 탭](./cpp-skus-tab.md)합니다.  사전 구성된 OS로 솔루션을 시작하는 것이 좋습니다.  그러나 OS를 설치하는 경우 VM 이미지의 기본 VHD 크기를 조정해야 합니다.

- Windows의 경우 운영 체제 VHD는 127-128GB의 고정 형식 VHD로 만들어야 합니다. 
- Linux의 경우 이 VHD는 30-50GB의 고정 형식 VHD로 만들어야 합니다.

물리적 크기가 127-128GB보다 작은 경우 VHD가 스파스여야 합니다. 이미 제공된 기본 Windows 및 SQL Server 이미지는 이러한 요구 사항을 충족해야 하므로 가져온 VHD의 형식 또는 크기를 변경하지 마세요. 

데이터 디스크는 1TB이하여야 합니다. 디스크 크기를 결정할 때 고객이 배포 시에 이미지 내에서 VHD 크기를 조정할 수 없음에 유의하세요. 데이터 디스크 VHD는 고정 형식 VHD로 만들어야 합니다. 또한 스파스여야 합니다. 처음에는 데이터 디스크가 비어 있거나 데이터를 포함할 수 있습니다.


## <a name="install-the-most-current-updates"></a>최신 업데이트 설치

운영 체제 VM의 기본 이미지에는 게시한 날짜까지의 최신 업데이트가 포함되어 있습니다. 만든 운영 체제 VHD를 게시하기 전에 OS 및 설치된 모든 서비스를 모든 최신 보안 및 유지 관리 패치로 업데이트해야 합니다.

Windows Server 2016의 경우 **업데이트 확인** 명령을 실행합니다.  이와 달리 이전 버전의 Windows의 경우 [Windows 업데이트를 통해 업데이트를 구하는 방법](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update)을 참조하세요.  Windows 업데이트는 중요한 최신 보안 업데이트를 자동으로 설치합니다.

Linux 배포판의 경우 일반적으로 명령줄 도구 또는 그래픽 유틸리티를 통해 업데이트를 다운로드하고 설치합니다.  예를 들어 Ubuntu Linux는 [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) 명령과 [업데이트 관리자](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) 도구를 제공하여 OS를 업데이트합니다.


## <a name="perform-additional-security-checks"></a>추가 보안 검사 수행

Azure Marketplace에서 솔루션 이미지에 대한 높은 수준의 보안을 유지해야 합니다.  다음 문서에서는 이 목표를 달성하는 데 도움이 되는 보안 구성 및 절차에 대한 검사 목록을 제공합니다. [Azure Marketplace 이미지에 대한 보안 권장 사항](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  이러한 권장 사항 중 일부는 Linux 기반 이미지에만 적용되지만, 대부분은 모든 VM 이미지에 적용됩니다. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>사용자 지정 구성 및 예약된 작업 수행

추가 구성이 필요한 경우 VM을 배포한 후 시작 시 실행되는 예약된 작업을 사용하여 VM을 최종적으로 변경하는 것이 좋습니다.  다음 권장 사항도 고려하세요.
- 한 번 실행되는 작업인 경우 작업이 성공적으로 완료되면 해당 작업 자체를 삭제하는 것이 좋습니다.
- 구성에는 항상 존재하도록 보장되는 두 개의 드라이브, 즉 C와 D 드라이브만 있으므로 C 또는 D 이외의 드라이브는 사용할 수 없습니다. C 드라이브는 운영 체제 디스크이고 D 드라이브는 임시 로컬 디스크입니다.

Linux 사용자 지정에 대한 자세한 내용은 [Linux용 가상 머신 확장 및 기능](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)을 참조하세요.


## <a name="generalize-the-image"></a>이미지 일반화

Azure Marketplace의 모든 이미지는 일반적으로 다시 사용할 수 있어야 합니다. 이 재사용을 달성하려면 운영 체제 VHD가 *일반화*되어야 합니다. 이 작업은 VM에서 인스턴스 관련 식별자와 소프트웨어 드라이버를 모두 제거합니다.

### <a name="windows"></a>Windows

Windows OS 디스크는 [sysprep 도구](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)를 사용하여 일반화됩니다. 나중에 OS를 업데이트하거나 재구성하면 sysprep을 다시 실행해야 합니다. 

> [!WARNING]
>  업데이트가 자동으로 실행될 수 있으므로 sysprep을 실행한 후에는 배포될 때까지 VM을 해제해야 합니다.  이렇게 종료하면 이후 업데이트에서 VHD OS 또는 설치된 서비스에 대한 인스턴스 관련 변경이 수행되지 않습니다.

Sysprep을 실행 하는 방법에 대 한 자세한 내용은 참조 하세요. [VHD를 일반화 하는 단계](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#steps-to-generalize-a-vhd)

### <a name="linux"></a>Linux

다음의 2단계 프로세스는 Linux VM을 일반화하고 별도의 VM으로 다시 배포합니다.  자세한 내용은 [가상 머신 또는 VHD의 이미지를 만드는 방법](../../../virtual-machines/linux/capture-image.md)을 참조하세요. 

#### <a name="remove-the-azure-linux-agent"></a>Azure Linux 에이전트 제거
1.  SSH 클라이언트를 사용하여 Linux VM에 연결합니다.
2.  SSH 창에서 다음 명령을 입력합니다. <br/>
    `sudo waagent -deprovision+user`
3.  `y`를 입력하여 계속 진행합니다. (이 확인 단계를 방지하기 위해 이전 명령에 `-force` 매개 변수를 추가할 수 있습니다.)
4.  명령이 완료되면 `exit`를 입력하여 SSH 클라이언트를 닫습니다.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>이미지 캡처
1.  Azure Portal로 이동하고, RG(리소스 그룹)를 선택하고, VM을 할당 취소합니다.
2.  이제 VHD가 일반화되었으며, 이 VHD를 사용하여 새 VM을 만들 수 있습니다.


## <a name="create-one-or-more-copies"></a>하나 이상의 복사본 만들기

VM 복사본을 만들면 백업, 테스트, 사용자 지정 장애 조치 또는 부하 분산, 다른 솔루션 구성 제공 등을 수행하는 데 유용합니다. 기본 VHD를 복제 및 다운로드하고 관리되지 않는 복제를 만드는 방법에 대한 자세한 내용은 다음을 참조하세요.

- Linux VM: [Azure에서 Linux VHD 다운로드](../../../virtual-machines/linux/download-vhd.md)
- Windows VM: [Azure에서 Windows VHD 다운로드](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>다음 단계

VM이 구성되면 [가상 하드 디스크에서 가상 머신](./cpp-deploy-vm-vhd.md)을 배포할 준비가 됩니다.
