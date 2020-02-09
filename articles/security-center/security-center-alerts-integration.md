---
title: Azure Security Center의 Azure 보안 제품에서 발생 하는 위협 검색 경고
description: 이 항목에서는 Azure Security Center 위협 경고를 표시할 수 있는 Azure 보안 제품을 제공 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: memildin
ms.openlocfilehash: 16cae819b1714c2b410cfa311a3602e0f4ed968a
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913030"
---
# <a name="threat-detection-alerts-from-azure-waf-and-azure-ddos-protection"></a>Azure WAF 및 Azure DDoS Protection에서 위협 검색 경고

다음 Azure 보안 제품에 의해 생성 된 위협 검색 경고를 표시 하 고 수집할 수 Azure Security Center (각 제품에 대해 별도의 라이선스가 필요 함):

* [Azure WAF](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure Application Gateway는 일반적인 악용 및 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호하는 WAF(웹 애플리케이션 방화벽)를 제공합니다.

일반적으로 알려진 취약성을 악용하여 웹 애플리케이션을 공격하는 악의적인 사례가 점점 늘어나고 있습니다. Application Gateway WAF는 공개 웹 응용 프로그램 보안 프로젝트의 핵심 규칙 집합 3.0 또는 2.2.9을 기반으로 합니다. WAF는 추가 구성 없이도 새로운 취약점 으로부터 보호 하기 위해 자동으로 업데이트 됩니다. WAF 경고는 Security Center로 스트리밍됩니다. WAF에 의해 생성 되는 경고에 대 한 자세한 내용은 [웹 응용 프로그램 방화벽 CRS 규칙 그룹 및 규칙](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)을 참조 하세요.

## Azure DDoS Protection<a name="azure-ddos"></a>

DDoS (분산 서비스 거부) 공격을 쉽게 실행 하는 것으로 알려져 있습니다. 특히 응용 프로그램을 클라우드로 이동 하는 경우 매우 중요 한 보안 문제가 됩니다. 

DDoS 공격은 애플리케이션의 리소스를 소진시켜서 정상적인 사용자가 애플리케이션을 사용할 수 없게 만듭니다. DDoS 공격은 인터넷을 통해 도달할 수 있는 모든 엔드포인트을 대상으로 지정할 수 있습니다.

응용 프로그램 설계 모범 사례와 결합 된 Azure DDoS Protection는 DDoS 공격에 대 한 방어를 제공 합니다. DDoS Protection는 서로 다른 서비스 계층을 제공 합니다. 자세한 내용은 [Azure DDoS Protection 개요](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)를 참조 하세요.

Azure DDoS Protection 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-azureddos)을 참조 하세요.