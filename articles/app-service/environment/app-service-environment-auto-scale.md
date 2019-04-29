---
title: 자동 크기 조정 및 App Service Environment v1 - Azure
description: 자동 크기 조정 및 App Service Environment
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 29a639142395c43fea06c1d6d18909b3c9f33b86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769417"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>자동 크기 조정 및 App Service Environment v1

> [!NOTE]
> 이 문서는 ASE(App Service Environment) v1에 관한 내용입니다.  사용하기가 더 쉽고 더 강력한 인프라에서 실행되는 최신 버전의 App Service Environment가 있습니다. 새 버전에 대한 자세한 내용은 [App Service Environment 소개](intro.md)를 참조하세요.
> 

Azure App Service 환경은 *자동 크기 조정*을 지원합니다. 메트릭 또는 일정에 따라 개별 작업자 풀을 자동 크기 조정할 수 있습니다.

![작업자 풀에 대한 자동 크기 조정 옵션입니다.][intro]

자동 크기 조정은 App Service 환경을 예산 및 부하 프로필에 맞게 자동으로 증대 및 축소하여 리소스 사용을 최적화합니다.

## <a name="configure-worker-pool-autoscale"></a>작업자 풀 자동 크기 조정 구성
작업자 풀의 **설정** 탭에서 자동 크기 조정 기능에 액세스할 수 있습니다.

![작업자 풀의 설정 탭입니다.][settings-scale]

여기서 인터페이스는 App Service 계획을 확장할 때와 동일한 환경이므로 상당히 친숙할 것입니다. 

![수동 크기 조정 설정입니다.][scale-manual]

또한 자동 크기 조정 프로필을 구성할 수 있습니다.

![자동 크기 조정 설정입니다.][scale-profile]

자동 크기 조정 프로필은 크기에 대한 제한을 설정하는 데 유용합니다. 이렇게 하면 하한 크기 값(1)을 설정하여 일관된 성능 환경을 유지하고 상한(2)을 설정하여 예측 가능한 지출 한도를 정할 수 있습니다.

![프로필의 크기 조정 설정입니다.][scale-profile2]

프로필을 정의한 후 자동 크기 조정 규칙을 추가하여 프로필에서 정의한 범위 안에서 작업자 풀의 인스턴스 수를 늘리거나 줄일 수 있습니다. 자동 크기 조정 규칙은 메트릭을 기반으로 합니다.

![크기 조정 규칙입니다.][scale-rule]

 작업자 풀 또는 프런트 엔드 메트릭을 사용하여 자동 크기 조정 규칙을 정의할 수 있습니다. 이러한 메트릭은 리소스 블레이드 그래프에서 모니터링하거나 그에 대한 알림을 설정할 수 있는 동일한 메트릭입니다.

## <a name="autoscale-example"></a>자동 크기 조정 예제
App Service 환경의 자동 크기 조정은 시나리오를 살펴보면서 가장 잘 설명할 수 있습니다.

이 문서에서는 자동 크기 조정을 설정할 때 필요한 모든 고려 사항에 대해 설명합니다. 이 문서에서는 App Service 환경에서 호스팅되는 App Service 환경의 자동 크기 조정에 반영하게 되는 모든 상호 작용에 대해 설명합니다.

### <a name="scenario-introduction"></a>시나리오 소개
Frank는 한 기업의 시스템 관리자로, 자신이 관리하는 워크로드의 일부를 App Service 환경으로 마이그레이션했습니다.

App Service Environment는 다음과 같이 수동 크기 조정으로 구성되었습니다.

* **프런트 엔드:** 3
* **작업자 풀 1**: 10
* **작업자 풀 2**: 5
* **작업자 풀 3**: 5

작업자 풀 1은 프로덕션 워크로드에, 작업자 풀 2 및 작업자 풀 3은 QA(품질 보증) 및 개발 워크로드에 사용됩니다.

QA 및 개발에 대한 App Service 계획은 수동 확장 하도록 구성됩니다. App Service 계획 프로덕션은 부하 및 트래픽을 다양하게 처리하도록 자동 크기 조정으로 설정됩니다.

Frank는 애플리케이션에 대해 잘 알고 있습니다. 직원이 사무실에 있는 동안 사용하는 LOB(기간 업무) 애플리케이션이므로 사용이 많은 피크 시간은 오전 9시와 오후 6시 사이라는 것을 알고 있습니다. 사용자가 퇴근한 후에는 사용량이 줍니다. 피크 시간 이외에도 사용자가 모바일 디바이스 또는 가정용 PC를 사용하여 원격으로 앱에 액세스할 수 있으므로 여전히 일부 부하가 있습니다. 프로덕션 App Service 계획은 이미 다음 규칙을 기준으로 CPU 사용량에 따라 자동 크기 조정을 수행하도록 구성되어 있습니다.

![LOB 앱에 대한 특정 설정입니다.][asp-scale]

| **자동 크기 조정 프로필 - 주중 - App Service 계획** | **자동 크기 조정 프로필 - 주말 - App Service 계획** |
| --- | --- |
| **이름:** 주중 프로필 |**이름:** 주말 프로필 |
| **배율 기준:** 일정 및 성능 규칙 |**배율 기준:** 일정 및 성능 규칙 |
| **프로필:** 평일 |**프로필:** 주말 |
| **유형:** 되풀이 |**유형:** 되풀이 |
| **대상 범위:** 5~20개 인스턴스 |**대상 범위:** 3~10개 인스턴스 |
| **요일:** 월요일, 화요일, 수요일, 목요일, 금요일 |**요일:** 토요일, 일요일 |
| **시작 시간:** 오전 9시 |**시작 시간:** 오전 9시 |
| **표준 시간대:** UTC-08 |**표준 시간대:** UTC-08 |
|  | |
| **자동 크기 조정 규칙(확장)** |**자동 크기 조정 규칙(확장)** |
| **리소스:** 프로덕션(App Service Environment) |**리소스:** 프로덕션(App Service Environment) |
| **메트릭:** CPU % |**메트릭:** CPU % |
| **작업:** 60% 초과 |**작업:** 80% 초과 |
| **기간:** 5분 |**기간:** 10분 |
| **시간 집계:** 평균 |**시간 집계:** 평균 |
| **작업:** 개수 2씩 증가 |**작업:** 개수 1씩 증가 |
| **정지(분):** 15 |**정지(분):** 20 |
|  | |
| **자동 크기 조정 규칙(축소)** |**자동 크기 조정 규칙(축소)** |
| **리소스:** 프로덕션(App Service Environment) |**리소스:** 프로덕션(App Service Environment) |
| **메트릭:** CPU % |**메트릭:** CPU % |
| **작업:** 30% 미만 |**작업:** 20% 미만 |
| **기간:** 10분 |**기간:** 15분 |
| **시간 집계:** 평균 |**시간 집계:** 평균 |
| **작업:** 개수 1씩 감소 |**작업:** 개수 1씩 감소 |
| **정지(분):** 20 |**정지(분):** 10 |

### <a name="app-service-plan-inflation-rate"></a>App Service 계획 인플레이션 속도
자동 크기 조정으로 구성된 App Service 계획은 시간당 최대 속도에서 작업을 수행합니다. 이 속도는 자동 크기 조정 규칙에서 제공한 값을 기준으로 산출됩니다.

작업자 풀의 크기 조정 변경은 즉각적으로 적용되는 것이 아니므로 App Service Environment에서 *App Service 계획 인플레이션 속도* 를 이해하고 산출하는 것이 중요합니다.

App Service 계획 인플레이션 속도는 다음과 같이 산출됩니다.

![App Service 계획 인플레이션 속도 계산입니다.][ASP-Inflation]

프로덕션 App Service 계획의 주중 프로필에 대한 자동 크기 조정 - 확장 규칙을 기준으로 합니다.

![자동 크기 조정 - 확장 규칙에 따른 주중의 App Service 계획 인플레이션 속도입니다.][Equation1]

프로덕션 App Service 계획의 주말 프로필에 대한 자동 크기 조정 - 확장 규칙을 기준으로, 수식은 다음과 같이 산출됩니다.

![자동 크기 조정 - 확장 규칙에 따른 주말의 App Service 계획 인플레이션 속도입니다.][Equation2]

이 값은 축소 작업에 대해서도 산출할 수 있습니다.

프로덕션 App Service 계획의 주중 프로필에 대한 자동 크기 조정 - 축소 규칙을 기준으로, 이 작업은 다음과 같습니다.

![자동 크기 조정 - 축소 규칙에 따른 주중의 App Service 계획 인플레이션 속도입니다.][Equation3]

프로덕션 App Service 계획의 주말 프로필에 대한 자동 크기 조정 - 축소 규칙을 기준으로, 수식은 다음과 같이 산출됩니다.  

![자동 크기 조정 - 축소 규칙에 따른 주말의 App Service 계획 인플레이션 속도입니다.][Equation4]

프로덕션 App Service 계획이 주중에는 최대 8개 인스턴스/시간 속도로, 주말에는 4개 인스턴스/시간의 속도로 확장될 수 있습니다. 주중에는 최대 4개 인스턴스/시간의 속도로, 주말에는 최대 6개 인스턴스/시간의 속도로 인스턴스를 해제할 수 있습니다.

여러 App Service 계획을 작업자 풀에서 호스트하는 경우 작업자 풀에서 호스트되는 모든 App Service 계획에 대한 인플레이션 속도의 합계로 *총 인플레이션 속도* 를 산출해야 합니다.

![작업자 풀에서 호스팅되는 여러 App Service 계획에 대한 총 인플레이션 속도 계산입니다.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>App Service 계획 인플레이션 속도를 사용하여 작업자 풀 자동 크기 조정 규칙 정의
자동 크기 조정으로 구성된 App Service 계획을 호스팅하는 작업자 풀은 용량의 버퍼를 할당해야 합니다. 버퍼는 자동 크기 조정 작업이 필요에 따라 App Service 계획을 늘리고 축소하도록 허용합니다. 최소 버퍼 크기는 산출된 총 App Service 계획 인플레이션 속도입니다.

App Service 환경 크기 조정 작업을 적용하려면 다소 시간이 걸리므로, 이러한 변경은 크기 조정 작업이 진행 중인 동안 발생할 수 있는 추가적인 수요 변화에 대처할 수 있어야 합니다. 이 대기 시간을 수용하려면 산출된 총 App Service 계획 인플레이션 속도를 각 자동 크기 조정 작업에 대해 추가된 최소 인스턴스 수로 사용하는 것이 좋습니다.

이 정보를 통해 Frank는 다음 자동 크기 조정 프로필과 규칙을 정의할 수 있습니다.

![LOB 예제에 대한 자동 크기 조정 프로필 규칙입니다.][Worker-Pool-Scale]

| **자동 크기 조정 프로필 – 주중** | **자동 크기 조정 프로필 – 주말** |
| --- | --- |
| **이름:** 주중 프로필 |**이름:** 주말 프로필 |
| **배율 기준:** 일정 및 성능 규칙 |**배율 기준:** 일정 및 성능 규칙 |
| **프로필:** 평일 |**프로필:** 주말 |
| **유형:** 되풀이 |**유형:** 되풀이 |
| **대상 범위:** 13~25개 인스턴스 |**대상 범위:** 6~15개 인스턴스 |
| **요일:** 월요일, 화요일, 수요일, 목요일, 금요일 |**요일:** 토요일, 일요일 |
| **시작 시간:** 오전 7시 |**시작 시간:** 오전 9시 |
| **표준 시간대:** UTC-08 |**표준 시간대:** UTC-08 |
|  | |
| **자동 크기 조정 규칙(확장)** |**자동 크기 조정 규칙(확장)** |
| **리소스:** 작업자 풀 1 |**리소스:** 작업자 풀 1 |
| **메트릭:** WorkersAvailable |**메트릭:** WorkersAvailable |
| **작업:** 8 미만 |**작업:** 3 미만 |
| **기간:** 20분 |**기간:** 30분 |
| **시간 집계:** 평균 |**시간 집계:** 평균 |
| **작업:** 개수 8씩 증가 |**작업:** 개수 3씩 증가 |
| **정지(분):** 180 |**정지(분):** 180 |
|  | |
| **자동 크기 조정 규칙(축소)** |**자동 크기 조정 규칙(축소)** |
| **리소스:** 작업자 풀 1 |**리소스:** 작업자 풀 1 |
| **메트릭:** WorkersAvailable |**메트릭:** WorkersAvailable |
| **작업:** 8 초과 |**작업:** 3 초과 |
| **기간:** 20분 |**기간:** 15분 |
| **시간 집계:** 평균 |**시간 집계:** 평균 |
| **작업:** 개수 2씩 감소 |**작업:** 개수 3씩 감소 |
| **정지(분):** 120 |**정지(분):** 120 |

프로필에서 정의한 대상 범위는 App Service 계획에 대한 프로필에서 정의한 최소 인스턴스 수 + 버퍼로 산출됩니다.

최대 범위는 작업자 풀에서 호스팅되는 모든 App Service 계획에 대한 모든 최대 범위의 합계가 됩니다.

강화 규칙의 증가 수는 강화를 위한 App Service 계획 인플레이션 속도에 1배 이상으로 설정되어야 합니다.

수 축소는 축소에 대한 App Service 계획 인플레이션 속도의 1/2배 ~ 1배 사이에서 조정할 수 있습니다.

### <a name="autoscale-for-front-end-pool"></a>프런트 엔드 풀에 대한 자동 크기 조정
프런트 엔드 자동 크기 조정에 대한 규칙은 작업자 풀에 대한 것보다 간단합니다. 기본적으로  
측정 시간을 확인하고 휴지 시간 타이머는 App Service 계획에 대한 크기 조정 작업이 즉각적이지 않다는 것을 고려해야 합니다.

이 시나리오에서 Frank는 프런트엔드의 CPU 사용률이 80%에 도달하면 오류 비율이 증가한다는 사실을 알고 있으며 다음과 같이 인스턴스를 증대하는 자동 확장 규칙을 설정합니다.

![프런트 엔드 풀에 대한 자동 크기 조정 설정입니다.][Front-End-Scale]

| **자동 크기 조정 프로필 – 프런트 엔드** |
| --- |
| **이름:** 자동 크기 조정 - 프런트 엔드 |
| **배율 기준:** 일정 및 성능 규칙 |
| **프로필:** 매일 |
| **유형:** 되풀이 |
| **대상 범위:** 3~10개 인스턴스 |
| **요일:** 매일 |
| **시작 시간:** 오전 9시 |
| **표준 시간대:** UTC-08 |
|  |
| **자동 크기 조정 규칙(확장)** |
| **리소스:** 프런트 엔드 풀 |
| **메트릭:** CPU % |
| **작업:** 60% 초과 |
| **기간:** 20분 |
| **시간 집계:** 평균 |
| **작업:** 개수 3씩 증가 |
| **정지(분):** 120 |
|  |
| **자동 크기 조정 규칙(축소)** |
| **리소스:** 작업자 풀 1 |
| **메트릭:** CPU % |
| **작업:** 30% 미만 |
| **기간:** 20분 |
| **시간 집계:** 평균 |
| **작업:** 개수 3씩 감소 |
| **정지(분):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
