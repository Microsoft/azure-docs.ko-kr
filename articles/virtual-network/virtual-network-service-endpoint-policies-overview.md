---
title: Azure 가상 네트워크 서비스 엔드포인트 정책 | Microsoft Docs
description: 서비스 엔드포인트 정책을 사용하여 Virtual Network 트래픽을 Azure 서비스 리소스로 필터링하는 방법 알아보기
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sumeet.mittal
ms.openlocfilehash: b39f365c8b66f7cab074a20bc574803e12f93422
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61033903"
---
# <a name="virtual-network-service-endpoint-policies-preview"></a>Virtual Network 서비스 엔드포인트 정책(미리 보기)

VNet(Virtual Network) 서비스 엔드포인트 정책을 통해 가상 네트워크 트래픽을 Azure 서비스로 필터링하여 특정 Azure 서비스 리소스만 서비스 엔드포인트에서 허용할 수 있습니다. 엔드포인트 정책은 Azure 서비스에 대한 가상 네트워크 트래픽의 상세 액세스 제어를 제공합니다.

이 기능은 다음과 같은 Azure 서비스 및 지역에서 __미리 보기__ 상태로 제공됩니다.

__Azure Storage__: WestCentralUS, WestUS2, NorthCentralUS, SouthCentralUS, CentralUS, EastUS2 합니다.

미리 보기에 대한 최신 알림은 [Azure Virtual Network 업데이트](https://azure.microsoft.com/updates/?product=virtual-network) 페이지를 참조하세요.

> [!NOTE]  
> 미리 보기 중에는 가상 네트워크 서비스 엔드포인트 정책이 일반 공급 릴리스에 있는 기능과 동일한 수준의 가용성 및 안정성을 제공하지 못할 수도 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 Microsoft Azure 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="key-benefits"></a>주요 이점

Virtual Network 서비스 엔드포인트 정책은 다음과 같은 이점을 제공합니다.

- __Azure Services에 대한 Virtual Network 트래픽의 보안 향상__

  [네트워크 보안 그룹에 대한 Azure 서비스 태그](https://aka.ms/servicetags)를 사용하면 가상 네트워크 아웃바운드 트래픽을 특정 Azure 서비스로 제한할 수 있습니다. 그러나 이렇게 하면 해당 Azure 서비스의 모든 리소스에 대한 트래픽이 허용됩니다. 
  
  엔드포인트 정책에서 이제 가상 네트워크 아웃바운드 액세스를 특정 Azure 리소스로 제한할 수 있습니다. 이렇게 하면 가상 네트워크에서 액세스하는 데이터를 보호하기 위해 더 세부적인 보안 제어가 가능합니다. 

- __Azure 서비스 트래픽을 필터링하기 위한 확장성 및 고가용성 정책__

   엔드포인트 정책은 서비스 엔드포인트를 통한 가상 네트워크로부터의 Azure 서비스 트래픽을 필터링하기 위해 수평적으로 확장 가능하며 가용성이 높은 솔루션을 제공합니다. 추가 오버헤드 없이 가상 네트워크에서 이 트래픽에 대한 중앙 네트워크 어플라이언스를 유지 관리할 수 있습니다.

## <a name="configuration"></a>구성

- 가상 네트워크 트래픽을 특정 Azure 서비스 리소스로 제한하는 엔드포인트 정책을 구성할 수 있습니다. 미리 보기에서는 Azure Storage에 대 한 엔드포인트 정책을 지원합니다. 
- 엔드포인트 정책은 가상 네트워크의 서브넷에 구성됩니다. 정책에 나열된 모든 Azure 서비스에 대해 정책을 적용하려면 서브넷에서 서비스 엔드포인트를 사용하도록 설정해야 합니다.
- 엔드포인트 정책을 사용하면 resourceID 형식을 통해 특정 Azure 서비스 리소스를 허용 목록에 지정할 수 있습니다. 구독 또는 리소스 그룹의 모든 리소스에 대한 액세스를 제한할 수 있습니다. 특정 리소스에 대한 액세스를 제한할 수도 있습니다. 
- 기본적으로 엔드포인트가 있는 서브넷에 정책이 연결되지 않은 경우 서비스의 모든 리소스에 액세스할 수 있습니다. 정책을 해당 서브넷에 구성한 후에는 정책에서 지정한 리소스만 해당 서브넷의 컴퓨팅 인스턴스에서 액세스할 수 있습니다. 특정 서비스에 대해 다른 모든 리소스 액세스는 거부됩니다. 
- 서비스 엔드포인트가 구성된 지역의 Azure 서비스 리소스에 대한 트래픽을 필터링할 수 있습니다. 기본적으로 다른 지역의 서비스 리소스에 대한 액세스가 허용됩니다. 

  > [!NOTE]  
  > 서비스 엔드포인트 지역의 Azure 리소스에 대한 가상 네트워크 아웃바운드 액세스를 완전히 잠그려면 [서비스 태그](security-overview.md#service-tags)를 사용하여 서비스 엔드포인트 지역에 대한 트래픽만 허용하도록 구성된 네트워크 보안 그룹 규칙도 필요합니다.

- 서브넷에 여러 정책을 적용할 수 있습니다. 여러 정책을 서브넷에 연결할 때는, 동일한 서비스에 대해 이 정책 간에 지정한 리소스로의 가상 네트워크 트래픽이 허용됩니다. 정책에서 지정하지 않은 모든 다른 서비스 리소스에 대한 액세스는 거부됩니다. 

  > [!NOTE]  
  > 정책은 가상 네트워크로부터 나열된 서비스 리소스에 대한 액세스만 허용합니다. 특정 리소스를 정책에 추가하면 서비스에 대한 다른 모든 트래픽이 자동으로 거부됩니다. 애플리케이션에 대한 모든 서비스 리소스 종속성이 정책에서 식별 및 나열 가능한지 확인합니다.

  > [!WARNING]  
  > 인프라 요구 사항에 따라 가상 네트워크(예: Azure HDInsight), Azure 서비스(예: Azure Storage)에 배포된 Azure 서비스. 엔드포인트 정책을 특정 리소스로 제한하면 가상 네트워크에 배포된 서비스에 대해 해당 인프라 리소스 액세스가 끊길 수 있습니다. 특정 서비스에 대한 내용은 [제한 사항](#limitations)을 참조하세요. 미리 보기 중에는 가상 네트워크에 배포된 관리 Azure 서비스에 대해 서비스 엔드포인트 정책이 지원되지 않습니다.

- Azure Storage: 
  -  저장소 계정의 Azure Resource Manager *resourceId*를 나열하여 액세스를 제한할 수 있습니다. Blob, 테이블, 큐, 파일 및 Azure Data Lake Storage Gen2 트래픽에 적용됩니다.

     예제에서는 Azure Storage 계정을 다음과 같이 엔드포인트 정책에 나열할 수 있습니다.
    
     특정 저장소 계정을 허용하려면         
     `subscriptions/subscriptionId/resourceGroups/resourceGroup/providers/Microsoft.Storage/storageAccounts/storageAccountName`
      
     구독 및 리소스 그룹에서 모든 계정을 허용하려면: `/subscriptions/subscriptionId/resourceGroups/resourceGroup`
     
     구독에서 모든 계정을 허용하려면: `/subscriptions/subscriptionId`
    
- Azure Resource Model을 사용하는 저장소 계정만 엔드포인트 정책에서 지정할 수 있습니다.  

  > [!NOTE]  
  > 클래식 저장소 계정에 대한 액세스는 엔드포인트 정책에서 차단됩니다.

- 나열된 계정의 기본 위치는 서브넷에 대해 서비스 엔드포인트의 지역 쌍 지역에 있어야 합니다. 

  > [!NOTE]  
  > 정책에서는 다른 지역의 서비스 리소스 지정이 허용됩니다. Azure 서비스에 대한 가상 네트워크 액세스는 지리 쌍 지역에 대해서만 필터링됩니다. 네트워크 보안 그룹이 Azure Storage에 대한 지리 쌍 지역으로 제한되지 않은 경우 가상 네트워크가 지리 쌍 지역 밖의 모든 스토리지 계정에 액세스할 수 있습니다.

- 기본 계정이 나열되면 RA-GRS 보조 액세스가 자동으로 허용됩니다. 
- 저장소 계정은 가상 네트워크와 동일하거나 다른 구독 또는 Azure Active Directory 테넌트에 있을 수 있습니다.  

## <a name="limitations"></a>제한 사항

- Azure Resource Manager 배포 모델을 통해 배포된 가상 네트워크에만 서비스 엔드포인트 정책을 배포할 수 있습니다.
- 가상 네트워크는 서비스 엔드포인트 정책은 동일한 지역에 있어야 합니다.
- 서비스 엔드포인트가 정책에 나열된 Azure 서비스에 대해 구성된 경우에만 서브넷에 서비스 엔드포인트 정책을 적용할 수 있습니다. 
- 온-프레미스 네트워크에서 Azure 서비스로 가는 트래픽에는 서비스 엔드포인트 정책을 사용할 수 없습니다.
- 인프라 요구 사항에 따라 Azure 서비스에 대한 종속성이 있는 관리 Azure 서비스의 서브넷에는 엔드포인트 정책이 적용되지 않습니다. 

  > [!WARNING]  
  > 인프라 요구 사항에 따라 가상 네트워크(예: Azure HDInsight), Azure 서비스(예: Azure Storage)에 배포된 Azure 서비스. 엔드포인트 정책을 특정 리소스로 제한하면 가상 네트워크에 배포된 Azure 서비스에 대해 해당 인프라 리소스 액세스가 끊길 수 있습니다.
  
  - 일부 Azure 서비스는 다른 컴퓨팅 인스턴스를 통해 서브넷에 배포할 수 있습니다. 아래 목록의 관리 서비스가 서브넷에 배포된 경우 엔드포인트 정책이 서브넷에 적용되지 않는지 확인합니다.
   
    - Azure HDInsight
    - Azure Batch(Azure Resource Manager)
    - Azure Active Directory Domain Services(Azure Resource Manager)
    - Azure Application Gateway(Azure Resource Manager)
    - Azure VPN Gateway(Azure Resource Manager)
    - Azure Firewall

  - 일부 Azure 서비스는 전용 서브넷에 배포됩니다. 미리 보기 중에는 아래 목록의 모든 서비스에서 엔드포인트 정책이 차단됩니다. 

     - Azure App Service Environment 
     - Azure Rediscache
     - Azure API Management
     - Azure SQL Managed Instance
     - Azure Active Directory Domain Services
     - Azure Application Gateway(클래식)
     - Azure VPN Gateway(클래식)

- Azure Storage: 클래식 스토리지 계정은 엔드포인트 정책에서 지원되지 않습니다. 정책은 기본적으로 모든 클래식 저장소 계정에 대한 액세스를 거부합니다. 애플리케이션이 Azure Resource Manager 및 클래식 저장소 계정에 액세스해야 하는 경우 해당 트래픽에 엔드포인트 정책을 사용해서는 안 됩니다. 

## <a name="nsgs-with-service-endpoint-policies"></a>서비스 엔드포인트 정책이 있는 NSG
- 기본적으로 NSG는 Azure 서비스로의 가상 네트워크 트래픽을 포함한 아웃바운드 인터넷 트래픽을 허용합니다.
- 모든 아웃바운드 인터넷 트래픽을 거부하고 특정 Azure 서비스에 대한 트래픽만 허용하려는 경우  

  1단계: *Azure 서비스 태그*를 사용하여 엔드포인트 지역의 Azure 서비스에 대한 아웃바운드 트래픽만 허용하도록 NSG를 구성합니다. 자세한 내용은 [NSG에 대한 서비스 태그](https://aka.ms/servicetags)를 참조하세요.
      
  예를 들어 지역 엔드포인트 지역으로만 액세스를 제한하는 네트워크 보안 그룹 규칙은 다음 예제와 비슷합니다.

  ```
  Allow AzureStorage.WestUS2,
  Allow AzureStorage.WestCentralUS,
  Deny all
  ```

  2단계: 특정 Azure 서비스 리소스에만 액세스하는 서비스 엔드포인트 정책을 적용합니다.

  > [!WARNING]  
  > 네트워크 보안 그룹이 가상 네트워크의 Azure 서비스 액세스를 엔드포인트 지역으로 제한하도록 구성되지 않은 경우 서비스 엔드포인트 정책을 적용하더라도 다른 지역의 서비스 리소스에 액세스할 수 있습니다.

## <a name="scenarios"></a>시나리오

- **피어링, 연결 또는 다중 가상 네트워크**: 피어링된 가상 네트워크에서 트래픽을 필터링하려면 엔드포인트 정책을 개별적으로 해당 가상 네트워크에 적용해야 합니다.
- **네트워크 어플라이언스 또는 Azure Firewall을 사용하여 인터넷 트래픽 필터링**: 엔드포인트에서 정책을 통해 Azure 서비스 트래픽을 필터링하고, 나머지 인터넷 또는 Azure 트래픽을 어플라이언스나 Azure Firewall을 통해 필터링합니다. 
- **Virtual Network에 배포된 Azure 서비스에서 트래픽 필터링**: 미리 보기 중에는 가상 네트워크에 배포된 관리되는 Azure 서비스에 대해 서비스 엔드포인트 정책이 지원되지 않습니다. 
 특정 서비스는 [제한 사항](#limitations)을 참조하세요.
- **온-프레미스에서 Azure 서비스로 트래픽 필터링**: 서비스 엔드포인트 정책은 정책과 연결된 서브넷으로부터의 트래픽에만 적용됩니다. 온-프레미스로부터의 특정 Azure 서비스 액세스를 허용하려면 트래픽을 네트워크 가상 어플라이언스나 방화벽을 통해 필터링해야 합니다.

## <a name="logging-and-troubleshooting"></a>로깅 및 문제 해결
서비스 엔드포인트 정책에 중앙 집중식 로깅을 사용할 수 없습니다. 서비스 진단 로그는 [서비스 엔드포인트 로깅](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting)을 참조하세요.

### <a name="troubleshooting-scenarios"></a>문제 해결 시나리오
- 엔드포인트 정책에 나열되지 않은 저장소 정책에 대한 액세스 허용
  - 네트워크 보안 그룹이 다른 지역의 Azure Storage 계정이나 인터넷에 대한 액세스를 허용할 수 있습니다.
  - 네트워크 보안 그룹은 모든 아웃바운드 인터넷 트래픽을 거부하고 특정 Azure Storage 지역에 대한 트래픽만 허용하도록 구성되어야 합니다. 자세한 내용은 네트워크 보안 그룹을 참조하세요.
- 엔드포인트 정책에 나열된 계정에 대한 액세스 거부 
  - 네트워크 보안 그룹 또는 방화벽 필터링이 액세스를 차단할 수 있음
  - 정책 제거/다시 적용 후 연결이 끊어짐
    - Azure 서비스가 가상 네트워크로부터 또는 엔드포인트를 경유한 액세스를 구성하도록 허용되었거나 리소스 기본 정책이 *모두 허용*으로 구성되었는지 유효성을 검사합니다.
      > [!NOTE]      
      > 엔드포인트 정책을 통해 액세스하려면 서비스 리소스가 가상 네트워크에 대해 보호되지 않아야 합니다. 그러나 보안 모범 사례에 따라 Azure Virtual Networks에서는 서비스 엔드포인트, 온-프레미스에서는 IP 방화벽 등, 같은 신뢰할 수 있는 네트워크에 대해 서비스 리소스를 보호하는 것이 좋습니다. 
  
    - 서비스 진단에서 엔드포인트를 통한 트래픽이 표시되는지 확인합니다.
    - 네트워크 보안 그룹 흐름 로그에 액세스가 표시되며 스토리지 로그에 서비스 엔드포인트를 통해 예상대로 액세스가 표시되는지 확인합니다.
    - Azure 지원에 문의하세요.
- 서비스 엔드포인트 정책에 나열되지 않은 계정에 대한 액세스 거부 
  - 네트워크 보안 그룹 또는 방화벽 필터링이 액세스를 차단할 수 있습니다. *Azure Storage* 서비스 태그가 엔드포인트 지역에 허용되었는지 확인합니다. 정책 제한은 [제한 사항](#limitations)을 참조하세요.
  예를 들어, 정책을 적용하면 클래식 저장소 계정은 액세스가 거부됩니다.
  - Azure 서비스가 가상 네트워크로부터 또는 엔드포인트를 경유한 액세스를 구성하도록 허용되었거나 리소스 기본 정책이 *모두 허용*으로 구성되었는지 유효성을 검사합니다.

## <a name="provisioning"></a>프로비전

가상 네트워크에 대한 쓰기 액세스 권한이 있는 사용자는 서브넷에서 서비스 엔드포인트 정책을 구성할 수 있습니다. Azure [기본 제공 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 특정 권한 할당에 대해 자세히 알아보세요.

가상 네트워크 및 Azure 서비스 리소스가 동일한 구독이나 다른 구독 또는 Azure Active Directory 테넌트에 있을 수 있습니다. 

## <a name="pricing-and-limits"></a>가격 책정 및 제한

서비스 엔드포인트 정책 사용에는 추가 비용이 없습니다. Azure 서비스(예: Azure Storage)의 현재 가격 책정 모델은 서비스 엔드포인트에서 현재 상태로 적용됩니다.

서비스 엔드포인트 정책에는 다음 제한이 적용됩니다. 

 |리소스 | 기본 제한 |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>다음 단계

- [가상 네트워크 서비스 엔드포인트정책을 구성](virtual-network-service-endpoint-policies-portal.md)하는 방법에 대한 자세한 내용
- [가상 네트워크 서비스 엔드포인트](virtual-network-service-endpoints-overview.md)에 대한 자세한 내용

