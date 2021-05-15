---
title: Azure Site Recovery 모니터링에 대한 일반적인 질문
description: 기본 제공 모니터링 및 Azure Monitor(Log Analytics)를 사용하여 Azure Site Recovery 모니터링에 대한 일반적인 질문에 대한 답변을 받으세요.
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.openlocfilehash: 715fd3a30dcc37a26b07f2ca1c6a0f2ce168a23a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581716"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Site Recovery 모니터링에 대한 일반적인 질문

이 문서에서는 기본 제공 Site Recovery 모니터링 및 Azure Monitor(Log Analytics)를 사용한 Azure [Site Recovery](site-recovery-overview.md) 모니터링에 대한 일반적인 질문에 답변합니다.

## <a name="general"></a>일반

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>RPO 값이 사용 가능한 최신 복구 지점과 어떻게 다르게 로그되었나요?

Site Recovery는 다중 단계 비동기 프로세스를 사용하여 머신을 Azure에 복제합니다.

- 복제의 마지막 두 번째 단계에서 컴퓨터의 최근 변경 내용이 메타데이터와 함께 로그/캐시 스토리지 계정으로 복사됩니다.
- 복구 가능 지점을 식별하기 위한 태그와 함께 이러한 변경 내용이 대상 지역의 스토리지 계정/관리 디스크에 기록됩니다.
- 이제 Site Recovery는 머신에 대한 복구 가능 지점을 생성할 수 있습니다.
- 이때 지금까지 스토리지 계정에 업로드된 변경 내용에 대해 RPO가 충족되었습니다. 즉, 이 시점에서 머신 RPO는 복구 가능한 지점에 해당하는 타임스탬프로부터 경과된 기간과 동일합니다.
- 이제 Site Recovery는 스토리지 계정에서 업로드된 데이터를 선택하고 해당 컴퓨터용으로 만든 복제본 디스크에 적용합니다.
- 그런 다음 Site Recovery는 복구 지점을 생성하고, 이 지점을 장애 조치(Failover) 시의 복구에 사용할 수 있게 합니다.
- 따라서 사용 가능한 최신 복구 지점은 이미 처리되었으며 복제본 디스크에 적용된 최신 복구 지점에 해당하는 타임스탬프를 나타냅니다.


복제 원본 머신 또는 온-프레미스 인프라 서버의 잘못된 시스템 시간이 컴퓨팅 RPO 값을 왜곡합니다. 정확한 RPO 보고를 위해 시스템 클록이 모든 서버와 컴퓨터에서 정확한지 확인합니다.



## <a name="inbuilt-site-recovery-logging"></a>기본 제공 Site Recovery로깅


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>자격 증명 모음 인프라 보기의 VM 수가 복제된 항목에 표시된 총 수와 다른 이유는 무엇인가요?

자격 증명 모음 인프라 보기는 복제 시나리오에 따라 범위가 지정됩니다. 현재 선택한 복제 시나리오의 머신만 보기의 수에 포함됩니다. 또한 Azure에 복제되도록 구성된 VM만 계산됩니다. 장애 조치(Failover)된 머신 또는 온-프레미스 사이트로 다시 복제하는 머신은 보기에서 계산되지 않습니다.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Essentials의 복제 항목 수가 대시보드의 복제된 항목 총 수와 다른 이유는 무엇인가요?

초기 복제가 완료된 머신만 Essentials에 표시되는 수에 포함됩니다. 복제된 항목의 총 수에는 초기 복제가 현재 진행 중인 머신을 포함하여 자격 증명 모음의 모든 머신이 포함됩니다.

## <a name="azure-monitor-logging"></a>Azure Monitor 로깅


### <a name="how-often-does-site-recovery-send-resource-logs-to-azure-monitor-log"></a>Site Recovery에서 Azure Monitor Log에 리소스 로그를 얼마나 자주 보내나요? 

- AzureSiteRecoveryReplicationStats 및 AzureSiteRecoveryRecoveryPoints는 15분마다 보내집니다.  
- AzureSiteRecoveryReplicationDataUploadRate 및 AzureSiteRecoveryProtectedDiskDataChurn은 5분마다 보내집니다. 
- AzureSiteRecoveryJobs는 작업을 트리거하고 완료할 때 전송됩니다.
- AzureSiteRecoveryEvents는 이벤트가 생성될 때마다 전송됩니다. 
- AzureSiteRecoveryReplicatedItems는 환경이 변경될 때마다 전송됩니다. 일반적으로 변경 후 데이터 새로 고침 시간은 15분입니다. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Azure Monitor 로그에서 데이터는 얼마나 오래 유지되나요? 

기본적으로 데이터 보존 기간은 31일입니다. Log Analytics 작업 영역의 **사용량 및 예상 비용** 섹션에서 기간을 늘릴 수 있습니다. **데이터 보존** 을 클릭하고 범위를 선택합니다.

### <a name="whats-the-size-of-the-resource-logs"></a>리소스 로그의 크기는 얼마나 되나요? 

일반적으로 로그 크기는 15~20KB입니다. 


## <a name="next-steps"></a>다음 단계

Site Recovery 기본 제공 [모니터링](site-recovery-monitor-and-troubleshoot.md) 또는 [Azure Monitor](monitor-log-analytics.md)를 사용하여 모니터링하는 방법을 알아봅니다.


