---
title: 서비스 할당량 및 제한 - Azure Batch | Microsoft Docs
description: 기본 Azure Batch 할당량, 한도 및 제약 조건에 대해 알아보고 할당량 증가를 요청하는 방법에 대해 알아봅니다.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c8a78acfa1f3e7332cee337ae1a82ee365fe356a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022973"
---
# <a name="batch-service-quotas-and-limits"></a>Batch 서비스 할당량 및 제한

다른 Azure 서비스와 마찬가지로 Batch 서비스와 관련하여 특정 리소스에 대한 제한이 있습니다. 이러한 제한 대부분은 Azure 구독 또는 계정 수준에서 적용되는 기본 할당량입니다. 이 문서는 그러한 기본값을 설명하고 할당량 증가를 요청하는 방법을 설명합니다.

Batch 워크로드를 설계 및 강화할 때 이 할당량에 주의합니다. 예를 들어, 풀이 지정한 컴퓨팅 노드의 대상 수에 도달하지 않는 경우 배치 계정의 주요 할당량 한도에 도달했을 수 있습니다.

단일 Batch 계정에서 여러 Batch 워크로드를 실행하거나 다른 Azure 지역이 아닌 동일한 구독에 있는 Batch 계정 간에 워크로드를 배포할 수 있습니다.

Batch에서 프로덕션 작업을 실행하려고 계획하는 경우, 위 기본값의 할당량 중 두 개 이상을 늘려야 할 수 있습니다. 할당량을 늘리려면 무료 온라인 [고객지원 요청](#increase-a-quota) 을 개설합니다.

## <a name="resource-quotas"></a>리소스 할당량

할당량은 신용 한도액일 뿐이며 용량을 보장하는 것은 아닙니다. 대규모 용량이 필요한 경우 Azure 지원에 문의하세요.

또한 할당량이 보장 되는 값은 아닙니다. 할당량은 Batch 서비스의 변경 내용에 따라 달라 지 며 할당량 값을 변경 하는 사용자 요청에 따라 달라질 수 있습니다.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>사용자 구독 모드에서 코어 할당량

풀 할당 모드가 **사용자 구독**으로 설정된 Batch 계정을 만든 경우 할당량이 다르게 적용됩니다. 이 모드에서 Batch VM 및 기타 리소스는 풀이 만들어질 때 구독에서 직접 만들어집니다. Azure Batch 코어 할당량은 이 모드에서 생성된 계정에는 적용되지 않습니다. 대신, 지역별 컴퓨팅 코어 및 기타 리소스에 대한 구독의 할당량이 적용됩니다. [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)에서 이러한 할당량에 대해 자세히 알아보세요.

## <a name="pool-size-limits"></a>풀 크기 제한

풀 크기 제한은 Batch 서비스에 의해 설정 됩니다. [리소스 할당량과](#resource-quotas)달리 이러한 값은 변경할 수 없습니다. 노드 간 통신 및 사용자 지정 이미지를 포함 하는 풀에만 표준 할당량과 다른 제한이 있습니다.

| **리소스** | **최대 제한** |
| --- | --- |
| **[노드 간 통신 사용 풀](batch-mpi.md)의 컴퓨팅 노드**  ||
| Batch 서비스 풀 할당 모드 | 100 |
| Batch 구독 풀 할당 모드 | 80 |
| **[관리 되는 이미지 리소스로 만든 풀](batch-custom-images.md)의 계산 노드**<sup>1</sup> ||
| 전용 노드 | 2000 |
| 우선 순위가 낮은 노드 | 1000 |

<sup>1</sup> 노드 간 통신을 사용하도록 설정되지 않은 풀입니다.

## <a name="other-limits"></a>기타 제한

일괄 처리 서비스에 의해 설정 된 추가 제한입니다. [리소스 할당량과](#resource-quotas)달리 이러한 값은 변경할 수 없습니다.

| **리소스** | **최대 제한** |
| --- | --- |
| 컴퓨팅 노드당 [동시 작업](batch-parallel-node-tasks.md) | 4 x 노드 코어 수 |
| [애플리케이션](batch-application-packages.md) | 20 |
| 애플리케이션당 애플리케이션 패키지 | 40 |
| 풀 당 응용 프로그램 패키지 | 10 |
| 최대 작업 수명 | 180일<sup>1</sup> |
| 계산 노드당 [탑재](virtual-file-mount.md) 수 | 10 |

<sup>1</sup> 태스크의 최대 수명(태스크가 작업에 추가되는 시점부터 완료되는 시점까지)은 180일입니다. 완료 된 작업은 7 일 동안 지속 됩니다. 최대 수명 내에 완료 되지 않은 태스크에 대한 데이터는 액세스할 수 없습니다.

## <a name="view-batch-quotas"></a>Batch 할당량 보기

[Azure Portal][portal]에서 Batch 계정 할당량을 확인 합니다.

1. 포털에서 **Batch 계정** 을 선택한 다음 관심 있는 Batch 계정을 선택합니다.
1. 배치 계정의 메뉴에서 **할당량**을 선택합니다.
1. Batch 계정에 현재 적용된 할당량을 표시합니다.

    ![Batch 계정 할당량][account_quotas]

## <a name="increase-a-quota"></a>할당량 증가

[Azure Portal][portal]를 사용 하 여 Batch 계정 또는 구독에 대한 할당량 증가를 요청 하려면 다음 단계를 수행 합니다. 할당량 증가 유형은 Batch 계정의 풀 할당 모드에 따라 다릅니다. 할당량 증가를 요청 하려면 할당량을 늘릴 VM 시리즈를 포함 해야 합니다. 할당량 증가가 적용 되는 경우 모든 Vm 시리즈에 적용 됩니다.

### <a name="increase-cores-quota-in-batch"></a>일괄 처리에서 코어 할당량 증가 

1. 포털 대시보드에서 **도움말 + 지원** 타일을 선택하거나 포털 오른쪽 위 모서리에 있는 물음표( **?** )를 선택합니다.
1. **새 기본 지원 요청** > **기본**을 클릭합니다.
1. **기본 사항**에서
   
    a. **문제 유형** > **서비스 및 구독 제한 (할당량)**
   
    b. 구독을 선택합니다.
   
    다. **할당량 유형** > **배치**
      
    **다음**을 선택합니다.
    
1. **세부 정보**에서 다음을 수행합니다.
      
    a. **세부 정보 제공**에서 위치, 할당량 유형 및 Batch 계정을 지정 합니다.
    
    ![일괄 처리 할당량 증가][quota_increase]

    할당량 유형은 다음과 같습니다.

    * **Batch 계정 당**  
        전용 및 우선 순위가 낮은 코어가 포함 된 단일 Batch 계정에 해당 하는 값 및 작업 및 풀 수입니다.
        
    * **지역별**  
        지역의 모든 Batch 계정에 적용 되는 값으로, 구독 당 지역별 Batch 계정 수를 포함 합니다.

    낮은 우선 순위 할당량은 모든 VM 시리즈에서 단일 값입니다. 제한 된 Sku가 필요한 경우 **우선 순위가 낮은 코어** 를 선택 하 고 요청할 VM 제품군을 포함 해야 합니다.

    b. [비즈니스 영향][support_sev]에 따라 **심각도** 를 선택 합니다.

    **다음**을 선택합니다.

1. **연락처 정보**에서
   
    a. **기본 연락 방법**을 선택합니다.
   
    b. 필수 연락처 세부 정보를 확인하고 입력합니다.
   
    **만들기** 를 선택 하 여 지원 요청을 제출 합니다.

지원 요청을 제출하면 Azure 지원 팀에서 연락을 드릴 것입니다. 할당량 요청은 몇 분 내에 완료 되거나 영업일의 최대 2 일 이내에 완료 될 수 있습니다.

## <a name="related-quotas-for-vm-pools"></a>VM 풀에 대한 관련 할당량

Azure 가상 네트워크에 배포된 Virtual Machine 구성의 Batch 풀은 추가 Azure 네트워킹 리소스를 자동으로 할당합니다. 가상 네트워크의 50개 풀 노드 각각에 대해 다음 리소스가 필요합니다.

* [네트워크 보안 그룹](../virtual-network/security-overview.md#network-security-groups) 하나
* 하나의 [공용 IP 주소](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* 하나의 [부하 분산 장치](../load-balancer/load-balancer-overview.md)

이러한 리소스는 Batch 풀을 만들 때 제공되는 가상 네트워크가 포함된 구독에 할당됩니다. 이러한 리소스는 구독의 [리소스 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md)으로 제한됩니다. 가상 네트워크에서 대형 풀 배포를 계획하는 경우 이러한 리소스에 대한 구독의 할당량을 확인합니다. 필요한 경우 **도움말 + 지원**을 선택하여 Azure Portal의 증가를 요청합니다.


## <a name="related-topics"></a>관련 항목
* [Azure Portal을 사용하여 Azure Batch 계정 만들기](batch-account-create-portal.md)
* [Azure Batch 기능 개요](batch-api-basics.md)
* [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
