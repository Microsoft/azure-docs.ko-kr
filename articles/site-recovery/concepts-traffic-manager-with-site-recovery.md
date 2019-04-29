---
title: Azure Site Recovery를 사용한 Azure Traffic Manager | Microsoft Docs
description: 재해 복구 및 마이그레이션에 Azure Site Recovery를 사용한 Azure Traffic Manager를 사용하는 방법 설명
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 6c77cd43231d4596535c11564313a0fe90633cdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947807"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Azure Site Recovery를 사용한 Azure Traffic Manager

Azure Traffic Manager를 사용하면 애플리케이션 엔드포인트에 트래픽 분산을 제어할 수 있습니다. 엔드포인트는 Azure의 내부 또는 외부에서 호스팅되는 모든 인터넷 연결 서비스입니다.

Traffic Manager는 DNS(Domain Name System)를 사용하여 클라이언트 요청을 트래픽 라우팅 메서드 및 엔드포인트의 상태를 기반으로 가장 적절한 엔드포인트로 리디렉션합니다. Traffic Manager는 다양한 애플리케이션 요구와 자동 장애 조치(failover)에 맞는 [트래픽 라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md) 및 [엔드포인트 모니터링 옵션](../traffic-manager/traffic-manager-monitoring.md)을 제공합니다. 클라이언트는 선택한 엔드포인트에 직접 연결됩니다. Traffic Manager는 프록시 또는 게이트웨이가 아니며, 클라이언트와 서비스 간에 전달되는 트래픽을 표시하지 않습니다.

이 문서에서는 Azure Site Recovery의 강력한 재해 복구 및 마이그레이션 기능으로 Azure Traffic Monitor의 지능형 라우팅을 결합하는 방법을 설명합니다.

## <a name="on-premises-to-azure-failover"></a>온-프레미스에서 Azure로 장애 조치(failover)

첫 번째 시나리오에서는 **A사**의 모든 애플리케이션 인프라가 온-프레미스 환경에서 실행 중이라고 가정합니다. **A사**는 비즈니스 연속성 및 규정 준수를 위해 Azure Site Recovery를 사용하여 자사의 애플리케이션을 보호하기로 결정했습니다.

**A사**는 공용 엔드포인트를 사용하여 애플리케이션을 실행 중이며 재해 발생 시 원활하게 트래픽을 Azure로 리디렉션하는 기능을 원합니다. A사는 Azure Traffic Manager의 [우선 순위](../traffic-manager/traffic-manager-configure-priority-routing-method.md) 트래픽 라우팅 메서드를 사용하면 이 장애 조치(Failover) 패턴을 쉽게 구현할 수 있습니다.

설정 방법은 다음과 같습니다.
- **A사**에서 [Traffic Manager 프로필](../traffic-manager/traffic-manager-create-profile.md)을 만듭니다.
- **A사**에서 **우선 순위** 라우팅 메서드를 활용하여 온-프레미스에 사용할 **기본** 엔드포인트와 Azure로 **장애 조치(failover)** 에 사용할 엔드포인트를 만듭니다. **기본** 엔드포인트는 우선 순위 1에 할당되고 **장애 조치(failover)** 엔드포인트는 우선 순위 2에 할당됩니다.
- **기본** 엔드포인트는 Azure 외부에 호스트되므로 엔드포인트가 [외부](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) 엔드포인트로 생성됩니다.
- Azure Site Recovery를 사용하면 장애 조치(failover) 전에는 Azure 사이트에서 가상 컴퓨터 또는 애플리케이션이 실행되지 않습니다. 따라서 **장애 조치(failover)** 엔드포인트도 **외부** 엔드포인트로 생성됩니다.
- 기본적으로 사용자 트래픽은 엔드포인트에 가장 높은 우선 순위가 연결되므로 온-프레미스 애플리케이션으로 전송됩니다. **기본** 엔드포인트가 정상이면 Azure로 트래픽이 전송되지 않습니다.

![장애 조치(Failover) 전 온-프레미스-Azure](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

재해 발생 시 A사는 Azure로 [장애 조치(failover)](site-recovery-failover.md)를 트리거하여 Azure에서 애플리케이션을 복구할 수 있습니다. Azure Traffic Manager는 **기본** 엔드포인트가 정상이 아닌 것을 감지하면 자동으로 DNS 응답에 **장애 조치(failover)** 를 사용하며, 사용자는 Azure에 복구된 애플리케이션에 연결하게 됩니다.

![장애 조치(Failover) 후 온-프레미스-Azure](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

비즈니스 요구 사항에 따라 **A사**는 더 높은 또는 더 낮은 [검색 빈도](../traffic-manager/traffic-manager-monitoring.md)를 선택하여 재해 발생 시 온-프레미스와 Azure 간에 전환함으로써 가동 중지 시간을 최소화할 수 있습니다.

재해가 억제되면 **A사**는 Azure Site Recovery를 사용하여 Azure에서 온-프레미스 환경([VMware](vmware-azure-failback.md) 또는 [Hyper-V](hyper-v-azure-failback.md))으로 장애 복구(failback)할 수 있습니다. Traffic Manager는 **기본** 엔드포인트가 다시 정상으로 돌아온 것을 확인하면 자동으로 DNS 응답에서 **기본** 엔드포인트를 활용합니다.

## <a name="on-premises-to-azure-migration"></a>온-프레미스에서 Azure로 마이그레이션

재해 복구 외에도, Azure Site Recovery는 [Azure로 마이그레이션](migrate-overview.md)을 지원합니다. Azure Site Recovery의 강력한 테스트 장애 조치(failover) 기능을 사용하면 고객은 온-프레미스 환경에 영향을 주지 않고 Azure에서 애플리케이션 성능을 평가할 수 있습니다. 고객은 마이그레이션 준비가 완료되면 전체 워크로드를 한꺼번에 마이그레이션할 수도 있고 점진적으로 조금씩 마이그레이션하여 확장할 수도 있습니다.

Azure Traffic Manager의 [가중치](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) 라우팅 메서드를 사용하여 트래픽의 대부분은 온-프레미스 환경으로 전송하고 들어오는 트래픽의 일부는 Azure로 전송할 수 있습니다. 이 방식은 워크로드를 조금씩 Azure로 마이그레이션하면서 Azure에 할당된 가중치를 계속 높여갈 수 있으므로 성능을 평가하는 데 도움이 됩니다.

예를 들어 **B사**는 단계별 마이그레이션을 선택하여 애플리케이션 환경의 일부만 이동하고 나머지는 온-프레미스에 유지할 수 있습니다. 초기 단계에는 대부분의 환경이 온-프레미스에 있고, 온-프레미스 환경에 더 많은 가중치가 할당됩니다. Traffic Manager는 사용 가능한 엔드포인트에 할당된 가중치에 따라 엔드포인트를 반환합니다.

![온-프레미스에서 Azure로 마이그레이션](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

마이그레이션하는 동안 두 엔드포인트가 활성화되고 대부분의 트래픽이 온-프레미스 환경으로 전달됩니다. 마이그레이션이 진행됨에 따라 Azure의 엔드포인트에 더 많은 가중치를 할당할 수 있으며, 마지막으로 마이그레이션이 끝나면 온-프레미스 엔드포인트를 비활성화할 수 있습니다.

## <a name="azure-to-azure-failover"></a>Azure에서 Azure로 장애 조치(failover)

이 예에서는 **C사**의 모든 애플리케이션 인프라가 Azure에서 실행 중이라고 가정합니다. **C사**는 비즈니스 연속성 및 규정 준수를 위해 Azure Site Recovery를 사용하여 자사의 애플리케이션을 보호하기로 결정했습니다.

**C사**는 공용 엔드포인트를 사용하여 애플리케이션을 실행 중이며 재해 발생 시 원활하게 트래픽을 다른 Azure 지역으로 리디렉션하는 기능을 원합니다. [우선 순위](../traffic-manager/traffic-manager-configure-priority-routing-method.md) 트래픽 라우팅 메서드를 사용하여 **C사**는 이러한 장애 조치(Failover) 패턴을 쉽게 구현할 수 있습니다.

설정 방법은 다음과 같습니다.
- **C사**에서 [Traffic Manager 프로필](../traffic-manager/traffic-manager-create-profile.md)을 만듭니다.
- **C사**에서 **우선 순위** 라우팅 메서드를 활용하여 원본 지역(Azure 동아시아)에 사용할 **기본** 엔드포인트와 복구 지역(동남 아시아)으로 **장애 조치(failover)** 에 사용할 엔드포인트를 만듭니다. **기본** 엔드포인트는 우선 순위 1에 할당되고 **장애 조치(failover)** 엔드포인트는 우선 순위 2에 할당됩니다.
- **기본** 엔드포인트가 Azure에 호스트되므로 엔드포인트는 [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) 엔드포인트일 수 있습니다.
- Azure Site Recovery를 사용하면 장애 조치(failover) 전에는 복구 Azure 사이트에서 가상 컴퓨터 또는 애플리케이션이 실행되지 않습니다. 따라서 **장애 조치(failover)** 엔드포인트가 [외부](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) 엔드포인트로 생성될 수 있습니다.
- 기본적으로 사용자 트래픽은 엔드포인트에 가장 높은 우선 순위가 연결되므로 원본 지역(동아시아) 애플리케이션으로 전송됩니다. **기본** 엔드포인트가 정상이면 복구 지역으로 트래픽이 전송되지 않습니다.

![장애 조치(failover) 전 Azure-Azure](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

재해 발생 시 **C사**는 [장애 조치(failover)](azure-to-azure-tutorial-failover-failback.md)를 트리거하여 복구 Azure 지역에서 애플리케이션을 복구할 수 있습니다. Azure Traffic Manager는 기본 엔드포인트가 정상이 아닌 것을 감지하면 자동으로 DNS 응답에 **장애 조치(failover)** 를 사용하며, 사용자는 복구 Azure 지역(동남 아시아)에 복구된 애플리케이션에 연결하게 됩니다.

![장애 조치(failover) 후 Azure-Azure](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

비즈니스 요구 사항에 따라 **C사**는 더 높은 또는 더 낮은 [검색 빈도](../traffic-manager/traffic-manager-monitoring.md)를 선택하여 재해 발생 시 원본 지역과 복구 지역 간에 전환함으로써 가동 중지 시간을 최소화할 수 있습니다.

재해가 억제되면 **C사**는 Azure Site Recovery를 사용하여 복구 Azure 지역에서 원본 Azure 지역으로 장애 복구(failback)할 수 있습니다. Traffic Manager는 **기본** 엔드포인트가 다시 정상으로 돌아온 것을 확인하면 자동으로 DNS 응답에서 **기본** 엔드포인트를 활용합니다.

## <a name="protecting-multi-region-enterprise-applications"></a>다중 지역 엔터프라이즈 애플리케이션 보호

글로벌 기업은 종종 지역 요구를 수용하도록 애플리케이션을 조정하여 사용자 환경을 개선합니다. 애플리케이션을 지역화하고 대기 시간을 줄이면 애플리케이션 인프라를 여러 지역에 걸쳐 분할할 수 있습니다. 또한 기업은 특정 영역에서 현지 데이터 법률의 제한을 받으며 애플리케이션 인프라의 일부를 지역 경계 내부에 격리하도록 선택합니다.  

예를 들어 **D사**에서 독일과 나머지 국가에 서비스를 따로 제공하기 위해 애플리케이션 엔드포인트를 분할했다고 가정해 봅시다. **D사**에서 Azure Traffic Manager의 [지리적](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) 라우팅 메서드를 사용하여 이렇게 설정합니다. 독일에서 시작되는 트래픽은 **엔드포인트 1**로 전달되고 독일 외부에서 시작되는 트래픽은 **엔드포인트 2**로 전달됩니다.

이 설정의 문제는 **엔드포인트 1**이 어떤 이유로 중지되면 트래픽이 **엔드포인트 2**로 리디렉션되지 않는다는 것입니다. 독일에서 시작된 트래픽이 엔드포인트 상태에 관계없이 계속해서 **엔드포인트 1**로 전달되므로 사용자들이 **D사**의 애플리케이션에 액세스하지 않고 떠나게 됩니다. 마찬가지로, **엔드포인트 2**가 오프라인이 되면 트래픽이 **엔드포인트 1**로 리디렉션되지 않습니다.

![다중 지역 애플리케이션 전](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

이 문제를 방지하고 애플리케이션 복원력을 보장하기 위해 **D사**에서 Azure Site Recovery에 [중첩 Traffic Manager 프로필](../traffic-manager/traffic-manager-nested-profiles.md)을 사용합니다. 중첩 프로필 설정에서는 트래픽이 개별 엔드포인트 대신 다른 Traffic Manager 프로필로 전달됩니다. 이 설정의 작동 원리는 다음과 같습니다.
- **D사**에서 개별 엔드포인트에 지리적 라우팅을 사용하는 대신 Traffic Manager 프로필에 지리적 라우팅을 사용합니다.
- 각 자식 Traffic Manager 프로필은 **우선 순위** 라우팅과 기본 및 복구 엔드포인트를 사용하며, 따라서 중첩 **우선 순위**는 **지리적** 라우팅 내에서 라우팅됩니다.
- 애플리케이션 복원력을 높이기 위해 재해 발생 시 각 워크로드 배포에서는 Azure Site Recovery를 사용하여 복구 영역에 장애 조치(failover)합니다.
- 부모 Traffic Manager는 DNS 쿼리를 받으면 사용 가능한 엔드포인트를 사용하여 쿼리에 응답하는 관련 자식 Traffic Manager로 전달됩니다.

![다중 지역 애플리케이션 후](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

예를 들어 독일 중부의 엔드포인트에서 장애가 발생하면 독일 북동부로 신속하게 복구할 수 있습니다. 새 엔드포인트는 독일에서 시작된 트래픽을 처리하여 가동 중지 시간을 최소화합니다. 마찬가지로 유럽 서부의 엔드포인트에서 정전이 발생할 경우 애플리케이션 워크로드를 북유럽에 복구하여 문제를 해결할 수 있으며, DNS를 처리하는 Azure Traffic Manager는 사용 가능한 엔드포인트로 리디렉션됩니다.

필요한 만큼 영역 및 엔드포인트 조합을 포함하도록 위의 설정을 확장할 수 있습니다. Traffic Manager는 최대 10단계의 중첩 프로필을 허용하며 중첩 구성 내에서 루프를 허용하지 않습니다.

## <a name="recovery-time-objective-rto-considerations"></a>RTO(복구 시간 목표) 고려 사항

대부분의 조직에서 DNS 레코드를 추가 또는 수정하는 일은 별도의 팀에서 또는 조직 외부 사람을 통해 처리합니다. 이렇게 하면 DNS 레코드를 변경하기가 매우 어렵습니다. DNS 인프라를 관리하는 다른 팀 또는 조직에서 DNS 레코드를 업데이트하는 데 걸리는 시간은 조직마다 다르며 애플리케이션의 RTO에 영향을 줍니다.

Traffic Manager를 활용하면 DNS 업데이트에 필요한 작업을 미리 로드할 수 있습니다. 실제 장애 조치(failover) 시에 수동 작업 또는 스크립트 작업이 필요 없습니다. 이 방식은 재해 발생 시 신속하게 전환하고(따라서 RTO가 낮아지고) 시간이 오래 걸리는 DNS 변경 오류를 방지하는 데 도움이 됩니다. 뿐만 아니라 Traffic Manager를 사용하면 장애 복구(failback)가 자동화되며, 사용하지 않을 경우 별도로 관리해야 합니다.

기본 또는 빠른 간격 상태 검사를 통해 올바른 [검색 간격](../traffic-manager/traffic-manager-monitoring.md)을 설정하면 장애 조치(failover) 중 RTO를 크게 낮추고 가동 중지 시간을 줄일 수 있습니다.

또한 Traffic Manager 프로필의 DNS TTL(Time-to-Live) 값을 최적화할 수 있습니다. TTL은 클라이언트에서 DNS 항목을 캐시하는 값입니다. 레코드의 경우 DNS는 TTL 범위 내에서 두 번 쿼리되지 않습니다. DNS 레코드마다 TTL이 연결됩니다. 이 값을 줄이면 Traffic Manager에 더 많은 DNS 쿼리가 전달되지만 작동 중단을 더 빠르게 발견하여 RTO를 줄일 수 있습니다.

클라이언트와 권한 있는 DNS 서버 사이에 있는 DNS 확인자 수가 증가하면 클라이언트에서 경험하는 TTL은 증가하지 않습니다. DNS 확인자는 TTL을 '카운트 다운'하며 레코드가 캐시된 후에 경과된 시간을 반영하는 TTL 값만 전달합니다. 이렇게 하면 체인의 DNS 확인자 수에 관계없이 TTL 후 클라이언트에서 DNS 레코드가 새로 고침됩니다.

## <a name="next-steps"></a>다음 단계
- Traffic Manager [라우팅 메서드](../traffic-manager/traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.
- [중첩 Traffic Manager 프로필](../traffic-manager/traffic-manager-nested-profiles.md)에 대해 자세히 알아봅니다.
- [엔드포인트 모니터링](../traffic-manager/traffic-manager-monitoring.md)에 대해 자세히 알아보세요.
- 애플리케이션 장애 조치(failover)를 자동화하는 [복구 계획](site-recovery-create-recovery-plans.md)에 대해 자세히 알아봅니다.
