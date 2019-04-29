---
title: Azure DevTest Labs에서 공유 IP 주소 이해 | Microsoft Docs
description: Azure DevTest Labs에서 공유 IP 주소를 사용하여 랩 VM에 액세스하는 데 필요한 공용 IP 주소를 최소화하는 방법에 대해 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: e7080901118dde33ed07c8a80f254b9b0d2e221c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623043"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Azure DevTest Labs에서 공유 IP 주소 이해

Azure DevTest Labs에서는 랩 VM이 동일한 공용 IP 주소를 공유하여 개별 랩 VM에 액세스하는 데 필요한 공용 IP 주소 수를 최소화할 수 있습니다.  이 문서에서는 공유 IP의 작동 방식과 관련 구성 옵션에 대해 설명합니다.

## <a name="shared-ip-setting"></a>공유 IP 설정

랩을 만들면 가상 네트워크의 서브넷에 상주합니다.  기본적으로 이 서브넷은 **Enable shared public IP**(공유 공용 IP 사용)을 *예*로 설정하면 생성됩니다.  이 구성은 전체 서브넷에 하나의 공용 IP 주소를 만듭니다.  가상 네트워크 및 서브넷 구성에 대한 자세한 내용은 [Azure DevTest Labs에서 가상 네트워크 구성](devtest-lab-configure-vnet.md)을 참조하세요.

![새 랩 서브넷](media/devtest-lab-shared-ip/lab-subnet.png)

기존 랩의 경우 **구성 및 정책 > Virtual Networks**를 선택하여 이 옵션을 사용하도록 설정할 수 있습니다. 그런 다음, 목록에서 가상 네트워크를 선택하고 선택한 서브넷에 대해 **공유 공용 IP 사용**을 선택합니다. 또한 랩 Vm 간에 공용 IP 주소를 공유 하지 않으려는 경우 아무 랩에서 나이 옵션을 해제할 수 있습니다.

이 랩에 생성된 모든 VM은 기본적으로 공유 IP를 사용합니다.  VM을 만들 때 **IP 주소 구성** 아래 **고급 설정** 블레이드에서 이 설정을 확인할 수 있습니다.

![새 VM](media/devtest-lab-shared-ip/new-vm.png)

- **공유:** 로 생성 된 모든 Vm **공유** 하나의 리소스 그룹 (RG)에 배치 됩니다. 해당 RG에는 단일 IP 주소가 할당되며, RG에 있는 모든 VM은 이 IP 주소를 사용합니다.
- **공용:** 만든 각 VM 자체 IP 주소 있으며 자체 리소스 그룹에 생성 됩니다.
- **개인:** 만든 각 VM에 개인 IP 주소를 사용 합니다. 원격 데스크톱을 사용하여 인터넷에서 직접 이 VM에 연결할 수 없습니다.

공유 IP 사용를 사용 하 여 VM 서브넷에 추가 될 때마다 DevTest Labs 자동으로 부하 분산 장치에 VM을 추가 하 고 VM의 RDP 포트에 전달 하는 공용 IP 주소에서 TCP 포트 번호를 할당 합니다.  

## <a name="using-the-shared-ip"></a>공유 IP 사용

- **Linux 사용자:** IP 주소 또는 정규화 된 도메인 이름, 콜론, 포트를 순서 대로 사용 하 여 VM에 SSH 합니다. 예를 들어 아래 이미지에서 VM에 연결되는 RDP 주소는 `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`입니다.

  ![VM 예](media/devtest-lab-shared-ip/vm-info.png)

- **Windows 사용자:** 선택 된 **Connect** 미리 구성 된 RDP 파일을 다운로드 하 고 VM에 액세스 하려면 Azure portal에서 단추입니다.

## <a name="next-steps"></a>다음 단계

* [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md)
* [Azure DevTest Labs에서 가상 네트워크 구성](devtest-lab-configure-vnet.md)





