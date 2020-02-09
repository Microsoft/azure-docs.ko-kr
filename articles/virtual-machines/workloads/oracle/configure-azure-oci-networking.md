---
title: Oracle 클라우드 인프라와 Azure Express 경로 연결 | Microsoft Docs
description: Azure Express 경로를 OCI (Oracle Cloud Infrastructure) FastConnect와 연결 하 여 클라우드 간 Oracle 응용 프로그램 솔루션 사용
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2019
ms.author: rogirdh
ms.openlocfilehash: 63543c0ac34536b736bd4b8cdbd47fdd98e9f9be
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802204"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Azure와 Oracle 클라우드 인프라 간의 직접 상호 관계 설정  

[통합 다중 클라우드 환경](oracle-oci-overview.md) (미리 보기)을 만들기 위해 Microsoft와 Oracle은 [Express](../../../expressroute/expressroute-introduction.md) 경로 및 [FASTCONNECT](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)를 통해 Azure와 OCI (oracle cloud Infrastructure) 간에 직접 연결을 제공 합니다. Express 경로 및 FastConnect 연결을 통해 고객은 두 클라우드 간에 짧은 대기 시간, 높은 처리량, 개인 직접 연결을 경험할 수 있습니다.

> [!IMPORTANT]
> Microsoft Azure와 OCI 간의 연결은 미리 보기 단계에 있습니다. Azure와 OCI 사이에 짧은 대기 시간 연결을 설정 하려면 먼저이 기능을 사용 하도록 Azure 구독을 설정 해야 합니다. 이 간단한 [설문 조사 양식을](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu)완료 하 여 미리 보기에 등록 해야 합니다. 구독이 등록되면 이메일 회신을 받게 됩니다. 확인 이메일을 받을 때까지는 기능을 사용할 수 없습니다. 이 미리 보기를 사용 하도록 설정 하려면 Microsoft 담당자에 게 문의할 수도 있습니다. 미리 보기 기능에 대 한 액세스는 가용성 및 Microsoft에 의해 제한 될 수 있습니다. 설문 조사 완료는 액세스를 보장 하지 않습니다. 이 미리 보기는 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에 사용할 수 없습니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 Microsoft Azure 미리 보기에 대 한 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 을 참조 하세요. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

다음 이미지는 상호 교차 수준 개요를 보여 줍니다.

![클라우드 간 네트워크 연결](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>선행 조건

* Azure와 OCI 간에 연결을 설정 하려면 활성 Azure 구독 및 활성 OCI 테넌트가 있어야 합니다.

* Azure Express 경로 피어 링 위치가 OCI FastConnect와 동일한 피어 링 위치에 있는 경우에만 연결을 사용할 수 있습니다. [미리 보기 제한 사항](oracle-oci-overview.md#preview-limitations)을 참조 하세요.

* 이 미리 보기 기능을 사용 하려면 Azure 구독을 사용 하도록 설정 해야 합니다.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>Express 경로와 FastConnect 간의 직접 연결 구성

1. 리소스 그룹에서 Azure 구독에 표준 Express 경로 회로를 만듭니다. 
    * Express 경로를 만드는 동안 서비스 공급자로 **Oracle Cloud FastConnect** 를 선택 합니다. Express 경로 회로를 만들려면 단계별 [가이드](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)를 참조 하세요.
    * Azure Express 경로 회로는 세분화 된 대역폭 옵션을 제공 하는 반면 FastConnect는 1, 2, 5 또는 10gbps를 지원 합니다. 따라서 Express 경로 아래에서 이러한 일치 하는 대역폭 옵션 중 하나를 선택 하는 것이 좋습니다.

    ![Express 경로 회로 만들기](media/configure-azure-oci-networking/exr-create-new.png)
1. Express 경로 **서비스 키**를 적어둡니다. FastConnect 회로를 구성 하는 동안 키를 제공 해야 합니다.

    ![Express 경로 서비스 키](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > **공급자 상태가** **프로 비전 되지 않은**경우에도 express 경로 회로가 프로 비전 되는 즉시 express 경로 요금이 청구 됩니다.

1. Azure virtual network 또는 OCI 가상 클라우드 네트워크 IP 주소 공간과 겹치지 않는/30의 두 개인 IP 주소 공간을 일정 합니다. 첫 번째 IP 주소 공간은 기본 주소 공간으로, 두 번째 IP 주소 공간은 보조 주소 공간으로 참조 합니다. FastConnect 회로를 구성할 때 필요한 주소를 적어둡니다.
1. DRG (동적 라우팅 게이트웨이)를 만듭니다. FastConnect 회로를 만들 때 필요 합니다. 자세한 내용은 [동적 라우팅 게이트웨이](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) 설명서를 참조 하세요.
1. Oracle 테넌트에서 FastConnect 회로를 만듭니다. 자세한 내용은 [Oracle 설명서](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)를 참조 하십시오.
  
    * FastConnect 구성에서 공급자로 **Microsoft Azure: express** 경로를 선택 합니다.
    * 이전 단계에서 프로 비전 한 동적 라우팅 게이트웨이를 선택 합니다.
    * 프로 비전 할 대역폭을 선택 합니다. 성능을 최적화 하기 위해 대역폭은 Express 경로 회로를 만들 때 선택한 대역폭과 일치 해야 합니다.
    * **공급자 서비스 키**에 express 경로 서비스 키를 붙여넣습니다.
    * 이전 단계에서 **주 BGP ip** 주소에 대 한 첫 번째/30 개인 ip 주소 공간을 사용 하 고 **보조 bgp ip** 주소에 대해 두 번째/30 개인 ip 주소 공간을 공백을 만들.
        * Oracle BGP IP 주소 (기본 및 보조)에 대해 두 범위의 첫 번째 가능한 주소를 할당 하 고 두 번째 주소를 FastConnect 관점에서 고객 BGP IP 주소에 할당 합니다. 첫 번째 가능한 IP 주소는/30 주소 공간의 두 번째 IP 주소입니다 (첫 번째 IP 주소는 Microsoft에서 예약 함).
    * **만들기**를 클릭합니다.
1. 경로 테이블을 사용 하 여 동적 라우팅 게이트웨이를 통해 Oracle 테넌트의 가상 클라우드 네트워크에 FastConnect 연결을 완료 합니다.
1. Azure로 이동 하 여 Express 경로 회로에 대 한 **공급자 상태가** **프로 비전** 됨으로 변경 되 고 **azure private** 형식의 피어 링이 프로 비전 되었는지 확인 합니다. 다음 단계를 수행 하기 위한 필수 구성 요소입니다.

    ![Express 경로 공급자 상태](media/configure-azure-oci-networking/exr-provider-status.png)
1. **Azure 개인** 피어 링을 클릭 합니다. FastConnect 회로를 설정할 때 입력 한 정보를 기준으로 피어 링 세부 정보가 자동으로 구성 된 것을 볼 수 있습니다.

    ![개인 피어 링 설정](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>Express 경로에 가상 네트워크 연결

1. 아직 없는 경우 가상 네트워크 및 가상 네트워크 게이트웨이를 만듭니다. 자세한 내용은 단계별 [가이드](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)를 참조 하세요.
1. [Terraform 스크립트](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) 를 실행 하거나 PowerShell 명령을 실행 하 여 [express 경로 Fastpath를 구성](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)하 여 가상 네트워크 게이트웨이와 express 경로 회로 간에 연결을 설정 합니다.

네트워크 구성을 완료 한 후에는 Azure Portal의 Express 경로 개인 피어 링 블레이드에서 **ARP 레코드 가져오기** 및 **경로 테이블 가져오기** 를 클릭 하 여 구성의 유효성을 확인할 수 있습니다.

## <a name="automation"></a>Automation

Microsoft는 Terraform 스크립트를 만들어 네트워크 상호 연결의 자동 배포를 사용 하도록 설정 했습니다. Terraform 스크립트는 Azure 구독에 대 한 적절 한 권한이 필요 하기 때문에 실행 전에 Azure를 사용 하 여 인증 해야 합니다. [Azure Active Directory 서비스 주체](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 를 사용 하거나 Azure CLI를 사용 하 여 인증을 수행할 수 있습니다. 자세한 내용은 [Terraform 설명서](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)를 참조 하세요.

내부 연결을 배포 하기 위한 Terraform 스크립트 및 관련 설명서는이 [GitHub 리포지토리에서](https://aka.ms/azureociinterconnecttf)찾을 수 있습니다.

## <a name="monitoring"></a>모니터링

두 클라우드 모두에 에이전트를 설치 하면 Azure [네트워크 성능 모니터 (NPM)](../../../expressroute/how-to-npm.md) 를 활용 하 여 종단 간 네트워크의 성능을 모니터링할 수 있습니다. NPM는 네트워크 문제를 쉽게 식별 하 고 제거 하는 데 도움이 됩니다.

## <a name="delete-the-interconnect-link"></a>상호 연결 링크를 삭제 합니다.

상호 연결을 삭제 하려면 다음 단계를 지정 된 순서 대로 따라야 합니다. 그렇게 하지 않으면 "실패 상태" Express 경로 회로가 생성 됩니다.

1. Express 경로 연결을 삭제 합니다. 연결에 대 한 페이지에서 **삭제** 아이콘을 클릭 하 여 연결을 삭제 합니다. 자세한 내용은 [express 경로 설명서](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#delete-a-connection-to-unlink-a-vnet)를 참조 하세요.
1. Oracle 클라우드 콘솔에서 Oracle FastConnect를 삭제 합니다.
1. Oracle FastConnect 회로를 삭제 한 후에는 Azure Express 경로 회로를 삭제할 수 있습니다.

이 시점에서 삭제 및 프로 비전 해제 프로세스가 완료 됩니다.

## <a name="next-steps"></a>다음 단계

* OCI와 Azure 간의 클라우드 간 연결에 대 한 자세한 내용은 [Oracle 설명서](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)를 참조 하세요.
* [Terraform 스크립트](https://aka.ms/azureociinterconnecttf) 를 사용 하 여 Azure를 통해 대상 Oracle 응용 프로그램에 대 한 인프라를 배포 하 고 네트워크 상호 연결을 구성 합니다. 
