---
title: Azure 센티널에 Office 365 데이터 연결 | Microsoft Docs
description: Azure 센티널에 Office 365 데이터를 연결 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: df5aade7244f69e7264f901364ecc164351eec50
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815793"
---
# <a name="connect-data-from-office-365-logs"></a>Office 365 로그의 데이터 연결



한 번의 클릭으로 [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) 의 감사 로그를 Azure 센티널로 스트리밍할 수 있습니다. 여러 테넌트의 감사 로그를 Azure 센티널의 단일 작업 영역으로 스트리밍할 수 있습니다. Office 365 활동 로그 커넥터는 지속적인 사용자 활동에 대 한 통찰력을 제공 합니다. Office 365에서 다양 한 사용자, 관리자, 시스템 및 정책 작업 및 이벤트에 대 한 정보를 얻을 수 있습니다. Office 365 로그를 Azure 센티널에 연결 하면이 데이터를 사용 하 여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사 프로세스를 개선할 수 있습니다.

> [!IMPORTANT]
> E3 라이선스가 있는 경우 Office 365 관리 활동 API를 통해 데이터에 액세스 하려면 먼저 Office 365 조직에 대해 통합 된 감사 로깅을 사용 하도록 설정 해야 합니다. 이렇게 하려면 Office 365 감사 로그를 설정 합니다. 지침은 [Office 365 감사 로그 검색 설정 또는 해제](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)를 참조 하세요. 자세한 내용은 [Office 365 관리 활동 API 참조](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)를 참조 하세요.

## <a name="prerequisites"></a>전제 조건

- 테넌트의 전역 관리자 또는 보안 관리자 여야 합니다.
- Azure 센티널에 로그인 하 여 연결을 만드는 컴퓨터에서 포트 4433이 웹 트래픽에 대해 열려 있는지 확인 합니다. 연결이 설정 되 면이 포트를 다시 닫을 수 있습니다.
- 테넌트에 Office 365 E3 또는 Office 365 E5 라이선스가 없는 경우 다음 프로세스 중 하나를 사용 하 여 테넌트에서 통합 감사를 사용 하도록 설정 해야 합니다.
    - [Set AdminAuditLogConfig cmdlet을 사용 하](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) 고 "UnifiedAuditLogIngestionEnabled" 매개 변수를 사용 하도록 설정 합니다.
    - [또는 보안 및 준수 센터 UI를 사용](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)합니다.

## <a name="connect-to-office-365"></a>Office 365에 연결

1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **Office 365** 타일을 클릭 합니다.

2. 아직 사용 하도록 설정 하지 않은 경우 **데이터 커넥터** 블레이드로 이동 하 고 **Office 365** 커넥터를 선택 하 여 수행할 수 있습니다. 여기에서 **커넥터 열기 페이지** 를 클릭 하 고 구성 섹션에서 **작업 영역에서 Office 365 솔루션** 사용을 클릭 **하 여 사용** 하도록 설정할 수 있습니다. 이미 사용 하도록 설정 된 경우 연결 화면에서 이미 사용 하도록 설정 된 것으로 식별 됩니다.
1. Office 365에서는 여러 테넌트의 데이터를 Azure 센티널로 스트리밍할 수 있습니다. 연결 하려는 각 테넌트에 대해 **Azure 센티널에**테넌트 연결 아래에 테넌트를 추가 합니다. 
1. Active Directory 화면이 열립니다. Azure 센티널에 연결 하려는 각 테넌트에서 전역 관리자에 게 인증 하 고 해당 로그를 읽을 수 있도록 Azure 센티널에 사용 권한을 제공 하 라는 메시지가 표시 됩니다. 
5. 테넌트 목록에는 Azure AD 디렉터리 ID (테넌트 ID)와 Exchange 및 Sharepoint 로그에 대 한 두 개의 확인란이 표시 됩니다. 센티널에서 수집 하려는 모든 서비스 또는 나열 된 서비스를 선택할 수 있습니다. 현재 Azure 센티널은 기존 Office365 services 내에서 Exchange 및 SharePoint 로그를 지원 합니다.

4. 서비스 (Exchange, sharepoint 등)를 선택한 후에는 페이지의 테넌트 추가 프레임에서 저장을 클릭할 수 있습니다. 

3. Office 365 로그에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **작업**을 검색 합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Office 365을 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.

