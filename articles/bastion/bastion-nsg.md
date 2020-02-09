---
title: Azure 방호에서 Vm 및 NSGs 사용
description: 이 문서에서는 Azure 방호에 NSG 액세스를 통합 하는 방법을 설명 합니다.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 15abee4688a2f6aefa2b08ad2b8eee6622d56be2
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087260"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>NSG 액세스 및 Azure 방호 작업

Azure 방호에서 작업 하는 경우 NSGs (네트워크 보안 그룹)를 사용할 수 있습니다. 자세한 내용은 [보안 그룹](../virtual-network/security-overview.md)을 참조 하세요. 

![Architecture](./media/bastion-nsg/nsg-architecture.png)

이 다이어그램에서

* 요새 호스트는 가상 네트워크에 배포 됩니다.
* 사용자는 HTML5 브라우저를 사용하여 Azure Portal에 연결합니다.
* 사용자가 Azure 가상 머신을 RDP/SSH로 이동 합니다.
* 통합 연결-브라우저 내부에서 단일 클릭 RDP/SSH 세션
* Azure VM에 공용 IP가 필요하지 않습니다.

## <a name="nsg"></a>네트워크 보안 그룹

이 섹션에서는 사용자와 Azure 방호 간의 네트워크 트래픽과, 가상 네트워크의 대상 Vm에 대한 네트워크 트래픽을 보여 줍니다.

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

Azure 방호는 특히 AzureBastionSubnet에 배포 됩니다.

* **수신 트래픽:**

   * **공용 인터넷에서 수신 되는 트래픽:** Azure 방호는 수신 트래픽에 대한 공용 IP에서 포트 443를 사용 하도록 설정 해야 하는 공용 IP를 만듭니다. AzureBastionSubnet에서 포트 3389/22을 열 필요는 없습니다.
   * **Azure 방호 제어 평면의 수신 트래픽:** 제어 평면 연결의 경우 **Gmanager** 서비스 태그에서 포트 443 인바운드를 사용 하도록 설정 합니다. 그러면 제어 평면, 즉 게이트웨이 관리자가 Azure 방호와 통신할 수 있습니다.

* **송신 트래픽:**

   * **대상 vm에 대한 송신 트래픽:** Azure 방호는 개인 IP를 통해 대상 Vm에 도달 합니다. NSGs는 포트 3389 및 22의 다른 대상 VM 서브넷에 송신 트래픽을 허용 해야 합니다.
   * **Azure의 다른 공용 엔드포인트에 대한 송신 트래픽:** Azure 방호는 Azure 내에서 다양 한 공용 엔드포인트 (예: 진단 로그 저장 및 계량 로그)에 연결할 수 있어야 합니다. 이러한 이유로 Azure 방호에는 443 ~ **Azurecloud** service 태그의 아웃 바운드가 필요 합니다.

* **대상 VM 서브넷:** 이 서브넷은 RDP/SSH 하려는 대상 가상 머신을 포함 하는 서브넷입니다.

   * **Azure 방호의 수신 트래픽:** Azure 방호는 개인 IP를 통해 대상 VM에 연결 됩니다. RDP/SSH 포트 (각각 포트 3389/22)는 개인 IP를 통해 대상 VM 쪽에서 열어야 합니다. 모범 사례로,이 규칙에서 Azure 방호 서브넷 IP 주소 범위를 추가 하 여, 대상 VM 서브넷의 대상 Vm에서 이러한 포트를 열 수만 있게 할 수 있습니다.

## <a name="apply"></a>AzureBastionSubnet에 NSGs 적용

***AzureBastionSubnet***에 nsg를 만들고 적용 하는 경우 nsg에서 다음 규칙을 추가 했는지 확인 합니다. 이러한 규칙을 추가 하지 않으면 NSG 생성/업데이트가 실패 합니다.

* **제어 평면 연결:** 게이트웨이 관리자의 443에 대한 인바운드
* **진단 로깅 및 기타:** 443에 대한 아웃 바운드를 AzureCloud로 설정 합니다. 이 서비스 태그 내의 지역 태그는 아직 지원 되지 않습니다.
* **대상 VM:** 3389 및 22의 아웃 바운드 VirtualNetwork

이 [빠른 시작 템플릿에서](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg)nsg 규칙 예제를 참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 방호에 대한 자세한 내용은 [FAQ](bastion-faq.md)를 참조 하세요.
