---
title: Azure 센티널에 Azure Information Protection 연결
description: Azure Information Protection 데이터 커넥터를 구성 하 여 Azure Information Protection에서 Azure 센티널로 로깅 정보를 스트리밍합니다. 
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 160912ef53b5c079d026c5d16230f49eadf66366
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794435"
---
# <a name="connect-data-from-azure-information-protection"></a>Azure Information Protection에서 데이터 연결

> [!IMPORTANT]
> Azure 센티널의 Azure Information Protection 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Information Protection 데이터 커넥터를 구성 하 여 [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) 에서 Azure 센티널로 로깅 정보를 스트리밍할 수 있습니다. Azure Information Protection은 클라우드 또는 온-프레미스에 저장 되어 있든 상관 없이 중요 한 데이터를 제어 하 고 보호 하는 데 도움이 됩니다.

[Azure Information Protection에 대 한 중앙 보고가](https://docs.microsoft.com/azure/information-protection/reports-aip) 이미 구성 되어 있으므로이 서비스의 로깅 정보가 현재 Azure 센티널에 대해 선택한 것과 동일한 Log Analytics 작업 영역에 저장 되어 있는 경우이 데이터 커넥터의 구성을 건너뛸 수 있습니다. Azure Information Protection의 로깅 정보는 Azure 센티널에서 이미 사용할 수 있습니다.

그러나 Azure Information Protection의 로깅 정보가 현재 Azure 센티널에 대해 선택한 것과 다른 Log Analytics 작업 영역으로 이동 하는 경우 다음 중 하나를 수행 합니다.

- Azure 센티널에서 선택한 작업 영역을 변경 합니다.

- 이 데이터 커넥터를 구성 하 여 수행할 수 있는 Azure Information Protection에 대 한 작업 영역을 변경 합니다.
    
    작업 영역을 변경 하는 경우 Azure Information Protection에 대 한 새 보고 데이터가 이제 Azure 센티널에 사용 하는 작업 영역에 저장 되며, Azure 센티널에서 기록 데이터를 사용할 수 없습니다. 또한 사용자 지정 쿼리, 경고 또는 REST Api에 대해 이전 작업 영역을 구성 하는 경우 Azure Information Protection에 사용 하려면 Azure 센티널 작업 영역에 대해 이러한 작업 영역을 다시 구성 해야 합니다. Azure Information Protection를 사용 하는 클라이언트와 서비스에는 재구성이 필요 하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 다음은 테 넌 트에 대 한 Azure AD 관리자 역할 중 하나입니다. 
    - Azure Information Protection 관리자
    - 보안 관리자
    - 규정 준수 관리자
    - 규정 준수 데이터 관리자
    - 전역 관리자
    
    > [!NOTE]
    > 테 넌 트가 [통합 레이블 지정 플랫폼](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)에 있는 경우 Azure Information Protection 관리자 역할을 사용할 수 없습니다.
    
    이러한 관리자 역할은 Azure Information Protection 커넥터를 구성 하는 데만 필요 하며 Azure 센티널이 Azure Information Protection에 연결 된 경우에는 필요 하지 않습니다.

- Azure 센티널 및 Azure Information Protection에 사용 중인 Log Analytics 작업 영역에 대 한 읽기 및 쓰기 권한입니다.

- Azure Information Protection Azure Portal에 추가 되었습니다. 이 단계에 대 한 도움이 필요한 경우 [Azure Portal에 Azure Information Protection 추가](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)를 참조 하세요.

## <a name="connect-to-azure-information-protection"></a>Azure Information Protection에 연결

Azure Information Protection에 대 한 Log Analytics 작업 영역을 구성 하지 않았거나 Azure Information Protection 로깅 정보를 저장 하는 작업 영역을 변경 해야 하는 경우 다음 지침을 따르십시오.

1. Azure 센티널에서 **데이터 커넥터**  >  **Azure Information Protection (미리 보기)** 를 선택 합니다.

2. **커넥터 페이지 열기**를 선택 합니다.

3. **구성**아래에서 **연결 Azure Information Protection 로그**를 선택 합니다.

4. **분석 구성 (미리 보기)** 블레이드에서 현재 Azure 센티널에 사용 하는 작업 영역을 선택 합니다. 다른 작업 영역을 선택 하는 경우 Azure Information Protection의 보고 데이터를 Azure 센티널에서 사용할 수 없습니다.

5. 작업 영역을 선택한 후 **확인**을 선택 합니다. 커넥터 **상태가** **연결 됨**으로 변경 됩니다.

6. Azure Information Protection의 보고 데이터는 선택한 작업 영역의 **InformationProtectionLogs_CL** 테이블에 저장 됩니다. 
    
    이 보고 데이터에 대 한 Azure Monitor에서 관련 스키마를 사용 하려면 **InformationProtectionEvents**를 검색 합니다. 이러한 이벤트 함수에 대 한 자세한 내용은 Azure Information Protection 설명서에서 [이벤트 함수에 대 한 친숙 한 스키마 참조](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) 섹션을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Information Protection를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
