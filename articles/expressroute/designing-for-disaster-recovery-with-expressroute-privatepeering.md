---
title: 'Azure Express 경로: 재해 복구를 위한 디자인'
description: 이 페이지에서는 Azure Express 경로를 사용 하는 동안 재해 복구에 대한 아키텍처 권장 사항을 제공 합니다.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: 726a014983c0da959d72b7976fef2ebb2c6e9b9e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076697"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Express 경로 개인 피어 링을 사용 하 여 재해 복구를 위한 디자인

Express 경로는 Microsoft 리소스에 대한 반송파 등급 개인 네트워크 연결을 제공 하는 고가용성을 위해 설계 되었습니다. 즉, Microsoft 네트워크 내에서 Express 경로 경로에 단일 실패 지점이 없습니다. Express 경로 회로의 가용성을 최대화 하기 위한 설계 고려 사항은 Express 경로를 [사용 하 여 고가용성을 위한 디자인][HA]을 참조 하세요.

그러나 Murphy의 인기 있는 말라을 사용 하는*경우에는 문제가 발생할 수 있습니다*.이 문서에서는 단일 express 경로 회로를 사용 하 여 해결할 수 있는 오류를 벗어난 솔루션에 집중할 수 있습니다. 즉,이 문서에서는 지역 중복 Express 경로 회로를 사용 하 여 재해 복구를 위해 강력한 백 엔드 네트워크 연결을 빌드하기 위한 네트워크 아키텍처 고려 사항을 살펴보겠습니다.

## <a name="need-for-redundant-connectivity-solution"></a>중복 연결 솔루션이 필요 합니다.

전체 지역 서비스 (Microsoft, 네트워크 서비스 공급자, 고객 또는 기타 클라우드 서비스 공급자)가 저하 되는 경우가 있습니다. 이러한 지역 전체의 서비스 영향에 대한 근본 원인은 자연 calamity입니다. 따라서 비즈니스 연속성 및 업무에 중요 한 응용 프로그램의 경우 재해 복구를 계획 하는 것이 중요 합니다.   

중요 업무용 응용 프로그램을 Azure 지역 또는 온-프레미스 또는 다른 곳에서 실행 하는지 여부에 관계 없이 다른 Azure 지역을 장애 조치 사이트로 사용할 수 있습니다. 다음 문서에서는 응용 프로그램 및 프런트 엔드 액세스 관점에서의 재해 복구에 대해 다룹니다.

- [엔터프라이즈급 재해 복구][Enterprise DR]
- [Azure Site Recovery를 사용한 SMB 재해 복구][SMB DR]

중요 업무용 작업을 위해 온-프레미스 네트워크와 Microsoft 간의 Express 경로 연결을 사용 하는 경우 재해 복구 계획에는 지역 중복 네트워크 연결도 포함 되어야 합니다. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>여러 Express 경로 회로를 사용 하는 문제

둘 이상의 연결을 사용 하 여 동일한 네트워크 세트를 상호 연결 하는 경우 네트워크 간에 병렬 경로가 도입 됩니다. 병렬 경로는 제대로 설계 되지 않은 경우 비대칭 라우팅이 발생할 수 있습니다. 경로에 상태 저장 엔터티 (예: NAT, 방화벽)가 있는 경우 비대칭 라우팅은 트래픽 흐름을 차단할 수 있습니다.  일반적으로 Express 경로 개인 피어 링 경로를 통해 NAT 또는 방화벽과 같은 상태 저장 엔터티를 통해 제공 되지 않습니다. 따라서 Express 경로 개인 피어 링을 통한 비대칭 라우팅은 반드시 트래픽 흐름을 차단 하지는 않습니다.
 
그러나 상태 저장 엔터티를 포함 하는지 여부에 관계 없이 지역 중복 병렬 경로에서 트래픽 부하를 분산 하는 경우 네트워크 성능이 일관 되지 않을 수 있습니다. 이 문서에서는 이러한 문제를 해결 하는 방법을 설명 하겠습니다.

## <a name="small-to-medium-on-premises-network-considerations"></a>중소 규모 온-프레미스 네트워크 고려 사항

다음 다이어그램에 나와 있는 예제 네트워크를 살펴보겠습니다. 이 예제에서 지역 중복 Express 경로 연결은 Contoso의 온-프레미스 위치와 Azure 지역의 Contoso VNet 간에 설정 됩니다. 다이어그램에서 녹색 실선은 기본 경로 (Express 경로 1을 통해)를 나타내고 점 하나는 기본 경로 (Express 경로 2를 통해)를 나타냅니다.

[![1]][1]

재해 복구를 위한 Express 경로 연결을 디자인 하는 경우 다음을 고려해 야 합니다.

- 지역 중복 Express 경로 회로 사용
- 다른 Express 경로 회로에 다양 한 서비스 공급자 네트워크 사용
- [고가용성][HA] 을 위해 각 express 경로 회로 디자인
- 고객 네트워크의 다른 위치에 있는 다른 Express 경로 회로 종료

기본적으로 모든 Express 경로 경로에 대해 동일한 경로를 보급 하는 경우 Azure는 동일한 ECMP (다중 경로) 라우팅을 사용 하 여 모든 Express 경로 경로에서 온-프레미스 바인딩된 트래픽을 부하 분산 합니다.

그러나 지역 중복 Express 경로 회로를 사용 하는 경우 네트워크 경로 (특히 네트워크 대기 시간)를 사용 하 여 서로 다른 네트워크 성능을 고려해 야 합니다. 정상적인 작업 중에 네트워크 성능을 일관 되 게 유지 하려면 최소 대기 시간을 제공 하는 Express 경로 회로를 사용 하는 것이 좋습니다.

다음 기술 중 하나를 사용 하 여 다른 Express 경로 회로에 대한 하나의 Express 경로 회로를 선호 하도록 Azure에 영향을 줄 수 있습니다 (효율성 순서로 나열 됨).

- 다른 Express 경로 회로와 비교 하 여 선호 하는 Express 경로 회로에서 보다 구체적인 경로를 보급 합니다.
- 가상 네트워크를 기본 설정 된 Express 경로 회로에 연결 하는 연결에서 더 높은 연결 가중치 구성
- 경로 앞에 경로를 추가 하 여 선호 하는 Express 경로 회로에서 경로를 더 길게 보급 합니다.

### <a name="more-specific-route"></a>보다 구체적인 경로

다음 다이어그램은 보다 구체적인 경로 광고를 사용 하는 Express 경로 선택 항목을 보여 줍니다. 예를 들어, Contoso 온-프레미스/24 IP 범위는 기본 경로 (Express 경로 1)를 통해 2/25 주소 범위로, 그리고 (Express 경로 2)를 통해/24로 보급 됩니다.

[![2]][2]

/25는/24와 비교 하 여 더 구체적 이므로 Azure는 표준 상태에서 Express 경로 1을 통해 10.1.11.0/24로 향하는 트래픽을 전송 합니다. Express 경로 1의 연결이 중단 되 면 VNet은 Express 경로 2를 통해서만 10.1.11.0/24 경로 알림을 볼 수 있습니다. 따라서이 오류 상태에서 대기 회로가 사용 됩니다.

### <a name="connection-weight"></a>연결 가중치

다음 스크린샷은 Azure Portal를 통해 Express 경로 연결의 무게를 구성 하는 방법을 보여 줍니다.

[![3]][3]

다음 다이어그램에서는 연결 가중치를 사용 하는 Express 경로 선택 경로 선택에 대해 설명 합니다. 기본 연결 가중치는 0입니다. 아래 예제에서는 Express 경로 1에 대한 연결의 가중치가 100로 구성 됩니다. VNet은 둘 이상의 Express 경로 회로를 통해 보급 된 경로 접두사를 받을 때 가장 높은 가중치가 있는 연결을 선호 합니다.

[![4]][4]

Express 경로 1의 연결이 중단 되 면 VNet은 Express 경로 2를 통해서만 10.1.11.0/24 경로 알림을 볼 수 있습니다. 따라서이 오류 상태에서 대기 회로가 사용 됩니다.

### <a name="as-path-prepend"></a>AS path 앞에 추가

다음 다이어그램에서는 AS path 앞에를 사용 하는 Express 경로 선택 경로 선택을 보여 줍니다. 다이어그램에서 Express 경로 1을 통한 경로 보급 알림은 eBGP의 기본 동작을 나타냅니다. Express 경로 2를 통한 경로 알림에서 온-프레미스 네트워크의 ASN이 경로 AS 경로에 추가로 붙습니다. 여러 Express 경로 회로를 통해 동일한 경로를 수신 하는 경우 (eBGP 경로 선택 프로세스에 따라) VNet은 최단 경로로 경로를 선호 합니다. 

[![5]][5]

Express 경로 1의 연결이 중단 되 면 VNet은 Express 경로 2를 통해서만 10.1.11.0/24 경로 알림을 볼 수 있습니다. Consequentially 경로는 관련성이 떨어질 수 있습니다. 따라서이 오류 상태에서 대기 회로가 사용 됩니다.

Azure에 영향을 주는 방법 중 하나를 사용 하 여 다른 사용자에 대한 Express 경로를 선호 하는 경우에도 온-프레미스 네트워크에서 Azure 바운드 트래픽에 대해 동일한 Express 경로 경로를 사용 하 여 비대칭 흐름을 방지 하도록 해야 합니다. 일반적으로 로컬 기본 설정 값은 온-프레미스 네트워크에 영향을 주므로 다른 Express 경로 회로를 선호 하는 데 사용 됩니다. 로컬 기본 설정은 iBGP (내부 BGP) 메트릭입니다. 로컬 기본 설정 값이 가장 높은 BGP 경로를 사용 하는 것이 좋습니다.

> [!IMPORTANT]
> 특정 Express 경로 회로를 사용 하는 경우 적극적으로 관리 하 고 장애 조치 (failover) 작업을 정기적으로 테스트 해야 합니다. 
> 

## <a name="large-distributed-enterprise-network"></a>대량 분산 엔터프라이즈 네트워크

분산 된 기업 네트워크가 많은 경우 Express 경로 회로가 여러 개 있을 가능성이 높습니다. 이 섹션에서는 추가 독립 실행형 회로를 필요로 하지 않고 활성-활성 Express 경로 회로를 사용 하 여 재해 복구를 설계 하는 방법을 알아보겠습니다. 

다음 다이어그램에 나와 있는 예제를 살펴보겠습니다. 이 예에서 Contoso는 두 개의 서로 다른 피어 링 위치에서 Express 경로 회로를 통해 두 개의 다른 Azure 지역에 두 개의 Contoso IaaS 배포에 연결 된 두 개의 온-프레미스 위치를 가집니다. 

[![6]][6]

재해 복구를 설계 하는 방법에 따라 지역별 지역 간 위치 간 (region1/region2 location2/location1) 트래픽이 라우팅되는 방법에 영향을 줍니다. 지역 간 위치 트래픽을 다르게 라우팅하는 서로 다른 두 가지 재해 아키텍처를 살펴보겠습니다.

### <a name="scenario-1"></a>시나리오 1

첫 번째 시나리오에서는 Azure 지역과 온-프레미스 네트워크 간의 모든 트래픽이 로컬 Express 경로 회로를 통해 안정 된 상태로 이동 하도록 재해 복구를 설계 합니다. 로컬 Express 경로 회로가 실패 한 경우 원격 Express 경로 회로는 Azure와 온-프레미스 네트워크 간의 모든 트래픽 흐름에 사용 됩니다.

시나리오 1은 다음 다이어그램에 설명 되어 있습니다. 다이어그램에서 녹색 선은 VNet1와 온-프레미스 네트워크 간의 트래픽 흐름 경로를 표시 합니다. 파란색 선은 VNet2와 온-프레미스 네트워크 간의 트래픽 흐름 경로를 표시 합니다. 실선은 안정적인 상태에서 원하는 경로를 나타내고 파선은 안정적인 상태 트래픽 흐름을 전달 하는 해당 하는 Express 경로 회로 오류의 트래픽 경로를 표시 합니다. 

[![7]][7]

Vnet에 영향을 주는 연결 가중치를 사용 하 여 온-프레미스 네트워크 바운드 트래픽에 대한 로컬 피어 링 위치에 대한 연결을 선호 하는 시나리오를 설계할 수 있습니다. 이 솔루션을 완료 하려면 대칭 역방향 트래픽 흐름을 확인 해야 합니다. BGP 라우터 (온-프레미스 쪽에서 Express 경로 회로가 종료 됨) 간의 iBGP 세션에서 로컬 기본 설정을 사용 하 여 Express 경로 회로를 선호 합니다. 이 솔루션은 다음 다이어그램에 설명 되어 있습니다. 

[![8]][8]

### <a name="scenario-2"></a>시나리오 2

시나리오 2는 다음 다이어그램에 설명 되어 있습니다. 다이어그램에서 녹색 선은 VNet1와 온-프레미스 네트워크 간의 트래픽 흐름 경로를 표시 합니다. 파란색 선은 VNet2와 온-프레미스 네트워크 간의 트래픽 흐름 경로를 표시 합니다. 안정적인 상태 (다이어그램의 실선)에서 Vnet 및 온-프레미스 위치 간의 모든 트래픽은 대부분의 경우 Microsoft 백본을 통해 이동 하 고, 장애 상태 (의 점선 줄)에 있는 온-프레미스 위치 간 상호 연결만을 통해 흐릅니다. 다이어그램)

[![9]][9]

이 솔루션은 다음 다이어그램에 설명 되어 있습니다. 앞에서 설명한 것 처럼 VNet 경로 선택에 영향을 주는 보다 구체적인 경로 (옵션 1) 또는 경로 앞에 추가 (옵션 2)를 사용 하 여 시나리오를 설계할 수 있습니다. Azure 바운드 트래픽에 대한 온-프레미스 네트워크 경로 선택에 영향을 미치는 경우 온-프레미스 위치 간 연결을 보다 낮은 수준으로 구성 해야 합니다. 로드하우 연결을 구성 하는 것은 온-프레미스 네트워크 내에서 사용 되는 라우팅 프로토콜에 따라 달라 집니다. IGP (OSPF 또는 is IS IS)와 함께 iBGP 또는 메트릭과 함께 로컬 기본 설정을 사용할 수 있습니다.

[![10]][10]


## <a name="next-steps"></a>다음 단계

이 문서에서는 Express 경로 회로 개인 피어 링 연결의 재해 복구를 설계 하는 방법에 대해 설명 했습니다. 다음 문서에서는 응용 프로그램 및 프런트 엔드 액세스 관점에서의 재해 복구에 대해 다룹니다.

- [엔터프라이즈급 재해 복구][Enterprise DR]
- [Azure Site Recovery를 사용한 SMB 재해 복구][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "중소 규모의 온-프레미스 네트워크 고려 사항"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "더 구체적인 경로를 사용 하 여 경로 선택에 영향"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "Azure Portal를 통해 연결 가중치 구성"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "연결 가중치를 사용 하 여 경로 선택에 영향"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png에 "영향을 주는 경로 선택에 사용 경로" 추가
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "개의 대량 분산 온-프레미스 네트워크 고려 사항"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "시나리오 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "활성-활성 express 경로 회로 솔루션 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "시나리오 2"
[10 개의]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "활성-활성 express 경로 회로 솔루션 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





