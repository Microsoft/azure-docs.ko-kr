---
title: Azure 센티널에 위협 인텔리전스 데이터 연결 | Microsoft Docs
description: Azure 센티널에 위협 인텔리전스 데이터를 연결 하는 방법에 대해 알아봅니다.
documentationcenter: na
author: cabailey
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: cabailey
ms.openlocfilehash: 33edeb04e88a01efafaf69b850ed87120671ed11
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384132"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>위협 인텔리전스 공급자의 데이터 연결

> [!IMPORTANT]
> Azure 센티널의 위협 인텔리전스 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 센티널을 사용 하 여 조직에서 사용 중인 위협 지표를 가져올 수 있습니다. 이렇게 하면 보안 분석가가 알려진 위협을 검색 하 고 우선 순위를 정하는 기능을 향상 시킬 수 있습니다. Azure 센티널의 여러 기능을 사용할 수 있게 되거나 향상 됩니다.

- **분석** 에는 위협 지표에서 로그 이벤트의 일치를 기반으로 경고 및 인시던트를 생성 하는 데 사용할 수 있는 예약 된 규칙 템플릿 집합이 포함 되어 있습니다.

- **통합 문서** 는 Azure 센티널로 가져온 위협 지표 및 위협 표시기와 일치 하는 분석 규칙에서 생성 된 모든 경고에 대 한 요약 정보를 제공 합니다.

- **구하기** 쿼리를 통해 보안 사관는 일반적인 구하기 시나리오의 컨텍스트 내에서 위협 지표를 사용할 수 있습니다.

- **노트북** 은 비정상을 조사 하 고 악의적인 행동을 사냥 하는 경우 위협 지표를 사용할 수 있습니다.

다음 섹션에 나열 된 통합 된 TAXII 서버에 연결 하거나 [Microsoft Graph Security tiIndicators API](https://aka.ms/graphsecuritytiindicators)와의 직접 통합을 사용 하 여 Azure 센티널에 위협 지표를 스트리밍할 수 있습니다.

## <a name="integrated-threat-intelligence-platform-products"></a>통합 위협 인텔리전스 플랫폼 제품

- [오픈 소스 위협 인텔리전스 플랫폼](https://www.misp-project.org/)
    
    클라이언트에 잘못 된 p 인스턴스를 제공 하 여 위협 표시기를 Microsoft Graph 보안 API로 마이그레이션하는 샘플 스크립트는 [Microsoft Graph 보안 스크립트에](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)대 한 잘못 된 항목을 참조 하세요.

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    단계별 지침은 [MineMeld를 사용 하 여 Microsoft Graph 보안 API로 IOCs 전송](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)을 참조 하세요.

- [ThreatConnect 플랫폼](https://threatconnect.com/solution/)

    자세한 내용은 [ThreatConnect 통합 및](https://threatconnect.com/integrations/) Security API Microsoft Graph 찾기 페이지에서 참조 하세요.


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Azure 센티널을 위협 인텔리전스 플랫폼에 연결

## <a name="prerequisites"></a>선행 조건  

- 전역 관리자 또는 보안 관리자의 Azure AD 역할로, Microsoft Graph Security tiIndicators API와의 직접 통합을 사용 하는 사용자 지정 응용 프로그램 또는 팁 제품에 대 한 사용 권한을 부여 합니다.

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한으로 위협 지표를 저장 합니다.

## <a name="instructions"></a>지침

1. 응용 프로그램 ID, 응용 프로그램 암호 및 Azure Active Directory 테넌트 ID를 가져오려면 Azure Active Directory에 [응용 프로그램을 등록](/graph/auth-v2-service#1-register-your-app) 합니다. Microsoft Graph Security tiIndicators API와의 직접 통합을 사용 하는 통합 팁 제품 또는 앱을 구성 하는 경우에 이러한 값이 필요 합니다.

2. 등록 된 응용 프로그램에 대 한 [API 권한 구성](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) : Microsoft Graph 응용 프로그램 권한 **ThreatIndicators** 을 등록 된 응용 프로그램에 추가 합니다.

3. Azure Active Directory 테넌트 관리자에 게 문의 하 여 조직에 등록 된 응용 프로그램에 관리자 동의를 부여 합니다. Azure Portal: **Azure Active Directory** > **앱 등록** > \< **_앱 이름_** > > **API 사용 권한** > \< **_테넌트 이름_>에 대 한 관리자 동의 부여**를 참조 하세요.

4. 다음을 지정 하 여 Azure 센티널에 표시기를 보내도록 Microsoft Graph Security tiIndicators API와의 직접 통합을 사용 하는 TIP 제품 또는 앱을 구성 합니다.
    
    가. 등록 된 응용 프로그램의 ID, 비밀 및 테넌트 ID에 대 한 값입니다.
    
    b. 대상 제품의 경우 Azure 센티널을 지정 합니다.
    
    c. 작업의 경우 경고를 지정 합니다.

5. Azure Portal에서 **Azure 센티널** > **데이터 커넥터** 로 이동한 다음, **위협 인텔리전스 플랫폼 (미리 보기)** 커넥터를 선택 합니다.

6. **커넥터 페이지 열기**를 선택한 다음 **연결**을 선택 합니다.

7. Azure 센티널로 가져온 위협 지표를 보려면 **Azure 센티널-Logs** > **securityinsights**로 이동한 다음 **ThreatIntelligenceIndicator**를 확장 합니다.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>TAXII 서버에 Azure 센티널 연결

## <a name="prerequisites"></a>선행 조건  

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한으로 위협 지표를 저장 합니다.

- TAXII 2.0 서버 URI 및 컬렉션 ID입니다.

## <a name="instructions"></a>지침

1. Azure Portal에서 **Azure 센티널** > **데이터 커넥터** 로 이동한 후 **위협 인텔리전스-TAXII (미리 보기)** 커넥터를 선택 합니다.

2. **커넥터 페이지 열기**를 선택 합니다.

3. 텍스트 상자에 필수 및 선택적 정보를 지정 합니다.

4. **추가** 를 선택 하 여 TAXII 2.0 서버에 대 한 연결을 사용 하도록 설정 합니다.

5. 추가 TAXII 2.0 서버가 있는 경우 3 단계와 4 단계를 반복 합니다.

6. Azure 센티널로 가져온 위협 지표를 보려면 **Azure 센티널-Logs** > **securityinsights**로 이동한 다음 **ThreatIntelligenceIndicator**를 확장 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 센티널에 위협 인텔리전스 공급자를 연결 하는 방법을 알아보았습니다. Azure 센티널에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
