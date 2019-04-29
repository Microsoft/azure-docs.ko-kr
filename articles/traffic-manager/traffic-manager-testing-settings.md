---
title: Azure Traffic Manager 설정 확인
description: 이 문서는 Traffic Manager 설정을 확인하는 데 도움이 됩니다.
services: traffic-manager
author: rockboyfor
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 03/16/2017
ms.date: 02/18/2019
ms.author: v-yeche
ms.openlocfilehash: 1e954e3c4ebba245d91cfb84ab583b314150e5b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771620"
---
# <a name="verify-traffic-manager-settings"></a>Traffic Manager 설정 확인

Traffic Manager 설정을 테스트 하려면 여러 클라이언트가 테스트를 실행할 수 있는 다양한 위치에 있어야 합니다. 그런 다음 Traffic Manager 프로필에 있는 엔드포인트를 한 번에 하나씩 종료합니다.

* 변경 내용이 신속하게 전파되도록 DNS TTL 값을 낮게 설정합니다.(예: 30초)
* 테스트 중인 프로필에서 Azure 클라우드 서비스 및 웹 사이트의 IP 주소를 알고 있습니다.
* DNS 이름을 IP 주소로 확인할 수 있는 도구를 사용하고 해당 주소를 표시합니다.

DNS 이름이 사용자 프로필에서 엔드포인트의 IP 주소로 확인되는지 검사 중입니다. 이름은 Traffic Manager 프로필에서 정의된 트래픽 라우팅 방법과 일치하는 방식으로 해결해야 합니다. DNS 이름을 해결하기 위해 **nslookup**와 같은 도구를 사용하거나 또는 **dig**할 수 있습니다.

다음은 Traffic Manager 프로필을 테스트하는 데 도움이 되는 예제입니다.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Windows에서 nslookup 및 ipconfig를 사용하여 Traffic Manager 프로필을 확인합니다.

1. 관리자로 명령 또는 Windows PowerShell 프롬프트를 엽니다.
2. `ipconfig /flushdns`를 입력하여 DNS 확인자 캐시를 플러시합니다.
3. `nslookup <your Traffic Manager domain name>`를 입력합니다. 예를 들어 다음 명령은 접두사 *myapp.contoso*가 있는 도메인 이름을 확인합니다.

        nslookup myapp.contoso.trafficmanager.cn

    일반적인 결과는 다음 정보를 보여줍니다.

    + 이 Traffic Manager 도메인을 확인하기 위해 액세스하는 DNS 서버의 DNS 이름 및 IP 주소입니다.
    + 명령줄에서 "nslookup" 뒤에 입력한 Traffic Manager 도메인 이름 및 Traffic Manager 도메인이 확인하는 IP 주소입니다. 두 번째 IP 주소는 중요 확인 사항입니다. 테스트 중인 Traffic Manager 프로필에서 웹 사이트 또는 클라우드 서비스 중 하나에 대한 공용 가상 IP(VIP) 주소와 일치해야 합니다.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>장애 조치(Failover) 트래픽 라우팅 메서드를 테스트하는 방법

1. 모든 엔드포인트를 실행 상태로 둡니다.
2. 단일 클라이언트를 사용할 경우, Nslookup이나 유사한 유틸리티를 사용하여 회사의 도메인 이름에 대한 DNS 확인을 요청합니다.
3. 확인된 IP 주소가 기본 엔드포인트와 일치하는지 확인합니다.
4. Traffic Manager가 애플리케이션이 종료되었다고 인식되도록 기본 엔드포인트를 종료하거나 모니터링 파일을 제거합니다.
5. Traffic Manager 프로필의 DNS TTL(Time-to-Live)에 2분을 추가하여 기다립니다. 예를 들어, DNS TTL이 300초(5분)인 경우 7분을 대기해야 합니다.
6. Nslookup을 사용하여 DNS 클라이언트 캐시 및 요청 DNS 확인을 플러시하십시오. Windows에서는 ipconfig /flushdns 명령을 실행하여 DNS 캐시를 플러시할 수 있습니다.
7. 확인된 IP 주소가 보조 엔드포인트와 일치하는지 확인합니다.
8. 프로세스를 반복하여 각 엔드포인트를 차례로 종료합니다. DNS가 목록에서 다음 엔드포인트의 IP 주소를 반환하는지 확인합니다. 모든 엔드포인트가 종료되면 다시 기본 엔드포인트의 IP 주소를 얻게 됩니다.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>가중치 적용 트래픽 라우팅 메서드를 테스트하는 방법

1. 모든 엔드포인트를 실행 상태로 둡니다.
2. 단일 클라이언트를 사용할 경우, Nslookup이나 유사한 유틸리티를 사용하여 회사의 도메인 이름에 대한 DNS 확인을 요청합니다.
3. 확인된 IP 주소가 엔드포인트 중 하나와 일치하는지 확인합니다.
4. 각 엔드포인트에 대해 DNS 클라이언트 캐시를 플러시하고 2단계와 3단계를 계속 반복합니다. 각 엔드포인트에 대해 서로 다른 IP 주소가 반환되어야 합니다.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>성능 트래픽 라우팅 메서드를 테스트하는 방법

성능 트래픽 라우팅 방법을 효과적으로 테스트하려면 전 세계의 각기 다른 곳에 클라이언트가 있어야 합니다. 서비스를 테스트하는 데 사용할 수 있는 여러 Azure 지역에서 클라이언트를 만들 수 있습니다. 글로벌 네트워크이 있다면 원격으로 세계 다른 곳에 있는 클라이언트에 로그인하여 해당 위치에서 테스트를 실행할 수 있습니다.

웹 기반 DNS 조회를 해제하고 사용할 수 있는 서비스를 이용합니다. 이들 도구 중 일부에는 세계의 다양한 위치에서 DNS 이름을 확인할 수 있는 기능이 있습니다. 예제를 보려면 "DNS 조회"를 검색합니다. Gomez 또는 Keynote와 같은 타사 서비스를 사용하여 정책이 예상대로 트래픽을 분산하는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Traffic Manager 트래픽 라우팅 방법 정보](traffic-manager-routing-methods.md)
* [Traffic Manager 성능 고려 사항](traffic-manager-performance-considerations.md)
* [Traffic Manager 성능 저하 상태 문제 해결](traffic-manager-troubleshooting-degraded.md)

<!-- Update_Description: update meta properties -->