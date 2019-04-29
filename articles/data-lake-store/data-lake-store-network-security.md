---
title: Azure Data Lake Storage Gen1의 네트워크 보안 | Microsoft Docs
description: Azure Data Lake Storage Gen1에서 가상 네트워크 통합이 작동하는 방식을 알아봅니다.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 7d6c826df2a509ffb378809e3682073bd5ab1301
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60612699"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1에 대한 가상 네트워크 통합

이 문서에서는 Azure Data Lake Storage Gen1에 대한 가상 네트워크 통합에 대해 소개합니다. 가상 네트워크 통합을 사용하여 특정 가상 네트워크 및 서브넷의 트래픽만 허용하도록 계정을 구성할 수 있습니다. 

이 기능을 통해 외부 위협으로부터 Data Lake Storage 계정을 보호할 수 있습니다.

Data Lake Storage Gen1에 대한 가상 네트워크 통합에서는 가상 네트워크와 Azure AD(Azure Active Directory) 간에 가상 네트워크 서비스 엔드포인트 보안을 사용하여 액세스 토큰에서 추가 보안 클레임을 생성하게 됩니다. 그런 다음, 이러한 클레임을 사용하여 Data Lake Storage Gen1 계정에 대해 가상 네트워크를 인증하고 액세스를 허용합니다.

> [!NOTE]
> 이러한 기능을 사용하는 것과 관련된 추가 비용은 없습니다. 계정에 대해 Data Lake Storage Gen1에 대한 표준 요금이 청구됩니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable)을 참조하세요. 사용하는 다른 모든 Azure 서비스의 경우 [가격 책정](https://azure.microsoft.com/pricing/#product-picker)을 참조하세요.

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>Data Lake Storage Gen1에 대한 가상 네트워크 통합 시나리오

Data Lake Storage Gen1 가상 네트워크 통합을 사용하여 특정 가상 네트워크 및 서브넷으로부터 Data Lake Storage Gen1 계정에 대한 액세스를 제한할 수 있습니다. 계정이 지정된 가상 네트워크 서브넷에 대해 잠기면 Azure의 다른 가상 네트워크/VM에 액세스하도록 허용되지 않습니다. 기능적으로 Data Lake Storage Gen1 가상 네트워크 통합을 사용하면 [가상 네트워크 서비스 엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)와 동일한 시나리오를 사용할 수 있습니다. 다음 섹션에서는 몇 가지 주요 차이점을 자세히 설명합니다. 

![Data Lake Storage Gen1 가상 네트워크 통합에 대한 시나리오 다이어그램](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> 기존 IP 방화벽 규칙을 가상 네트워크 규칙과 함께 사용하여 온-프레미스 네트워크로부터 액세스도 허용할 수 있습니다. 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>Data Lake Storage Gen1 가상 네트워크 통합에서 최적의 라우팅

가상 네트워크 서비스 엔드포인트의 주요 이점은 가상 네트워크로부터 [최적의 라우팅](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview#key-benefits)이 가능하다는 점입니다. Data Lake Storage Gen1 계정에 동일한 경로 최적화를 수행할 수 있습니다. 가상 네트워크에서 Data Lake Storage Gen1 계정에 대해 다음 [사용자 정의 경로](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined)를 사용합니다.

**Data Lake Storage 공용 IP 주소** – 대상 Data Lake Storage Gen1 계정에 공용 IP 주소를 사용합니다. Data Lake Storage Gen1 계정의 IP 주소를 식별하기 위해 계정의 [DNS 이름을 확인](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-connectivity-from-vnets#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity)합니다. 각 주소에 대해 별도의 항목을 만듭니다.

    ```azurecli
    # Create a route table for your resource group.
    az network route-table create --resource-group $RgName --name $RouteTableName
    
    # Create route table rules for Data Lake Storage public IP addresses.
    # There's one rule per Data Lake Storage public IP address. 
    az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet
    
    # Update the virtual network, and apply the newly created route table to it.
    az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
    ```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>고객 가상 네트워크으로부터 데이터 반출

가상 네트워크의 액세스로부터 Data Lake Storage 계정을 보호하는 것 외에도 권한이 없는 계정에 대한 반출이 없는지 확인하는 것도 중요합니다.

가상 네트워크에서 방화벽 솔루션을 사용하여 대상 계정 URL에 따라 아웃바운드 트래픽을 필터링합니다. 승인된 Data Lake Storage Gen1 계정에 대한 액세스만을 허용합니다.

사용 가능한 옵션은 다음과 같습니다.
- [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview): 가상 네트워크에 [Azure Firewall을 배포하고 구성](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)합니다. Data Lake Storage 아웃바운드 트래픽을 보호하고 알려지고 승인된 계정 URL까지 잠급니다.
- [네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) 방화벽: 관리자는 특정 상용 방화벽 공급업체만 사용하도록 허용할 수 있습니다. 동일한 기능을 수행할 수 있도록 Azure Marketplace에서 지원되는 네트워크 가상 어플라이언스 방화벽 솔루션을 사용합니다.

> [!NOTE]
> 데이터 경로에서 방화벽을 사용하면 데이터 경로에서 추가 홉이 발생합니다. 종단 간 데이터 교환의 네트워크 성능에 영향을 줄 수 있습니다. 처리량 가용성 및 연결 대기 시간은 영향을 받을 수 있습니다. 

## <a name="limitations"></a>제한 사항

- Data Lake Storage Gen1 가상 네트워크 통합 지원을 사용할 수 있기 전에 만들어진 HDInsight 클러스터는 이 새 기능을 지원하기 위해 다시 만들어야 합니다.
 
- 가상 네트워크 통합을 사용하여 새 HDInsight 클러스터를 만들고 Data Lake Storage Gen1 계정을 선택하면 프로세스가 실패합니다. 먼저, 가상 네트워크 규칙을 사용하지 않습니다. 또는 Data Lake Storage 계정의 **방화벽 및 가상 네트워크** 블레이드에서 **모든 네트워크 및 서비스로부터 액세스 허용**을 선택합니다. 그런 다음, HDInsight 클러스터를 만든 후에 최종적으로 가상 네트워크 규칙을 다시 사용하도록 설정하거나 **모든 네트워크 및 서비스에서 액세스 허용**을 선택 취소합니다. 자세한 내용은 [예외](#exceptions) 섹션을 참조하세요.

- Data Lake Storage Gen1 가상 네트워크 통합은 [Azure 리소스에 대한 관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)에서 작동하지 않습니다.
  
- 가상 네트워크에서 사용된 Data Lake Storage Gen1 계정에서 파일 및 폴더 데이터는 포털에서 액세스할 수 없습니다. 이 제한 사항에는 가상 네트워크 내부의 VM 및 데이터 탐색기를 사용하는 등의 작업으로부터의 액세스가 포함됩니다. 계정 관리 작업은 계속 작동합니다. 가상 네트워크에서 사용된 Data Lake Storage 계정에서 파일 및 폴더 데이터는 포털 이외 모든 리소스를 통해 액세스할 수 있습니다. 이러한 리소스가 포털에서 발생하지 않은 경우 SDK 액세스, PowerShell 스크립트 및 기타 Azure 서비스를 포함합니다. 

## <a name="configuration"></a>구성

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>1단계: Azure AD 서비스 엔드포인트를 사용하도록 가상 네트워크 구성

1.  Azure Portal로 이동하고, 계정에 로그인합니다.
 
2.  구독에서 [새 가상 네트워크를 만듭니다](https://docs.microsoft.com/azure/virtual-network/quick-create-portal). 또는 기존 가상 네트워크로 이동할 수 있습니다. 가상 네트워크는 Data Lake Storage Gen 1 계정과 동일한 영역에 있어야 합니다.
 
3.  **가상 네트워크** 블레이드에서 **서비스 엔드포인트**를 선택합니다.
 
4.  **추가**를 선택하여 새 서비스 엔드포인트를 추가합니다.

    ![가상 네트워크 서비스 엔드포인트 추가](media/data-lake-store-network-security/config-vnet-1.png)

5.  **Microsoft.AzureActiveDirectory**를 엔드포인트의 서비스로 선택합니다.

     ![Microsoft.AzureActiveDirectory 서비스 엔드포인트 선택](media/data-lake-store-network-security/config-vnet-2.png)

6.  연결을 허용하려는 서브넷을 선택합니다. **추가**를 선택합니다.

    ![서브넷 선택](media/data-lake-store-network-security/config-vnet-3.png)

7.  서비스 엔드포인트 추가에 최대 15분이 소요될 수 있습니다. 추가된 후에 목록에 표시됩니다. 표시되고 모든 세부 정보가 구성된 대로인지 확인합니다.
 
    ![서비스 엔드포인트 추가 성공](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>2단계: Data Lake Storage Gen1 계정에 허용된 가상 네트워크 또는 서브넷 설정

1.  가상 네트워크를 구성하면 구독에서 [새 Azure Data Lake Storage Gen1 계정을 만듭니다](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account). 또한 기존 Data Lake Storage Gen1 계정으로 이동할 수 있습니다. Data Lake Storage Gen1 계정은 가상 네트워크와 동일한 영역에 있어야 합니다.
 
2.  **방화벽 및 가상 네트워크**를 선택합니다.

    > [!NOTE]
    > **방화벽 및 가상 네트워크**가 설정에 표시되지 않으면 포털에서 로그오프합니다. 브라우저를 닫고 브라우저 캐시를 지웁니다. 머신을 다시 시작하고 다시 시도합니다.

       ![Data Lake Storage 계정에 가상 네트워크 규칙 추가](media/data-lake-store-network-security/config-adls-1.png)

3.  **선택한 네트워크**를 선택합니다.
 
4.  **기존 가상 네트워크 선택**을 선택합니다.

    ![기존 가상 네트워크 추가](media/data-lake-store-network-security/config-adls-2.png)

5.  연결을 허용할 가상 네트워크 및 서브넷을 선택합니다. **추가**를 선택합니다.

    ![가상 네트워크 및 서브넷을 선택합니다.](media/data-lake-store-network-security/config-adls-3.png)

6.  목록에 가상 네트워크 및 서브넷이 올바르게 표시되는지 확인합니다. **저장**을 선택합니다.

    ![새 규칙 저장](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > 설정을 저장한 후에 적용되는 데 최대 5분이 걸릴 수 있습니다.

7.  [선택 사항] **방화벽 및 가상 네트워크** 페이지의 **방화벽** 섹션에서 특정 IP 주소의 연결을 허용할 수 있습니다. 

## <a name="exceptions"></a>예외
선택한 가상 네트워크 외부에서 Azure 서비스 및 VM의 연결을 설정할 수 있습니다. **방화벽 및 가상 네트워크** 블레이드의 **예외** 영역에서 다음 두 옵션 중 하나를 선택합니다.
 
- **모든 Azure 서비스가 이 Data Lake Storage Gen1 계정에 액세스하도록 허용합니다**. 이 옵션을 사용하면 Azure Data Factory, Azure Event Hubs 및 모든 Azure VM과 같은 Azure 서비스가 Data Lake Storage 계정과 통신할 수 있습니다.

- **Azure Data Lake Analytics가 이 Data Lake Storage Gen1 계정에 액세스하도록 허용합니다**. 이 옵션을 사용하면 Data Lake Analytics를 이 Data Lake Storage 계정에 연결할 수 있습니다. 

  ![방화벽 및 가상 네트워크 예외](media/data-lake-store-network-security/firewall-exceptions.png)

이러한 예외를 해제된 상태로 유지하는 것이 좋습니다. 가상 네트워크 외부에서 이러한 다른 서비스를 연결해야 하는 경우에만 해제하세요.
