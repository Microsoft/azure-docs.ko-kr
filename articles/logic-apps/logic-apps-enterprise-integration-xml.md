---
title: B2B 엔터프라이즈 통합용 XML 메시지 - Azure Logic Apps | Microsoft Docs
description: 엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 B2B 솔루션용 XML 메시지를 처리, 유효성 검사, 변환 및 보강
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.date: 02/27/2017
ms.openlocfilehash: a75ac9773072423c13eef85ecad29c632c13d024
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996579"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps을 사용하는 XML 메시지 및 플랫 파일

논리 앱을 사용하여 송신 및 수신하는 XML 메시지를 처리하는 기능이 있습니다. 이 기능은 엔터프라이즈 통합 팩에 포함됩니다. 백그라운드로 BizTalk Server가 있는 사용자의 경우 엔터프라이즈 통합 팩을 통해 메시지를 변환 및 유효성 검사하거나, 플랫 파일로 작업하며, XPath를 사용하여 메시지를 보강하거나 메시지에서 특정 속성을 추출하는 유사한 기능을 사용할 수 있습니다. 

이 분야를 처음 접하는 사용자의 경우 이러한 기능을 통해 워크플로 내에서 메시지를 처리하는 방식을 확장할 수 있습니다. 예를 들어, 기업 간 시나리오에 해당하며 특정 XML 스키마로 작업하는 경우 엔터프라이즈 통합 팩을 사용하여 기업이 이러한 메시지를 처리하는 방식을 개선할 수 있습니다. 

엔터프라이즈 통합 팩에는 다음이 포함됩니다. 

* [XML 유효성 검사](logic-apps-enterprise-integration-xml-validation.md "XML 메시지 유효성 검사에 대해 알아보기") - 특정 스키마에 대해 들어오거나 나가는 XML 메시지의 유효성을 검사합니다.
* [XML 변환](../logic-apps/logic-apps-enterprise-integration-transform.md "XML 메시지 변환 및 맵에 대해 알아보기") - 사용자 또는 파트너의 요구 사항에 따라 XML 메시지를 변환하거나 사용자 지정합니다.
* [플랫 파일 인코딩 및 플랫 파일 디코딩](logic-apps-enterprise-integration-flatfile.md "플랫 파일 인코딩/디코딩에 대해 알아보기") - 플랫 파일을 인코딩 또는 디코딩합니다. 예를 들어 SAP에서 플랫 파일로 IDOC 파일을 수락하고 보냅니다. 많은 통합 플랫폼에서는 Logic Apps를 포함하여 XML 메시지를 만듭니다. 따라서 XML 파일을 플랫 파일로 "변환"하는 플랫 파일 인코더를 사용하는 논리 앱을 만들 수 있습니다. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) - 메시지를 보완하고 메시지에서 특정 속성을 추출합니다. 그런 후에는 추출된 속성을 사용하여 메시지를 대상 또는 중간 엔드포인트로 라우팅할 수 있습니다.

## <a name="try-it-out"></a>체험
[완전히 작동 하는 논리 앱 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (GitHub sample) Azure Logic Apps의 XML 기능을 사용 합니다.

## <a name="learn-more"></a>자세한 정보
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")
