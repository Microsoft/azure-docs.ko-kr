---
title: 클라우드 서비스 할당 실패 문제 해결 | Microsoft Docs
description: Azure에서 Cloud Services 배포 시 할당 실패 문제 해결
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: d1f24c3661a23496d1873f12ce46083bf5258269
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61435511"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Azure에서 Cloud Services 배포 시 할당 실패 문제 해결
## <a name="summary"></a>요약
클라우드 서비스에 인스턴스를 배포하거나 새 웹 또는 작업자 역할 인스턴스를 추가할 때 Microsoft Azure는 계산 리소스를 할당합니다. 이러한 작업을 수행하면서 Azure 구독 제한에 도달하기 전에 오류를 수신하는 경우도 있습니다. 이 문서는 일부 일반적인 할당 오류의 이유를 설명하고 가능한 수정을 제안합니다. 서비스 배포를 계획하는 사용자에게 이 정보가 유용할 수 있습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>배경 – 할당의 작동 원리
Azure 데이터 센터의 서버는 클러스터로 분할되어 있습니다. 여러 클러스터에서 새 클라우드 서비스 할당 요청을 시도합니다. 첫 번째 인스턴스가 클라우드 서비스(스테이징 또는 프로덕션)에 배포되면 해당 클라우드 서비스가 클러스터에 고정됩니다. 이후 클라우드 서비스에 대한 모든 추가 배포는 동일한 클러스터에서 발생합니다. 이 문서에서는 이것을 “클러스터에 고정된”이라고 하겠습니다. 아래 다이어그램 1은 여러 클러스터에 시도되는 정상적인 할당의 사례를 보여 줍니다. 다이어그램 2는 클라우드 서비스 CS_1이 호스트되는 클러스터 2에 고정된 할당의 사례를 보여 줍니다.

![할당 다이어그램](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>할당 오류가 발생하는 이유
할당 요청이 클러스터에 고정되면 사용 가능한 리소스 풀이 클러스터로 제한되기 때문에 유휴 리소스를 찾는 데 실패할 가능성이 높습니다. 할당 요청이 클러스터에 고정되어 있지만 요청하는 리소스 유형이 이 클러스터에서 지원되지 않으면, 클러스터에 유휴 리소스가 있어도 사용자의 요청은 실패합니다. 아래 다이어그램 3은 유일한 후보 클러스터에 유휴 리소스가 없어서 고정된 할당이 실패하는 경우를 보여줍니다. 다이어그램 4는 클러스터에 유휴 리소스가 있지만 유일한 후보 클러스터가 요청한 VM 크기를 지원하지 않기 때문에 고정된 할당이 실패하는 경우를 보여 줍니다.

![고정된 할당 오류](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>클라우드 서비스에 대한 할당 실패 문제 해결
### <a name="error-message"></a>오류 메시지
다음과 같은 오류 메시지가 표시될 수 있습니다.

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>일반적인 문제
다음은 할당 요청이 단일 클러스터에 고정되도록 하는 일반적인 할당 시나리오입니다.

* 스테이징 슬롯에 배포하는 경우 - 클라우드 서비스가 두 슬롯 중 하나에 배포된 경우 전체 클라우드 서비스가 특정 클러스터에 고정됩니다.  이는 프로덕션 슬롯에 이미 배포가 있을 경우 프로덕션 슬롯과 동일한 클러스터에만 새 스테이징 배포를 할당할 수 있다는 의미입니다. 클러스터의 용량이 거의 찼을 경우 요청이 실패할 수 있습니다.
* 크기 조정 - 기존 클라우드 서비스에 새 인스턴스를 추가할 때는 동일한 클러스터에 할당해야 합니다.  작은 크기 조정 요청은 대부분 할당할 수 있지만 항상 가능한 것은 아닙니다. 클러스터의 용량이 거의 찼을 경우 요청이 실패할 수 있습니다.
* 선호도 그룹 - 클라우드 서비스가 선호도 그룹에 고정되어 있지 않는 한, 빈 클라우드 서비스에 새로 배포할 경우 해당 영역에 있는 클러스터의 패브릭에 의해서만 할당할 수 있습니다. 동일한 선호도 그룹에 대한 배포는 동일한 클러스터에서 시도됩니다. 클러스터의 용량이 거의 찼을 경우 요청이 실패할 수 있습니다.
* 선호도 그룹 vNet - 이전에는 Virtual Network가 영역이 아닌 선호도 그룹에 연결되었고 이러한 Virtual Network의 클라우드 서비스는 선호도 그룹 클러스터에 고정되었습니다. 이 가상 네트워크 형식에 대한 배포는 고정된 클러스터에서 시도됩니다. 클러스터의 용량이 거의 찼을 경우 요청이 실패할 수 있습니다.

## <a name="solutions"></a>솔루션
1. 새 클라우드 서비스에 다시 배포 - 이 솔루션은 플랫폼이 해당 영역의 모든 클러스터에서 선택할 수 있으므로 가장 성공률이 높습니다.

   * 새 클라우드 서비스에 작업을 배포합니다.  
   * 새 클라우드 서비스로의 트래픽을 가리키도록 CNAME 또는 A 레코드를 업데이트합니다.
   * 제로(0) 트래픽이 이전 사이트로 이동하면 이전 클라우드 서비스를 삭제할 수 있습니다. 이 솔루션은 가동 중지 시간 없이 발생합니다.
2. 프로덕션 및 스테이징 슬롯 삭제 - 이 솔루션은 기존 DNS 이름을 유지하지만 애플리케이션 가동 중지 시간이 발생합니다.

   * 기존 클라우드 서비스의 프로덕션 및 스테이징 슬롯을 삭제하여 클라우드 서비스를 비웁니다.
   * 기존 클라우드 서비스에서 새 배포를 만듭니다. 영역의 모든 클러스터에서 할당이 다시 시도됩니다. 클라우드 서비스가 선호도 그룹에 연결되지 않았는지 확인합니다.
3. 예약된 IP - 이 솔루션은 기존 IP 주소를 유지하지만 애플리케이션 가동 중지 시간이 발생합니다.  

   * Powershell을 사용하여 기존 배포에 대한 ReservedIP 만들기

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * 위의 #2에 따라 서비스의 CSCFG에 새 ReservedIP를 지정해야 합니다.
4. 새 배포에 대한 선호도 그룹 제거 - 선호도 그룹은 더 이상 권장되지 않습니다. 위의 #1에 따라 새 클라우드 서비스를 배포합니다. 클라우드 서비스가 선호도 그룹에 없는지 확인합니다.
5. 지역 Virtual Network로 변환 - [선호도 그룹에서 지역 VNet(Virtual Network)으로 마이그레이션하는 방법](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)을 참조하세요.
