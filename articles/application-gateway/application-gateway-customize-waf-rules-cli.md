---
title: Azure Application Gateway-Azure CLI에서에서 웹 응용 프로그램 방화벽 규칙 사용자 지정
description: 이 문서에서는 Azure CLI를 통해 Application Gateway에서 웹 애플리케이션 방화벽 규칙을 사용자 지정하는 방법을 설명합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
origin.date: 02/22/2019
ms.date: 02/26/2019
ms.author: v-junlch
ms.openlocfilehash: 5e364c597b8c524e95297f279003462f2d16abe1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832900"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli"></a>Azure CLI를 통해 웹 애플리케이션 방화벽 규칙 사용자 지정

Azure Application Gateway WAF(웹 애플리케이션 방화벽)는 웹 애플리케이션을 보호합니다. 이러한 보호 기능은 OWASP(Open Web Application Security Project) CRS(코어 규칙 세트)을 통해 제공됩니다. 일부 규칙은 거짓 긍정의 원인이 되어 실제 트래픽을 차단할 수도 있습니다. 이러한 이유로 Application Gateway는 규칙 그룹 및 규칙을 사용자 지정하는 기능을 제공합니다. 특정 규칙 그룹 및 규칙에 대한 자세한 내용은 [웹 애플리케이션 방화벽 CRS 규칙 그룹 및 규칙 목록](application-gateway-crs-rulegroups-rules.md)을 참조하세요.

## <a name="view-rule-groups-and-rules"></a>규칙 그룹 및 규칙 보기

다음 코드 예제에서는 구성 가능한 규칙 및 규칙 그룹을 보는 방법을 보여 줍니다.

### <a name="view-rule-groups"></a>규칙 그룹 보기

다음 예제에서는 규칙 그룹을 보는 방법을 보여 줍니다.

```azurecli
az network application-gateway waf-config list-rule-sets --type OWASP
```

다음 출력은 이전 예제에서 잘린 응답입니다.

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>규칙 그룹에서 규칙 보기

다음 예제에서는 지정된 규칙 그룹에서 규칙을 보는 방법을 보여 줍니다.

```azurecli
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

다음 출력은 이전 예제에서 잘린 응답입니다.

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>규칙 사용 안 함

다음 예제는 애플리케이션 게이트웨이에서 규칙 `910018` 및 `910017`을 비활성화합니다.

```azurecli
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="mandatory-rules"></a>필수 규칙

다음 목록에는 WAF (검색 모드에서 예외로 기록 됩니다) 방지 모드에서 요청을 차단 하는 조건이 포함 됩니다. 이러한 구성 하거나 비활성화할 수 있습니다.

- 요청 본문을 구문 분석 하지 못하면 차단 되 고 요청에 본문 검사 됩니다 (XML, JSON, 양식 데이터)를 해제 하지 않는다면
- 요청 본문 (파일 없음)와 데이터 길이 구성된 된 제한 보다 큽니다.
- 요청 본문 (파일 포함) 제한 보다 큽니다.
- WAF 엔진에 내부 오류가 발생 했습니다.

CRS 3.x 관련:

- 인바운드 변칙을 초과 하는 점수 임계값

## <a name="next-steps"></a>다음 단계

사용하지 않는 규칙을 구성한 후에 WAF 로그를 보는 방법을 알아볼 수 있습니다. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md#diagnostic-logging)을 참조하세요.

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

<!-- Update_Description: wording update -->