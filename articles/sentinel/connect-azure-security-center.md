---
title: Azure 센티널에 Azure Security Center 데이터 연결
description: Azure 센티널에 Azure Security Center 데이터를 연결 하는 방법에 대해 알아봅니다.
author: rkarlin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 4c75d1870ca3b966acdd658c91c0af8fd7465bfb
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190871"
---
# <a name="connect-data-from-azure-security-center"></a>Azure Security Center에서 데이터 연결





Azure 센티널을 사용 하면 [Azure Security Center](../security-center/security-center-intro.md) 에서 경고를 연결 하 고 azure 센티널로 스트리밍할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure Security Center에서 경고를 내보내려면 스트리밍할 로그의 구독에 보안 읽기 권한자 역할이 있어야 합니다.

- 구독에서 실행 중인 [Azure Security Center 표준 계층이](../security-center/security-center-pricing.md) 있어야 합니다. 그렇지 않은 경우 [구독을 표준으로 업그레이드](https://azure.microsoft.com/pricing/details/security-center/)합니다.



## <a name="connect-to-azure-security-center"></a>Azure Security Center에 연결

1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **Azure Security Center** 타일을 클릭 합니다.

1. 오른쪽에서 경고를 Azure 센티널로 스트리밍할 각 구독 옆의 **연결** 을 클릭 합니다. 각 구독을 Azure Security Center 표준 계층으로 업그레이드 하 여 경고를 Azure 센티널로 스트리밍하려면 확인 하세요.

1. Azure Security Center 경고가 자동으로 Azure 센티널에서 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다. **인시던트 만들기**에서 **사용**을 선택하여 연결된 보안 서비스에서 생성된 경고에서 인시던트가 자동으로 생성되는 기본 분석 규칙을 사용하도록 설정합니다. 그런 다음, **Analytics** 및 **활성 규칙**에서 이 규칙을 편집할 수 있습니다.

3. **연결**을 클릭합니다.

4. Azure Security Center 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Securityalert**를 검색 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Security Center를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
