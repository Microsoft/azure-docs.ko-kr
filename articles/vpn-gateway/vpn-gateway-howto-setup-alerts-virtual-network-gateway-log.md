---
title: Azure VPN 게이트웨이 진단 로그 이벤트에 대 한 경고를 설정 하는 방법
description: VPN 게이트웨이 진단 로그 이벤트에서 경고를 구성 하는 단계
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 39a6d2e6201dd0635149159cb3727fd3c40f710a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769738"
---
# <a name="setting-up-alerts-on-vpn-gateway-diagnostic-log-events"></a>VPN 게이트웨이 진단 로그 이벤트에 대 한 경고 설정

이 문서에서는 VPN 게이트웨이 진단 로그 이벤트를 기반으로 경고를 설정 합니다.


## <a name="setup"></a>포털을 사용 하 여 진단 로그 이벤트를 기반으로 하는 Azure Monitor 경고 설정

아래 예제 단계는 사이트 간 VPN 터널 연결 끊기 이벤트에 대 한 경고가 만들어집니다.



1. "Log Analytics" 모든 서비스에서 검색 하 고 "Log Analytics 작업 영역"을 선택 ![지점 대 사이트간](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "만들기")

2. Log Analytics 페이지에서 "만들기"를 클릭 합니다.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Select")

3. "새로 만들기" 작업 영역을 확인 하 고 세부 정보를 입력 합니다.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Select")

4. VPN gateway "모니터"에서 찾을 > "진단 설정" 블레이드에서 ![지점 대 사이트간](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "선택")

5. 진단을 켜려면, 게이트웨이 두 번 클릭 하 고 "진단 켜기"를 클릭 ![지점 대 사이트간](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "선택")

6. 세부 정보를 입력 하 고 "Log Analytics에 보내기" 및 "TunnelDiagnosticLog"이 선택 되어 있는지 확인 합니다. 3 단계에서 만든 Log Analytics 작업 영역을 선택 합니다.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Select")

7. Virtual network 게이트웨이 리소스 개요로 이동 합니다 및 모니터링 탭에서 "경고"를 선택 다음 새 경고 규칙을 만들거나 기존 경고 규칙을 편집 합니다.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")

8. Log Analytics 작업 영역 및 리소스를 선택 합니다.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Select")

9. 선택 "사용자 지정 로그 검색"으로 신호 논리 조건 추가 아래의 ![지점 대 사이트간](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "선택")

10. 적절 하 게 <>의 값을 바꿀 "검색어" 텍스트 상자에 다음 쿼리를 입력 합니다.

    AzureDiagnostics | where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"

    0과 "완료"를 클릭 하는 임계값 설정

    ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Select")

11. "규칙 만들기" 페이지에서 작업 그룹 섹션에서 "새로 만들기"를 클릭 합니다. 세부 정보를 입력 하 고 확인을 클릭 합니다.

![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Select")

12. "규칙 만들기" 페이지에서 "사용자 지정 작업"에 대 한 세부 정보를 입력 하 고 올바른 작업 그룹 이름 "작업 그룹 이름" 섹션에 표시 되는지 확인 합니다. "경고 규칙 만들기"를 클릭 하 여 규칙을 만듭니다.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Select")

## <a name="next-steps"></a>다음 단계

터널 메트릭에 대해 경고를 구성 하려면 [VPN Gateway 메트릭에 대 한 경고 설정 방법](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)합니다.
