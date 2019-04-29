---
title: Azure Security Center의 위협 인텔리전스 및 보안 경고 맵 | Microsoft Docs
description: Azure Security Center에서 보안 경고 맵 및 위협 인텔리전스 기능을 사용하여 VM 및 컴퓨터의 잠재적 위협을 식별하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: f1e238890b777e2816648ec407e2581f636d1c12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60906131"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>보안 경고 맵 및 위협 인텔리전스
이 문서에서는 Azure Security Center 보안 경고 맵과 보안 이벤트 기반 위협 인텔리전스 맵을 사용하여 보안 관련 문제를 해결하는 데 도움이 됩니다.

## <a name="how-the-security-alerts-map-works"></a>보안 경고 맵이 작동하는 방법
Security Center는 환경에 대한 보안 위협을 식별하는 데 도움이 되는 맵을 제공합니다. 예를 들어 특정 컴퓨터가 봇네트에 속해 있는지 여부와 위협이 제공되는 위치를 확인할 수 있습니다. 공격자가 봇네트를 관리하는 명령 및 제어와 몰래 상호 작용하는 맬웨어를 불법적으로 설치하면 컴퓨터가 봇네트의 노드가 될 수 있습니다. 

Security Center는 이 맵을 작성하기 위해 Microsoft 내의 여러 소스에서 제공되는 데이터를 사용합니다. Security Center는 이 데이터를 사용하여 사용자 환경에 대한 잠재적 위협을 매핑합니다. 

[보안 인시던트 대응 프로세스](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) 단계 중 하나는 손상된 시스템의 심각도를 식별하는 것입니다. 이 단계에서는 다음 작업을 수행해야 합니다.

- 공격 특성 확인
- 공격 원점 확인
- 공격 의도 확인 특정 정보를 얻기 위해 사용자 조직을 노린 공격입니까, 아니면 무작위 공격입니까?
- 손상된 시스템 식별
- 액세스된 파일 식별 및 해당 파일의 중요도 확인

Security Center에서 보안 경고 맵을 사용하여 이러한 작업을 지원할 수 있습니다.

## <a name="access-the-security-alerts-map"></a>보안 경고 맵 액세스
환경의 현재 위협을 시각화하려면 보안 경고 맵을 엽니다.

1. **Security Center** 대시보드를 엽니다.
2. 왼쪽 창의 **위협 보호** 아래에서 **보안 경고 맵**을 선택합니다. 맵이 열립니다.
3. 경고에 대한 자세한 정보를 얻고 수정 단계를 받으려면 맵에서 경고 점을 클릭하고 지침을 따릅니다. 
 
보안 경고 맵은 경고를 기반으로 합니다. 이러한 경고는 IP 주소가 알려진 위험한 IP 주소(예: 알려진 암호 화폐 채굴기(cryptominer))인지 또는 이전에 위험한 것으로 인식되지 않은 IP 주소인지 여부에 관계없이 네트워크 통신이 성공적으로 확인된 IP 주소와 연결된 활동을 기반으로 합니다. 맵은 Azure에서 이전에 선택한 모든 구독에 대해 알림을 제공합니다. 

맵의 경고는 발생 원점으로 감지된 지리적 위치에 따라 표시되며, 심각도별 색 코드로 지정되어 있습니다. 
    ![위협 인텔리전스 보고서](./media/security-center-threat-intel/security-center-alert-map.png)

## <a name="viewing-the-event-based-threat-intelligence-dashboard"></a>이벤트 기반 위협 인텔리전스 대시보드 보기
원시 보안 이벤트를 기반으로 하는 위협 인텔리전스 맵을 보려면 다음 절차를 수행할 수 있습니다. 이 맵은 위험한 것으로 간주되는 IP 주소(예: 알려진 봇네트의 IP 주소)와 관련된 이벤트만 표시합니다.

1. **Security Center** 대시보드를 엽니다.

1. 왼쪽 창의 **위협 보호** 아래에서 **보안 경고 맵**을 선택합니다. 맵이 열립니다.
2. 오른쪽 위 모서리에서 **보안 이벤트 맵으로 이동**을 클릭합니다.
3. 보려는 대시보드의 대상이 되는 작업 영역을 선택합니다.
4. 맵 위쪽에서 **클래식 위협 인텔리전스 보기**를 선택합니다. **위협 인텔리전스** 대시보드가 열립니다.

   > [!NOTE]
   > 맨 오른쪽의 열이 **업그레이드 계획**을 표시하면 이 작업 영역은 체험용 구독을 사용하는 중입니다. 표준으로 업그레이드하여 이 기능을 사용합니다. 맨 오른쪽 열 표시 되 면 **업데이트 필요**, 업데이트 [Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 이 기능을 사용 합니다. 가격 계획에 대한 자세한 내용은 Azure Security Center 가격 책정을 참조하세요.
   >
5. 둘 이상의 작업 영역을 조사할 경우 **악성 IP** 열에 따라 조사의 우선 순위를 지정합니다. 이 작업 영역에서 현재 악성 IP 수를 보여줍니다. 사용하려는 작업 영역을 선택하면 **위협 인텔리전스** 대시보드가 나타납니다.

    ![위협 인텔리전스 정보](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

6. 대시보드는 네 개의 타일로 구성됩니다.

    a.  **위협 형식** 선택한 작업 영역에서 검색된 위협의 유형을 요약합니다.

    b.  **국적** 해당 원본 위치에 따라 트래픽 양을 집계합니다.

    다.  **위협 위치** 사용자 환경과 통신하는 전 세계의 현재 위치를 식별할 수 있습니다. 표시된 맵에서 주황색(들어옴)과 빨간색(보냄) 화살표는 트래픽 방향을 식별합니다. 이러한 화살표 중 하나를 선택하는 경우 위협의 유형 및 트래픽 방향이 나타납니다.

    d.  **위협 세부 정보** 맵에서 선택된 위협에 대한 세부 정보를 표시합니다.

선택한 옵션 타일에 관계 없이 표시되는 대시보드는 로그 검색 쿼리를 기반으로 합니다. 유일한 차이점은 쿼리 및 결과의 유형입니다.

### <a name="threat-types"></a>위협 유형
**위협 형식** 타일을 선택하여 **로그 검색** 대시보드를 엽니다. 필터 옵션이 왼쪽에 표시되고 쿼리 결과는 오른쪽에 표시됩니다.

![로그 검색](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

쿼리 결과는 이름을 기준으로 위협을 표시합니다. 왼쪽 창을 사용하여 필터링하려는 특성을 선택할 수 있습니다. 예를 들어 현재 컴퓨터에 연결되어 있는 위협만을 확인하려면 **SESSIONSTATE**에서 **연결됨** > **적용**을 선택합니다.

![세션 상태](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Azure VM의 경우 에이전트를 통해 흐르는 네트워크 데이터만이 **위협 인텔리전스** 대시보드에 표시됩니다. 다음 데이터 형식은 위협 인텔리전스에서도 사용됩니다.

- CEF 데이터(Type=CommonSecurityLog)
- WireData(Type= WireData)
- IIS 로그(Type=W3CIISLog)
- Windows 방화벽(Type=WindowsFirewall)
- DNS 이벤트(Type=DnsEvents)


## <a name="see-also"></a>참고 항목
이 문서에서는 의심스러운 활동을 식별하기 위해 Security Center에서 위협 인텔리전스를 사용하는 방법을 살펴보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center에서 보안 경고 관리 및 응답](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md). Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). 다양한 유형의 보안 경고에 대해 알아봅니다.
* [Azure Security Center 문제 해결 가이드](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Security Center의 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md)로 설정합니다. 서비스 사용에 관한 질문과 대답에 대한 답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
