---
title: Azure 웹 응용 프로그램 방화벽 (WAF) 데이터를 Azure 센티널에 연결
description: Azure WAF 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: 8f21b415ef36442d6ac1aac518cd1327f70b8927
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88264075"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>Azure WAF (웹 응용 프로그램 방화벽)에서 데이터 연결

일반적으로 알려진 취약성을 악용하여 웹 애플리케이션을 공격하는 악의적인 사례가 점점 늘어나고 있습니다. Azure WAF (웹 응용 프로그램 방화벽)는 코드 삽입 및 사이트 간 스크립팅과 같은 일반적인 악용 및 위협 으로부터 웹 응용 프로그램에 대 한 중앙 집중식 보호를 제공 합니다. Azure WAF는 [Azure 애플리케이션 Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview) 서비스, [azure Front 도어](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview) 서비스 및 [azure Content Delivery Network (CDN)](https://docs.microsoft.com/azure/web-application-firewall/cdn/cdn-overview) waf 정책 (현재 공개 미리 보기로 제공 됨)을 통해 배포할 수 있습니다.
Azure WAF 로그를 Azure 센티널에 연결 하 여 통합 문서에서 로그 데이터를 확인 하 고이를 사용 하 여 사용자 지정 경고를 만든 다음이를 통합 하 여 조사를 개선할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

## <a name="connect-to-azure-waf"></a>Azure WAF에 연결

### <a name="instructions-tab"></a>명령 탭

1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터**를 선택 합니다.

1. 데이터 커넥터 갤러리에서 **Azure WAF (웹 응용 프로그램 방화벽)** 를 선택한 다음 미리 보기 창에서 **커넥터 페이지 열기** 를 선택 합니다.

1. 연결할 로그가 포함 된 WAF 리소스의 유형에 대 한 링크를 선택 합니다. **Application Gateway 리소스 >** 을 열거나, **전방 도어 리소스 >** 를 열거나 **Content Delivery Network, CDN (CDN) waf 정책 >** 를 엽니다. 리소스 목록의 화면에서 한 번은 목록에서 waf 리소스를 선택 합니다.

    1. WAF 리소스의 탐색 메뉴에서 **진단 설정**을 선택 합니다.

    1. 목록의 맨 아래에 있는 **+ 진단 설정 추가** 를 선택 합니다.

    1. **진단 설정** 화면에서 **진단 설정 이름** 필드에 이름을 입력 합니다.

    1. **Log Analytics 보내기** 확인란을 클릭 합니다. 그 아래에는 두 개의 새 필드가 표시 됩니다. 관련 **구독** 및 **Log Analytics 작업 영역** 을 선택 합니다 (Azure 센티널이 있는 경우).

    1. 수집 하려는 로그의 규칙 유형에 대 한 확인란을 클릭 합니다. 각 리소스 종류에 대 한 권장 사항은 다음과 같습니다.

        | 리소스 | 수집을 위해 선택할 로그 |
        |----------|------------------------------|
        | Application Gateway | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | CDN WAF 정책      | WebApplicationFirewallLogs |
        |

    1. **저장**을 선택합니다.

### <a name="next-steps-tab"></a>다음 단계 탭

- Azure WAF 로그 데이터에 대 한 정보를 얻으려면 **azure 웹 응용 프로그램 방화벽** 데이터 커넥터와 함께 제공 되는 권장 통합 문서, 쿼리 샘플 및 분석 규칙 템플릿을 참조 하세요.

- **로그**에서 Azure waf 데이터를 쿼리하려면 쿼리 창에서 **azurediagnostics** 를 입력 합니다.

> [!NOTE]
>
> 이 특정 데이터 커넥터를 사용 하 여 지난 2 주 동안 데이터가 수집 된 경우에만 연결 상태 표시기 (데이터 커넥터 갤러리의 색 줄무늬 및 데이터 형식 이름 옆의 연결 아이콘)가 *연결* 됨 (녹색)으로 표시 됩니다. 두 주가 데이터 수집 없이 전달 되 면 커넥터는 연결 끊김으로 표시 됩니다. 더 많은 데이터가 제공 되는 순간에 *연결* 된 상태는를 반환 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 azure azure에 azure WAF 로그를 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
