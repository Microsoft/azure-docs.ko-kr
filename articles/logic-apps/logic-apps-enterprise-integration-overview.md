---
title: B2B 엔터프라이즈 통합 개요 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps 및 엔터프라이즈 통합 팩을 사용하여 엔터프라이즈 통합 솔루션용 자동화 B2B 워크플로 빌드
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.date: 09/08/2016
ms.openlocfilehash: 8c0e47f5bed0799b8536cecb38a85ed76185d0cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845842"
---
# <a name="overview-b2b-enterprise-integration-scenarios-in-azure-logic-apps-with-enterprise-integration-pack"></a>개요: 엔터프라이즈 통합 팩이 포함된 Azure Logic Apps을 사용한 B2B 엔터프라이즈 통합 시나리오

Azure Logic Apps에 대한 B2B(기업 간) 워크플로 및 원활한 통신을 위해 Microsoft의 클라우드 기반 솔루션인 엔터프라이즈 통합 팩을 통한 엔터프라이즈 통합 시나리오를 사용하도록 설정할 수 있습니다. 조직에서는 다른 프로토콜 및 형식을 사용하더라도 전자 방식으로 메시지를 교환할 수 있습니다. 이 팩은 여려 다른 형식을 조직의 시스템에서 해석하고 처리할 수 있는 형식으로 변환합니다. 조직은 [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) 및 [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md)를 비롯한 업계 표준 프로토콜을 통해 메시지를 교환할 수 있습니다. 암호화 및 디지털 서명 둘 다를 사용하여 메시지를 보호할 수 있습니다.

BizTalk Server 또는 Microsoft Azure BizTalk Services를 잘 알고 있다면 개념이 대부분 유사하기 때문에 엔터프라이즈 통합 기능을 쉽게 사용할 수 있습니다. 주요 차이점은 엔터프라이즈 통합이 통합 계정을 사용하여 B2B 통신에 사용한 아티팩트의 저장 및 관리를 간소화한다는 점입니다. 

구조적으로 엔터프라이즈 통합 팩은 "통합 계정"을 기준으로 합니다. 이러한 계정은 스키마, 파트너, 인증서, 맵 및 규약 등의 모든 사용자 아티팩트를 저장하는 클라우드 기반 컨테이너입니다. 이러한 아티팩트를 사용하여 B2B 앱을 디자인, 배포 및 유지 관리하고, Logic Apps에 대한 B2B 워크플로를 구축할 수 있습니다. 그렇지만 이러한 아티팩트를 사용하려면 먼저 통합 계정을 논리 앱에 연결해야 합니다. 그런 후에 논리 앱에서 통합 계정의 아티팩트에 액세스할 수 있습니다.

## <a name="why-should-you-use-enterprise-integration"></a>엔터프라이즈 통합을 사용하는 이유

* 엔터프라이즈 통합을 사용하면 통합 계정에 모든 아티팩트를 저장할 수 있습니다.
* Azure Logic Apps 및 모든 해당 커넥터를 활용하여 B2B 워크플로를 구축하고 타사 SaaS(Software-as-Service) 앱, 온-프레미스 앱 및 사용자 지정 앱과 통합할 수 있습니다.
* Azure Functions를 사용하여 Logic Apps에 대한 사용자 지정 코드를 만들 수 있습니다.

## <a name="how-to-get-started-with-enterprise-integration"></a>엔터프라이즈 통합을 시작하는 방법

**Azure Portal**에서 Logic App Designer를 통한 엔터프라이즈 통합 팩을 사용하여 B2B 앱을 구축하고 관리할 수 있습니다. [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp)을 사용하여 논리 앱을 관리할 수도 있습니다.

Azure Portal에서 앱을 만들기 전에 수행해야 하는 고급 단계는 다음과 같습니다.

![개요 이미지](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>몇 가지 일반적인 시나리오는 무엇인가요?

엔터프라이즈 통합은 이러한 업계 표준을 지원합니다.

* EDI - 전자 데이터 교환
* EAI - Enterprise Application Integration

## <a name="heres-what-you-need-to-get-started"></a>시작하기 위해 필요한 항목은 다음과 같습니다.

* 통합 계정을 사용하는 Azure 구독
* 맵과 스키마를 만드는 Visual Studio 2015
* [Visual Studio용 Microsoft Azure Logic Apps 엔터프라이즈 통합 도구 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it-now"></a>지금 평가판 사용

Azure Logic Apps의 B2B 기능을 사용하는 [완벽하게 작동하는 샘플 AS2 송신 및 수신 논리 앱을 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)합니다.

## <a name="learn-more"></a>자세한 정보
* [규약](../logic-apps/logic-apps-enterprise-integration-agreements.md "엔터프라이즈 통합 규약에 대해 알아보기")
* [B2B(기업 간) 시나리오](../logic-apps/logic-apps-enterprise-integration-b2b.md "B2B 기능으로 논리 앱을 만드는 방법 알아보기")  
* [인증서](logic-apps-enterprise-integration-certificates.md "엔터프라이즈 통합 인증서에 대해 알아보기")
* [플랫 파일 인코딩/디코딩](logic-apps-enterprise-integration-flatfile.md "플랫 파일 내용을 인코딩 및 디코딩하는 방법 알아보기")  
* [Integration accounts](../logic-apps/logic-apps-enterprise-integration-accounts.md "Learn about 통합 계정")
* [맵](../logic-apps/logic-apps-enterprise-integration-maps.md "엔터프라이즈 통합 맵에 대해 알아보기")
* [파트너](logic-apps-enterprise-integration-partners.md "엔터프라이즈 통합 파트너에 대해 알아보기")
* [스키마](logic-apps-enterprise-integration-schemas.md "엔터프라이즈 통합 스키마에 대해 알아보기")
* [XML 메시지 유효성 검사](logic-apps-enterprise-integration-xml.md "논리 앱을 사용하여 XML 메시지의 유효성을 검사하는 방법 알아보기")
* [XML 변환](logic-apps-enterprise-integration-transform.md "엔터프라이즈 통합 맵에 대해 알아보기")
* [엔터프라이즈 통합 커넥터](../connectors/apis-list.md "엔터프라이즈 통합 팩 커넥터에 대해 알아보기")
* [통합 계정 메타데이터](../logic-apps/logic-apps-enterprise-integration-metadata.md "통합 계정 메타데이터에 대해 알아보기")
* [B2B 메시지 모니터링](logic-apps-monitor-b2b-message.md "B2B 메시지를 모니터링하는 방법에 대해 알아보기")
* [Azure Monitor 로그에서 B2B 메시지 추적](logic-apps-track-b2b-messages-omsportal.md "Azure Monitor 로그에서 B2B 메시지 추적에 대해 자세히 알아보기")

