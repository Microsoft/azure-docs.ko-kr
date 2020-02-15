---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 4643bb7f95e4fd1249d3ab6699c1f835c77f18fe
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198346"
---
| 리소스 | [소비 계획](../articles/azure-functions/functions-scale.md#consumption-plan) | [프리미엄 요금제](../articles/azure-functions/functions-scale.md#premium-plan) | [App Service 계획](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| 확장 | 이벤트 기반 | 이벤트 기반 | [수동/자동 크기 조정](../articles/app-service/manage-scale-up.md) | 
| 최대 인스턴스 | 200 | 100 | 10-20 |
|기본 [제한 시간](../articles/azure-functions/functions-scale.md#timeout) (분) |5 | 30 |30<sup>2</sup> |
|최대 [제한](../articles/azure-functions/functions-scale.md#timeout) 시간 (분) |10 | 무제한<sup>8</sup> | 바인딩되지 않은<sup>3</sup> |
| 최대 아웃 바운드 연결 (인스턴스당) | 600 활성 (총 1200) | unbounded | unbounded |
| 최대 요청 크기 (MB)<sup>4</sup> | 100 | 100 | 100 |
| 최대 쿼리 문자열 길이<sup>4</sup> | 4096 | 4096 | 4096 |
| 최대 요청 URL 길이<sup>4</sup> | 8192 | 8192 | 8192 |
| 인스턴스당 [Acu](../articles/virtual-machines/windows/acu.md) | 100 | 210-840 | 100-840 |
| 최대 메모리 (인스턴스당 GB) | 1.5 | 3.5-14 | 1.75 g-14 |
| 계획 당 함수 앱 |100 |100 |제한 없음<sup>5</sup> |
| [App Service 계획](../articles/app-service/overview-hosting-plans.md) | 100/ [지역](https://azure.microsoft.com/global-infrastructure/regions/) |리소스 그룹당 100 |리소스 그룹당 100 |
| 저장소<sup>6</sup> |1 GB |250GB |50-1000 GB |
| 앱당 사용자 지정 도메인</a> |500<sup>7</sup> |500 |500 |
| 사용자 지정 도메인 [SSL 지원](../articles/app-service/configure-ssl-bindings.md) |제한 없는 SNI SSL 연결 포함 | 제한 없는 SNI SSL 및 1 개의 IP SSL 연결 포함 |제한 없는 SNI SSL 및 1 개의 IP SSL 연결 포함 | 

<sup>1</sup> 다양 한 App Service 계획 옵션에 대 한 특정 한도는 [App Service 계획 제한](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)을 참조 하세요.  
<sup>2</sup> 기본적으로 App Service 계획의 함수 1.x 런타임에 대 한 제한 시간은 제한 되지 않습니다.  
<sup>3</sup> App Service 계획을 [Always On](../articles/azure-functions/functions-scale.md#always-on)으로 설정 해야 합니다. 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 지불 합니다.  
<sup>4</sup> [호스트에서](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config)이러한 한도를 설정 합니다.  
<sup>5</sup> 실제로 호스트할 수 있는 함수 앱 수는 앱의 작업, 컴퓨터 인스턴스의 크기 및 해당 리소스 사용률에 따라 달라 집니다.  
<sup>6</sup> 저장소 제한은 동일한 App Service 계획의 모든 앱에서 임시 저장소의 전체 콘텐츠 크기입니다. 소비 계획은 임시 저장소에 대 한 Azure Files를 사용 합니다.  
<sup>7</sup> 함수 앱이 [소비 계획](../articles/azure-functions/functions-scale.md#consumption-plan)에서 호스트 되는 경우에는 CNAME 옵션만 지원 됩니다. [프리미엄 계획](../articles/azure-functions/functions-scale.md#premium-plan) 또는 [App Service 계획](../articles/azure-functions/functions-scale.md#app-service-plan)의 함수 앱의 경우 CNAME 또는 a 레코드를 사용 하 여 사용자 지정 도메인을 매핑할 수 있습니다.  
<sup>8</sup> 최대 60 분까지 보장 됩니다.