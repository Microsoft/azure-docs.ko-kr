---
title: Azure SSIS 통합 런타임을 구성 하 여 가상 네트워크에 가입 하는 자습서
description: Azure 가상 네트워크에 Azure SSIS 통합 런타임을 조인 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 065610a9de4898d012cef8a16849c09a81f0774c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841100"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>가상 네트워크에 가입 하기 위해 Azure SQL Server Integration Services (SSIS) IR (통합 런타임) 구성

이 자습서에서는 Azure Portal를 사용 하 여 가상 네트워크에 가입 하기 위해 Azure SQL Server Integration Services (SSIS) IR (통합 런타임)을 구성 하는 기본 단계를 제공 합니다.

단계는 다음과 같습니다.

- 가상 네트워크를 구성합니다.
- Azure Data Factory 포털에서 가상 네트워크에 Azure-SSIS IR을 연결 합니다.

## <a name="prerequisites"></a>필수 조건

- **Azure-SSIS 통합 런타임**. Azure SSIS 통합 런타임이 없는 경우 시작 하기 전에 [Azure Data Factory에서 AZURE ssis 통합 런타임을 프로 비전](tutorial-deploy-ssis-packages-azure.md) 합니다.

- **사용자 권한**. Azure-SSIS IR를 만드는 사용자에 게 다음 옵션 중 하나를 사용 하 여 최소한 Azure Data Factory 이상의 리소스에 대한 [역할 할당](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) 이 있어야 합니다.

    - 기본 제공 네트워크 참가자 역할을 사용 합니다. 이 역할에는 범위가 필요 이상으로 큰 _Microsoft.Network/\*_ 권한이 제공됩니다.
    - 필요한 _Microsoft.Network/virtualNetworks/\*/join/action_ 권한만 포함하는 사용자 지정 역할을 만듭니다. 또한 Azure Resource Manager 가상 네트워크에 연결 하는 동안 Azure-SSIS IR에 대한 공용 IP 주소를 가져오려면 역할에 _Microsoft. network/publicIPAddresses/*/join/action_ 권한도 포함 해야 합니다.

- **가상 네트워크**.

    - 가상 네트워크가 없는 경우 [Azure Portal를 사용 하 여 가상 네트워크를 만듭니다](https://docs.microsoft.com/azure/virtual-network/quick-create-portal).

    - 가상 네트워크의 리소스 그룹이 특정 Azure 네트워크 리소스를 만들고 삭제할 수 있는지 확인 합니다.
    
        Azure-SSIS IR은 가상 네트워크와 동일한 리소스 그룹에 특정 네트워크 리소스를 만들어야 합니다. 이러한 리소스는 다음과 같습니다.
        - 이름 *\<Guid >-azurebatch-cloudserviceloadbalancer* 를 사용 하는 Azure 부하 분산 장치
        - 이름 *\<Guid >-azurebatch-cloudservicenetworksecuritygroup를 사용 하는 네트워크 보안 그룹
        - 이름이-azurebatch-cloudservicepublicip 인 Azure 공용 IP 주소
    
        이러한 리소스는 Azure-SSIS IR 시작 될 때 생성 됩니다. Azure-SSIS IR 중지 될 때 삭제 됩니다. Azure-SSIS IR 중단 되지 않도록 차단 하려면 다른 리소스에서 이러한 네트워크 리소스를 다시 사용 하지 마세요.

    - 가상 네트워크가 속한 리소스 그룹/구독에 대한 [리소스 잠금이](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) 없는지 확인 합니다. 읽기 전용/삭제 잠금을 구성 하는 경우 Azure-SSIS IR를 시작 하 고 중지 하면 실패 하거나 응답 하지 않습니다.

    - 가상 네트워크가 속한 리소스 그룹/구독에서 다음 리소스를 만들지 못하도록 하는 Azure 정책이 없는지 확인 합니다.
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- 아래 **네트워크 구성** 시나리오는이 자습서에서 다루지 않습니다.
    - Azure-SSIS IR에 대한 사용자 고유의 공용 IP 주소를 가져옵니다.
    - 사용자 고유의 DNS (Domain Name System) 서버를 사용 하는 경우
    - 서브넷에 NSG (네트워크 보안 그룹)를 사용 하는 경우
    - Azure Express 경로 또는 UDR (사용자 정의 경로)를 사용 하는 경우.
    - 사용자 지정 된 Azure-SSIS IR를 사용 하는 경우
    
    자세한 내용은 [가상 네트워크 구성](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)을 참조 하세요.

## <a name="configure-a-virtual-network"></a>가상 네트워크 구성

Azure Portal를 사용 하 여 Azure-SSIS IR에 연결 하기 전에 가상 네트워크를 구성 합니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 Data Factory UI를 지원 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **추가 서비스**를 선택합니다. **가상 네트워크**를 필터링하여 선택합니다.

1. 목록에서 가상 네트워크를 필터링하고 선택합니다.

1. **가상 네트워크** 페이지에서 **속성**을 선택합니다.

1. **리소스 ID**에 대한 복사 단추를 선택하여 가상 네트워크에 대한 리소스 ID를 클립보드에 복사합니다. 클립 보드의 ID를 OneNote 또는 파일에 저장합니다.

1. 왼쪽 메뉴에서 **서브넷**을 선택 합니다.

    - 선택한 서브넷에 Azure-SSIS IR 사용할 수 있는 충분 한 주소 공간이 있는지 확인 합니다. IR 노드 번호의 두 배 이상에 대해 사용 가능한 IP 주소를 유지 합니다. Azure는 각 서브넷 내의 일부 IP 주소를 예약합니다. 이러한 주소는 사용할 수 없습니다. 서브넷의 첫 번째 및 마지막 IP 주소는 프로토콜 규칙을 위해 예약 되 고 3 개 이상의 주소가 Azure 서비스에 사용 됩니다. 자세한 내용은 [이러한 서브넷 내에서 IP 주소를 사용하는데 제한 사항이 있습니까?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Azure-SSIS IR을 배포 하려면 게이트웨이 서브넷을 선택 하지 마세요. 가상 네트워크 게이트웨이 전용입니다.
    - 다른 Azure 서비스 (예: SQL Database 관리 되는 인스턴스, App Service 등)에서 독점적으로 사용 되는 서브넷을 사용 하지 마세요.

1. 가상 네트워크가 있는 Azure 구독에 Azure Batch 공급자가 등록되었는지 확인합니다. 또는 Azure Batch 공급자를 등록 합니다. 구독에 Azure Batch 계정이 이미 있는 경우에는 Azure Batch에 대한 구독이 등록 됩니다. (Data Factory 포털에 Azure-SSIS IR을 만들면 Azure Batch 공급자가 자동으로 등록됩니다.)

   1. Azure Portal의 왼쪽 메뉴에서 **구독**을 선택 합니다.

   1. 구독을 선택합니다.

   1. 왼쪽에서 **리소스 공급자**를 선택 하 고, **Microsoft Batch** 가 등록 된 공급자 인지 확인 합니다.

   !["등록됨" 상태 확인](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   목록에서 **Microsoft.Batch**가 보이지 않으면 지금 등록할 수 있도록 구독에서 [빈 Azure Batch 계정을 만듭니다](../batch/batch-account-create-portal.md). 나중에 삭제할 수 있습니다.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Azure-SSIS IR을 가상 네트워크에 조인

Azure Resource Manager 가상 네트워크 또는 클래식 가상 네트워크를 구성한 후 Azure-SSIS IR 가상 네트워크에 조인할 수 있습니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 Data Factory UI를 지원 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **데이터 팩터리**를 선택 합니다. 메뉴에 **데이터 팩터리** 가 표시 되지 않으면 **추가 서비스**를 선택한 다음 **인텔리전스 + 분석** 섹션에서 **데이터 팩터리**를 선택 합니다.

   ![데이터 팩터리 목록](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 목록에서 Azure-SSIS IR를 사용 하 여 데이터 팩터리를 선택 합니다. 데이터 팩터리의 홈 페이지가 표시됩니다. **작성자 & 모니터** 타일을 선택 합니다. 별도의 탭에 Data Factory UI가 표시됩니다.

   ![데이터 팩터리 홈페이지](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 데이터 팩터리 UI에서 **편집** 탭으로 전환하고 **연결**을 선택한 다음, **통합 런타임** 탭으로 전환합니다.

   !["통합 런타임" 탭](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Azure-SSIS IR 실행 중인 경우 **통합 런타임** 목록의 **작업** 열에서 Azure-SSIS IR의 **중지** 단추를 선택 합니다. 중지할 때까지 Azure-SSIS IR을 편집할 수 없습니다.

   ![IR 중지](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. **통합 런타임** 목록의 **작업** 열에서 Azure-SSIS IR에 대한 **편집** 단추를 선택 합니다.

   ![통합 런타임 편집](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Integration runtime 설정 패널에서 **다음** 단추를 선택 하 여 **일반 설정** 및 **SQL 설정** 섹션을 진행 합니다.

1. **고급 설정** 섹션에서 다음을 수행 합니다.
   1. **참가할 VNet 선택 Azure-SSIS Integration Runtime 선택 하 고 ADF가 특정 네트워크 리소스를 만들도록 허용 하 고 선택적으로 고정 공용 IP 주소를 가져옵니다** . 확인란을 선택 합니다.

   1. **구독**에 대해서는 가상 네트워크가 있는 Azure 구독을 선택합니다.

   1. **위치**에는 통합 런타임의 동일한 위치가 선택됩니다.

   1. **유형**에 대해 가상 네트워크의 유형 (클래식 또는 Azure Resource Manager)을 선택 합니다. 클래식 가상 네트워크는 곧 사용 되지 않으므로 Azure Resource Manager 가상 네트워크를 선택 하는 것이 좋습니다.

   1. **VNet 이름**에서 가상 네트워크의 이름을 선택합니다. 가상 네트워크 서비스 엔드포인트을 사용 하는 Azure SQL Database 서버 또는 SSISDB를 호스트 하는 개인 엔드포인트이 포함 된 관리 되는 인스턴스를 사용 하는 것과 동일 해야 합니다. 또는 온-프레미스 네트워크에 연결 된 것과 동일 해야 합니다. 그렇지 않은 경우에는 Azure-SSIS IR에 대한 고정 공용 IP 주소를 만들 수 있는 가상 네트워크가 될 수 있습니다.

   1. **서브넷 이름**에서 가상 네트워크의 서브넷 이름을 선택합니다. SSISDB를 호스트 하는 가상 네트워크 서비스 엔드포인트을 사용 하 여 Azure SQL Database 서버에 사용 하는 것과 동일 해야 합니다. 또는 SSISDB를 호스트 하기 위해 개인 엔드포인트을 사용 하 여 관리 되는 인스턴스에 사용 되는 서브넷과 다른 서브넷 이어야 합니다. 그렇지 않은 경우에는 Azure-SSIS IR에 대한 고정 공용 IP 주소를 가져오는 서브넷이 될 수 있습니다.

   1. **VNet 유효성 검사**를 선택 합니다. 유효성 검사에 성공 하면 **계속**을 선택 합니다.

   ![가상 네트워크 관련 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. **요약** 섹션에서 Azure-SSIS IR에 대한 모든 설정을 검토 합니다. 그런 다음 **업데이트**를 선택 합니다.

1. Azure-SSIS IR의 **작업** 열에서 **시작** 단추를 선택 하 여 Azure-SSIS IR를 시작 합니다. 가상 네트워크에 연결 하는 Azure-SSIS IR를 시작 하는 데 약 20 ~ 30 분이 소요 됩니다.

## <a name="next-steps"></a>다음 단계

[가상 네트워크에 Azure-SSIS IR를 조인 하는](join-azure-ssis-integration-runtime-virtual-network.md)방법에 대해 자세히 알아보세요.
