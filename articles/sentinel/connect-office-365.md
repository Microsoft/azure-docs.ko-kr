---
title: Azure 센티널에 Office 365 로그 연결 | Microsoft Docs
description: Office 365 로그 커넥터를 사용 하 여 Exchange, 팀 및 SharePoint에서 OneDrive를 비롯 한 진행 중인 사용자 및 관리자 활동에 대 한 정보를 가져오는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: d6b59de048cdf00d352c4f488ecb51bfdf83640f
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89178929"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Azure 센티널에 Office 365 로그 연결

[Office 365](https://docs.microsoft.com/office/) 로그 커넥터는 **Exchange** 및 **SharePoint** ( **OneDrive**포함)에서 진행 중인 사용자 및 관리 활동에 대 한 Azure 센티널 정보를 제공 하 고, 현재 **팀** 에서도 제공 합니다. 이 정보에는 작업을 수행한 사용자의 세부 정보 뿐만 아니라 파일 다운로드, 보낸 액세스 요청, 그룹 이벤트 변경 내용, 사서함 작업, 팀 이벤트 (예: 채팅, 팀, 멤버 및 채널 이벤트)와 같은 작업에 대 한 세부 정보가 포함 됩니다. Office 365 로그를 Azure 센티널에 연결 하면 통합 문서에서이 데이터를 보고 분석 하며, 사용자 지정 경고를 만들어 쿼리하고,이를 통합 하 여 조사 프로세스를 개선 하 고, Office 365 보안에 대 한 자세한 정보를 제공할 수 있습니다.

> [!IMPORTANT]
> **Microsoft 팀 로그의** Office 365 로그 커넥터 확장은 현재 공개 미리 보기 상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- 테넌트의 전역 관리자 또는 보안 관리자여야 합니다.

- Office 365 배포는 Azure 센티널 작업 영역과 동일한 테 넌 트에 있어야 합니다.

> [!IMPORTANT]
> - 커넥터가 Office 365 관리 활동 API를 통해 데이터에 액세스할 수 있도록 하려면 Office 365 배포에 대해 **통합 된 감사 로깅을** 사용 하도록 설정 해야 합니다. 보유 하 고 있는 Office 365/Microsoft 365 라이선스의 유형에 따라 기본적으로 사용 하거나 사용 하지 않을 수 있습니다. 라이선스 유형에 따라 통합 감사 로깅 상태를 확인 하려면 [Office 365 보안 및 규정 준수 센터](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) 를 참조 하세요.
> - Office 365 통합 감사 로깅의 현재 상태를 수동으로 사용 하도록 설정 하 고, 사용 하지 않도록 설정 하 고, 확인할 수도 있습니다. 지침은 [Office 365 감사 로그 검색 설정 또는 해제](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)를 참조하세요.
> - 자세한 내용은 [Office 365 관리 활동 API 참조](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)를 참조 하세요.


   > [!NOTE]
   > 위에서 설명한 것 처럼 **데이터 형식**아래의 커넥터 페이지에 표시 되는 것 처럼 Azure 센티널 Office 365 커넥터는 현재 Microsoft Exchange 및 SharePoint (OneDrive 포함) 뿐만 아니라 **팀**에서도 감사 로그 수집을 지원 합니다. 그러나 [다른 Office 데이터](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) 를 Azure 센티널로 가져오는 데 관심이 있는 경우에는 몇 가지 외부 솔루션이 있습니다. 

## <a name="enable-the-office-365-log-connector"></a>Office 365 로그 커넥터 사용

### <a name="instructions-tab"></a>명령 탭

1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터**를 선택 합니다.

1. **데이터 커넥터** 갤러리에서 **Office 365**을 선택 하 고 미리 보기 창에서 **커넥터 페이지 열기** 를 선택 합니다.

1. **구성**섹션 아래에서 Azure 센티널에 연결 하려는 Office 365 활동 로그의 확인란을 표시 하 고 **변경 내용 적용**을 클릭 합니다. 

   > [!NOTE]
   > 이를 지 원하는 이전 버전의 Office 365 커넥터를 사용 하 여 이전에 Azure 센티널에 여러 테 넌 트를 연결한 경우 각 테 넌 트에서 수집 하는 로그를 보고 수정할 수 있습니다. 다른 테넌트를 추가할 수는 없지만 이전에 추가된 테넌트를 제거할 수는 있습니다.

### <a name="next-steps-tab"></a>다음 단계 탭

- SharePoint, OneDrive, Exchange 및 팀 로그 데이터에 대 한 정보를 얻기 위해 **Office 365** 로그 커넥터와 함께 제공 되는 권장 통합 문서, 쿼리 샘플 및 분석 규칙 템플릿을 참조 하세요.

- **로그**에서 Office 365 로그 데이터를 수동으로 쿼리하려면 `OfficeActivity` 쿼리 창의 첫 번째 줄에를 입력 합니다.
   - 특정 로그 유형에 대 한 쿼리를 필터링 하려면 `| where OfficeWorkload == "<logtype>"` 쿼리의 두 번째 줄에를 입력 *\<logtype\>* 합니다. 여기서은,, `SharePoint` `OneDrive` `Exchange` 또는 `MicrosoftTeams` 입니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Office 365를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아보세요.
- [기본 제공](tutorial-detect-threats-built-in.md) 또는 [사용자 지정](tutorial-detect-threats-custom.md) 규칙을 사용 하 여 Azure 센티널에서 위협을 검색 하기 시작 합니다.

