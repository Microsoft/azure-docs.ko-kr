---
title: 'Azure Express 경로: 고가용성을 위한 디자인'
description: 이 페이지에서는 Azure Express 경로를 사용 하는 동안 고가용성을 위한 아키텍처 권장 사항을 제공 합니다.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4c3c6ae5fbdd91e6e44438be7fef2a3a91564a34
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076672"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Express 경로를 사용 하 여 고가용성을 위한 디자인

Express 경로는 Microsoft 리소스에 대한 반송파 등급 개인 네트워크 연결을 제공 하는 고가용성을 위해 설계 되었습니다. 즉, Microsoft 네트워크 내에서 Express 경로 경로에 단일 실패 지점이 없습니다. 가용성을 최대화 하기 위해 Express 경로 회로의 고객 및 서비스 공급자 세그먼트도 고가용성을 위해 설계 되어야 합니다. 이 문서에서는 먼저 Express 경로를 사용 하 여 강력한 네트워크 연결을 빌드하기 위한 네트워크 아키텍처 고려 사항을 살펴보고 Express 경로 회로의 고가용성을 향상 시키는 데 도움이 되는 미세 조정 기능을 살펴보겠습니다.


## <a name="architecture-considerations"></a>아키텍처 고려 사항

다음 그림은 express 경로 회로의 가용성을 극대화 하기 위해 Express 경로 회로를 사용 하 여 연결 하는 권장 방법을 보여 줍니다.

 [![1]][1]

고가용성을 위해 종단 간 네트워크 전체에서 Express 경로 회로의 중복성을 유지 하는 것이 중요 합니다. 즉, 온-프레미스 네트워크 내에서 중복성을 유지 해야 하 고 서비스 공급자 네트워크 내에서 중복성을 손상 시 키 지 않아야 합니다. 최소한의 중복성을 유지 하는 것은 단일 네트워크 오류 지점을 방지 하는 것을 의미 합니다. 네트워크 장치에 대한 중복 된 전원 및 냉각 기능을 사용 하면 고가용성을 더욱 향상 시킬 수 있습니다.

### <a name="first-mile-physical-layer-design-considerations"></a>첫 번째 마일 물리적 계층 설계 고려 사항

 동일한 CPE (고객 프레미스 장비)에서 Express 경로 회로의 기본 및 보조 연결을 모두 종료 하는 경우 온-프레미스 네트워크 내에서 고가용성을 손상 시킬 수 있습니다. 또한 서로 다른 네트워크에서 두 개의 연결을 종료 하거나 파트너 네트워크 내에서 두 개의 연결을 병합 하 여 동일한 CPE 포트를 통해 기본 및 보조 연결을 모두 구성 하는 경우 파트너를 강제로 적용 하 게 됩니다. 네트워크 세그먼트 에서도 고가용성을 손상 시킬 수 있습니다. 이 손상은 다음 그림에 나와 있습니다.

[![2]][2]

반면에 여러 지리적 위치에서 Express 경로 회로의 기본 및 보조 연결을 종료 하면 연결의 네트워크 성능이 손상 될 수 있습니다. 여러 지리적 위치에서 종료 되는 주 연결 및 보조 연결에서 트래픽이 적극적으로 부하 분산 되는 경우 두 경로 간의 네트워크 대기 시간에 큰 차이가 발생 하는 경우 네트워크가 최적이 아닌 네트워크를 사용할 수 있습니다. 성능도. 

지역 중복 디자인 고려 사항은 Express 경로를 [사용 하 여 재해 복구 디자인][DR]을 참조 하세요.

### <a name="active-active-connections"></a>활성-활성 연결

Microsoft 네트워크는 Express 경로 회로와 활성-활성 모드의 기본 및 보조 연결을 작동 하도록 구성 됩니다. 그러나 경로 알림을 통해 Express 경로 회로의 중복 연결이 활성-수동 모드에서 작동 하도록 강제할 수 있습니다. 더 구체적인 경로 및 BGP AS 경로 앞에 추가 하는 것은 다른 경로에 대해 하나의 경로를 선호 하는 데 사용 되는 일반적인 기술입니다.

고가용성을 향상 시키려면 Express 경로 회로와 활성-활성 모드의 연결을 모두 작동 하는 것이 좋습니다. 연결이 활성-활성 모드에서 작동 하도록 하는 경우 Microsoft 네트워크는 흐름에 따라 연결을 통해 트래픽의 부하를 분산 합니다.

활성-수동 모드에서 Express 경로 회로의 기본 및 보조 연결을 실행 하면 활성 경로에 오류가 발생 하 여 연결에 실패 하는 위험이 발생 합니다. 장애 조치 (failover)에 실패 하는 일반적인 원인은 수동 연결의 활성 관리와 오래 된 경로를 보급 하는 수동 연결의 부족입니다.

또는 활성-활성 모드에서 Express 경로 회로의 기본 및 보조 연결을 실행 하는 경우에는 Express 경로 연결 실패로 인해 흐름이 실패 하 고 다시 라우팅됩니다. 따라서 활성-활성 모드는 MTTR (평균 복구 시간)를 개선 하는 데 큰 도움이 됩니다.

### <a name="nat-for-microsoft-peering"></a>Microsoft 피어 링에 대한 NAT 

Microsoft 피어 링은 공용 엔드포인트 간 통신을 위해 설계 되었습니다. 따라서 일반적으로 온-프레미스 개인 엔드포인트은 Microsoft 피어 링을 통해 통신 하기 전에 고객 또는 파트너 네트워크에서 공용 IP를 사용 하 여 네트워크 주소 변환 (NATed으로 변환 됩니다. 활성-활성 모드에서 기본 및 보조 연결을 모두 사용 한다고 가정 합니다 .이 경우 NAT는 Express 경로 연결 중 하나에서 오류 발생 후 복구 하는 속도에 영향을 줍니다. 다음 그림에는 두 가지 NAT 옵션이 나와 있습니다.

[![3]][3]

옵션 1에서는 Express 경로의 기본 연결과 보조 연결 간에 트래픽을 분할 한 후 NAT가 적용 됩니다. NAT의 상태 저장 요구 사항을 충족 하기 위해 기본 및 보조 장치 간에 독립 NAT 풀이 사용 되므로, 흐름 egressed 하는 것과 동일한에 지 장치에 반환 트래픽이 도착할 수 있습니다.

옵션 2에서는 Express 경로의 기본 연결과 보조 연결 간에 트래픽을 분할 하기 전에 일반적인 NAT 풀이 사용 됩니다. 트래픽을 분할 하기 전에 공통 NAT 풀에서 단일 실패 지점을 도입 하 여 고가용성을 손상 시 키 지 않는다는 것을 구분 하는 것이 중요 합니다.

옵션 1을 사용 하 여 Express 경로 연결 실패 후 해당 NAT 풀에 도달할 수 있는 기능이 손상 됩니다. 따라서 모든 끊어진 흐름은 TCP 또는 응용 프로그램 계층을 통해 해당 창 시간 제한에 따라 다시 설정 해야 합니다. NAT 풀 중 하나를 사용 하 여 온-프레미스 서버를 프런트 엔드 하 고 해당 연결에 실패 한 경우 연결을 수정할 때까지 Azure에서 온-프레미스 서버에 연결할 수 없습니다.

옵션 2를 사용 하는 경우 기본 또는 보조 연결 실패 후에도 NAT에 연결할 수 있습니다. 따라서 네트워크 계층 자체는 패킷 경로를 다시 조정 하 고 오류 발생 후 복구 속도를 높일 수 있습니다. 

> [!NOTE]
> 기본 및 보조 Express 경로 연결에 대한 NAT 옵션 1 (독립 NAT 풀)을 사용 하 고 NAT 풀 중 하나에서 온-프레미스 서버로 IP 주소의 포트를 매핑하는 경우 해당 하는 연결에 실패 합니다.
> 

## <a name="fine-tuning-features-for-private-peering"></a>개인 피어 링에 대한 미세 조정 기능

이 섹션에서는 Express 경로 회로의 고가용성을 향상 시키는 데 도움이 되는 선택적 (Azure 배포 및 MTTR에 대한 중요 한 기능에 따라) 기능을 검토 합니다. 특히 Express 경로 가상 네트워크 게이트웨이의 영역 인식 배포 및 BFD (양방향 전달 검색)를 검토해 보겠습니다.

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>가용성 영역 인식 Express 경로 가상 네트워크 게이트웨이

Azure 지역의 가용성 영역은 장애 도메인과 업데이트 도메인의 조합입니다. 영역 중복 Azure IaaS 배포를 선택 하는 경우 Express 경로 개인 피어 링을 종료 하는 영역 중복 가상 네트워크 게이트웨이를 구성할 수도 있습니다. 자세한 내용은 [Azure 가용성 영역에서 영역 중복 가상 네트워크 게이트웨이 정보][zone redundant vgw]를 참조 하세요. 영역 중복 가상 네트워크 게이트웨이를 구성 하려면 [Azure 가용성 영역에서 영역 중복 가상 네트워크 게이트웨이 만들기][conf zone redundant vgw]를 참조 하세요.

### <a name="improving-failure-detection-time"></a>오류 검색 시간 향상

Express 경로는 개인 피어 링을 통해 BFD를 지원 합니다. BFD는 adsee (Microsoft Enterprise Edge)와 온-프레미스 측의 BGP 이웃 사이에서 계층 2 네트워크를 통한 오류 검색 시간을 약 3 분 (기본값)에서 1 초 미만으로 줄입니다. 빠른 오류 검색 시간은 재촉 오류 복구를 도와줍니다. 자세히 알아보려면 Express 경로를 [통한 BFD 구성][BFD]을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Express 경로 회로 연결의 고가용성을 설계 하는 방법에 대해 설명 했습니다. Express 경로 회로 피어 링 지점은 지리적 위치에 고정 되므로 전체 위치에 영향을 주는 치명적 오류의 영향을 받을 수 있습니다. 

전체 지역에 영향을 주는 치명적인 오류를 견딜 수 있는 Microsoft 백본으로 지역 중복 네트워크 연결을 빌드하기 위한 디자인 고려 사항은 Express 경로 [개인 피어 링을 사용 하 여 재해 복구 디자인][DR]을 참조 하세요.

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "express 경로를 사용 하 여 연결 하는 권장 방법" 1 개
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "가장 최적이 아닌 마지막 마일 연결"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT 옵션"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




