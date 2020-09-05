---
title: Advisor를 사용 하 여 운영 excellency 개선
description: Azure Advisor를 사용 하 여 Azure 구독에 대 한 운영 뛰어난 작업을 최적화 하 고 완성 합니다.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 036adb7e7d59bd78980c72b210ad41faea277d00
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258482"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Azure Advisor를 사용 하 여 뛰어난 운영 효율성 얻기

Azure Advisor에서 뛰어난 운영 상의 권장 사항은 다음에 도움이 될 수 있습니다. 
- 프로세스 및 워크플로 효율성.
- 리소스 관리 효율성.
- 배포 모범 사례. 

Advisor 대시보드의 **작동 가능한 작업** 탭에서 이러한 권장 사항을 가져올 수 있습니다.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Azure 문제가 사용자에 게 영향을 주는 경우 알림을 받을 Azure Service Health 경고 만들기

Azure 서비스 문제가 사용자에 게 영향을 줄 때 알림을 받을 수 있도록 Azure Service Health 경고를 설정 하는 것이 좋습니다. [Azure Service Health](https://azure.microsoft.com/features/service-health/) 는 Azure 서비스 문제의 영향을 받는 경우 개인 설정 된 지침 및 지원을 제공 하는 무료 서비스입니다. Advisor는 경고가 구성 되지 않은 구독을 식별 하 고 구성 하는 것을 권장 합니다.


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>최대 구독 제한에 도달 하지 않도록 저장소 계정 디자인

Azure 지역은 구독 당 최대 250 개의 저장소 계정을 지원할 수 있습니다. 이 한도에 도달 하면 해당 지역/구독 조합에서 저장소 계정을 만들 수 없습니다. Advisor는 구독을 확인 하 고 한도에 도달 하는 데 가까운 모든 지역/구독에 대해 저장소 계정을 덜 설계 하기 위한 권장 사항을 제공 합니다.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>필요할 때 Azure 클라우드 전문가에 대한 액세스 권한이 있는지 확인

업무상 중요 한 워크 로드를 실행 하는 경우 필요할 때 기술 지원에 액세스 하는 것이 중요 합니다. Advisor는 지원 계획에 기술 지원이 포함 되지 않은 잠재적 업무상 중요 한 구독을 식별 합니다. 기술 지원이 포함 된 옵션으로 업그레이드 하는 것이 좋습니다.

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>사용 되지 않는 내부 구성 요소를 제거 하려면 풀을 삭제 하 고 다시 만드십시오.

풀에서 사용 되지 않는 내부 구성 요소를 사용 하는 경우 향상 된 안정성 및 성능을 위해 풀을 삭제 하 고 다시 만듭니다.

## <a name="repair-invalid-log-alert-rules"></a>잘못 된 로그 경고 규칙 복구

Azure Advisor는 조건 섹션에 잘못 된 쿼리가 지정 된 경고 규칙을 검색 합니다. Azure Monitor에서 로그 경고 규칙을 만들고이 규칙을 사용 하 여 지정 된 간격으로 분석 쿼리를 실행할 수 있습니다. 쿼리 결과에 따라 경고를 트리거할 것인지 여부가 결정됩니다. 분석 쿼리는 참조 된 리소스, 테이블 또는 명령의 변경 내용 때문에 시간이 지남에 따라 무효화 될 수 있습니다. Advisor는 자동으로 사용 하지 않도록 설정할 수 없도록 경고 규칙의 쿼리를 수정 하 고 Azure에서 리소스의 모니터링 적용 범위를 확인 하는 것이 좋습니다. [경고 규칙 문제 해결에 대해 자세히 알아보세요.](https://aka.ms/aa_logalerts_queryrepair)

## <a name="use-azure-policy-recommendations"></a>Azure Policy 권장 사항 사용

Azure Policy는 정책을 만들고 할당 하 고 관리 하는 데 사용할 수 있는 Azure의 서비스입니다. 이러한 정책은 리소스에 규칙 및 효과를 적용 합니다. 다음 Azure Policy 권장 사항은 운영 excellency을 구현 하는 데 도움이 될 수 있습니다. 

**태그를 관리 합니다.** 이 정책은 리소스를 만들거나 업데이트할 때 지정된 태그 및 값을 추가하거나 바꿉니다. 수정 작업을 트리거하여 기존 리소스를 수정할 수 있습니다. 이 정책은 리소스 그룹의 태그를 수정 하지 않습니다.

**지역 규정 준수 요구 사항을 적용 합니다.** 이 정책을 사용하면 조직에서 리소스를 배포할 때 지정할 수 있는 위치를 제한할 수 있습니다. 

**배포에 대해 허용 되는 가상 컴퓨터 Sku를 지정 합니다.** 이 정책을 통해 조직에서 배포할 수 있는 가상 컴퓨터 SKU 집합을 지정할 수 있습니다.

***관리 디스크를 사용 하지 않는 감사 vm을*적용 합니다.**

***리소스 그룹에서 태그 상속을*사용 하도록 설정 합니다.** 이 정책은 리소스를 만들거나 업데이트할 때 상위 리소스 그룹의 지정된 태그 및 값을 추가하거나 바꿉니다. 수정 작업을 트리거하여 기존 리소스를 수정할 수 있습니다.

## <a name="no-validation-environment-enabled"></a>유효성 검사 환경을 사용하지 않음
Azure Advisor 현재 구독에서 유효성 검사 환경을 사용 하도록 설정 하지 않은 것을 확인 합니다. 호스트 풀을 만들 때 \" \" \" 속성 탭에서 유효성 검사 환경에 대해 아니요를 선택 했습니다 \" . 유효성 검사 환경을 사용 하도록 설정 된 호스트 풀이 하나 이상 있으면 잠재적인 문제를 조기에 검색 하 여 Windows 가상 데스크톱 서비스 배포를 통해 비즈니스 연속성을 보장 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/virtual-desktop/create-validation-host-pool)

## <a name="ensure-production-non-validation-environment-to-benefit-from-stable-functionality"></a>안정적인 기능의 이점을 얻기 위해 프로덕션 (유효성 검사 아님) 환경 확인
Azure Advisor는 유효성 검사 환경이 활성화 된 호스트 풀이 너무 많은 것을 감지 합니다. 유효성 검사 환경이 용도에 가장 적합 하 게 하려면 유효성 검사 환경에서 하나 이상의 호스트 풀이 필요 하지 않습니다. 유효성 검사 환경을 사용 하도록 설정 된 호스트 풀과 사용 하지 않도록 설정 된 호스트 풀 간에 정상적인 균형을 유지 하 여 Windows 가상 데스크톱이 특정 업데이트를 제공 하는 다단계 배포의 이점을 최대한 활용할 수 있습니다. 이 문제를 해결 하려면 호스트 풀의 속성을 열고 \" \" 유효성 검사 환경 설정 옆에 있는 아니요를 선택 \" \" 합니다.

## <a name="enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources"></a>트래픽 분석을 사용하여 Azure 리소스에서 트래픽 패턴에 대한 인사이트를 볼 수 있음
트래픽 분석은 Azure의 사용자 및 애플리케이션 작업에 대한 가시성을 제공하는 클라우드 기반 솔루션입니다. 트래픽 분석은 NSG(Network Watcher 네트워크 보안 그룹) 흐름 로그를 분석하여 트래픽 흐름에 대한 인사이트를 제공합니다. 트래픽 분석을 사용하면 Azure 및 비 Azure 배포 전반에서 상위 토커를 확인하고, 사용자 환경에서 열린 포트, 프로토콜 및 악성 흐름을 조사하고, 네트워크 배포를 성능에 맞게 최적화할 수 있습니다. 10분 및 60분의 처리 간격으로 흐름 로그를 처리하여 트래픽에 대한 분석을 더 빠르게 할 수 있습니다. Azure 리소스에 대 한 트래픽 분석를 사용 하도록 설정 하는 것이 좋습니다. 


## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 안정성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
