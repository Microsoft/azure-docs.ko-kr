---
title: Microsoft 웹 응용 프로그램 방화벽 데이터를 Azure Sentinel 미리 보기에 연결 | Microsoft Docs
description: Microsoft 웹 응용 프로그램 방화벽 데이터 Azure Sentinel를 연결 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 5316fa7e3aa4465349b762b99bec9171f821062f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714747"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Microsoft 웹 응용 프로그램 방화벽에서 데이터 연결

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Application Gateway의 Microsoft 웹 응용 프로그램 방화벽 (WAF)의 로그를 스트리밍할 수 있습니다. 이 WAF 교차 사이트 스크립팅, SQL 주입 등 일반적인 웹 취약점 으로부터 응용 프로그램을 보호 하 고 가양성을 줄이는 규칙을 사용자 지정할 수 있습니다. Azure Sentinel에 Microsoft 웹 응용 프로그램 방화벽 로그를 스트리밍하려면 다음이 지침을 따릅니다.


## <a name="prerequisites"></a>필수 조건

- 기존 응용 프로그램 게이트웨이 리소스

## <a name="connect-to-microsoft-web-application-firewall"></a>Microsoft 웹 응용 프로그램 방화벽에 연결

Microsoft 웹 응용 프로그램 방화벽에 이미 있는 경우 기존 게이트웨이 리소스가 있는지 확인 합니다.
Microsoft 웹 응용 프로그램 방화벽을 배포 하 고 데이터 가져오기, 경고 데이터를 쉽게 수행할 수 되 면 Azure Sentinel로 스트림할 수 있습니다.
    
1. Sentinel Azure portal에서 선택 **데이터 커넥터**합니다.
1. 데이터 커넥터 페이지에서 선택 합니다 **WAF** 바둑판식으로 배열 합니다.
1. 로 이동 [Application Gateway 리소스](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) WAF를 선택 합니다.
    1. 선택 **진단 설정**합니다.
    1. 선택 **진단 설정 추가 +** 테이블 아래에 있습니다.
    1. 에 **진단 설정** 페이지에서 입력을 **이름** 선택한 **Log Analytics에 보내기**합니다.
    1. 아래 **Log Analytics 작업 영역** Azure Sentinel 작업 영역을 선택 합니다.
    1. 분석 하려는 로그 유형을 선택 합니다. 좋습니다. ApplicationGatewayAccessLog 및 ApplicationGatewayFirewallLog 합니다.
1. Log Analytics에서 관련 스키마를 사용 하 여 Microsoft 웹 응용 프로그램 방화벽 경고를 검색할 **AzureDiagnostics**합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel Microsoft 웹 응용 프로그램 방화벽 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.
