---
title: Azure Advisor를 사용 하 여 Azure 구독에 대한 운영 excellency 개선
description: Advisor를 사용 하 여 Azure 구독에 대한 운영 편의성을 최적화 하 고 최대한 활용 하세요.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443067"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Azure Advisor로 뛰어난 운영

Azure Advisor operational 최고 권장 사항은 고객에 게 프로세스 및 워크플로 효율성, 리소스 관리 효율성 및 배포 모범 사례를 제공 하는 데 도움이 됩니다. Advisor 대시보드의 **운영 뛰어난 작업** 탭에서 advisor를 통해 이러한 권장 사항을 확인할 수 있습니다.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure 문제가 영향을 미칠 때 알림을 받도록 Azure Service Health 경고 만들기

Azure 문제가 영향을 미칠 때 알림을 받도록 Azure Service Health 경고를 만드는 것이 좋습니다. [Azure Service Health](https://azure.microsoft.com/features/service-health/)는 Azure 서비스 문제가 영향을 미칠 경우 개인화된 지침과 지원을 제공하는 무료 서비스입니다. Advisor는 경고가 구성되지 않은 구독을 식별하고 구독을 생성하도록 권장합니다.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>최대 구독 제한에 도달 하지 않도록 저장소 계정 디자인

Azure 지역은 구독 당 최대 250 개의 저장소 계정을 지원할 수 있습니다. 제한에 도달 하면 해당 지역/구독 조합에서 저장소 계정을 더 이상 만들 수 없습니다. Advisor는 최대한도에 도달 하는 데 가까운 모든 저장소 계정에 대한 설계를 위해 구독과 surface 권장 사항을 확인 합니다.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>필요할 때 Azure 클라우드 전문가에 대한 액세스 권한이 있는지 확인

중요 비즈니스용 워크로드를 실행할 때 필요한 경우 기술 지원에 대한 액세스 권한이 있어야 합니다. Advisor는 해당 지원 플랜에 포함된 기술 지원이 없는 잠재적 중요 비즈니스용 구독을 식별하고 기술 지원을 포함하는 옵션으로 업그레이드할 것을 권장합니다.

## <a name="repair-invalid-log-alert-rules"></a>잘못 된 로그 경고 규칙 복구

Azure Advisor는 해당 조건 섹션에 잘못 된 쿼리가 지정 된 경고 규칙을 검색 합니다. 로그 경고 규칙은 Azure Monitor에서 생성되며 지정된 간격으로 분석 쿼리를 실행하는 데 사용됩니다. 쿼리 결과에 따라 경고를 트리거할 것인지 여부가 결정됩니다. 시간이 지나 참조되는 리소스, 테이블 또는 명령이 변경되면 분석 쿼리가 유효하지 않게 될 수 있습니다. Advisor는 자동 사용 하지 않도록 설정 하 고 Azure에서 리소스의 모니터링 범위를 확인 하는 것을 방지 하기 위해 경고 규칙의 쿼리를 수정 하는 것을 권장 합니다. [경고 규칙 문제 해결에 대한 자세한 정보](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Azure Policy 사용 모범 사례 준수

Azure Policy는 정책을 만들고, 할당하고, 관리하는 데 사용하는 Azure의 서비스입니다. 이러한 정책은 리소스에 대해 다양한 규칙과 효과를 적용합니다. 운영 excellency를 실현 하는 데 도움이 되는 Azure 정책 권장 사항은 다음과 같습니다. 
1. Azure Policy를 사용 하 여 태그 관리:이 정책은 리소스가 만들어지거나 업데이트 될 때 지정 된 태그 및 값을 추가 하거나 바꿉니다. 기존 리소스는 수정 작업을 트리거하여 수정할 수 있습니다. 또한 리소스 그룹의 태그는 수정 되지 않습니다.
2. Azure Policy를 사용 하 여 지역 규정 준수 요구 사항 적용: 정책을 사용 하면 조직에서 리소스를 배포할 때 지정할 수 있는 위치를 제한할 수 있습니다. 
3. 배포할 수 있는 가상 머신 Sku 지정:이 정책을 사용 하면 조직에서 배포할 수 있는 가상 머신 Sku 집합을 지정할 수 있습니다.
4. Azure 정책을 사용 하 여 ' 관리 디스크를 사용 하지 않는 Vm 감사 ' 적용
5. Azure 정책을 사용 하 여 ' 리소스 그룹에서 태그 상속 ' 사용: 정책이 만들어지거나 업데이트 될 때 부모 리소스 그룹의 지정 된 태그 및 값을 추가 하거나 대체 합니다. 기존 리소스는 수정 작업을 트리거하여 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
