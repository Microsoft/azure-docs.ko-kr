---
title: 자습서 - Azure 이벤트 허브로 로그 스트리밍 | Microsoft Docs
description: 이벤트 허브로 Azure Active Directory 로그를 푸시하도록 Azure Diagnostics를 설정하는 방법 알아보기
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cf35b2c15c0d85c93e69ef4e3b6c76932cbd75d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229381"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub"></a>자습서: Azure 이벤트 허브로 Azure Active Directory 로그 스트리밍

이 자습서에서는 Azure 이벤트 허브로 Azure AD(Azure Active Directory) 로그를 스트리밍하도록 Azure Monitor 진단 설정을 지정하는 방법을 알아봅니다. 이 메커니즘을 사용하여 로그를 QRadar 및 Splunk 같은 타사 SIEM(보안 정보 및 이벤트 관리) 도구와 통합할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항 

이 기능을 사용하려면 다음이 필요합니다.

* Azure 구독 Azure 구독이 없으면 [평가판](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
* Azure AD 테넌트.
* Azure AD 테넌트의 *글로벌 관리자* 또는 *보안 관리자*인 사용자.
* Azure 구독의 Event Hubs 네임스페이스 및 이벤트 허브. [이벤트 허브를 만드는 방법](../../event-hubs/event-hubs-create.md)을 알아봅니다.

## <a name="stream-logs-to-an-event-hub"></a>이벤트 허브에 로그 스트림

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. **Azure Active Directory** > **모니터링** > **감사 로그**를 선택합니다. 

3. **설정 내보내기**를 선택합니다.  
    
4. **진단 설정** 창에서 다음 중 하나를 수행합니다.
    * 기존 설정을 변경하려면 **설정 편집**을 선택합니다.
    * 새 설정을 추가하려면 **진단 설정 추가**를 선택합니다.  
      최대 세 개의 설정을 지정할 수 있습니다.

      ![설정 내보내기](./media/quickstart-azure-monitor-stream-logs-to-event-hub/ExportSettings.png)

5. **이벤트 허브로 스트림** 확인란을 선택한 다음, **이벤트 허브/구성**을 선택합니다.

6. 로그를 라우팅하려는 Azure 구독 및 Event Hubs 네임스페이스를 선택합니다.  
    구독 및 Event Hubs 네임스페이스는 로그가 스트리밍되는 Azure AD 테넌트와 연결되어야 합니다. 또한 로그를 전송해야 하는 Event Hubs 네임스페이스 내에 이벤트 허브를 지정할 수 있습니다. 이벤트 허브가 지정되지 않으면 기본 이름 **insights-logs-audit**를 사용하여 네임스페이스 내에 이벤트 허브가 생성됩니다.

7. **확인**을 선택하여 이벤트 허브 구성을 종료합니다.

8. 다음 중 하나 또는 둘 모두를 수행합니다.
    * 스토리지 계정에 감사 로그를 보내려면 **AuditLogs** 확인란을 선택합니다. 
    * 스토리지 계정에 로그인 로그를 보내려면 **SignInLogs** 확인란을 선택합니다.

9. **저장**을 선택하여 설정을 저장합니다.

    ![진단 설정](./media/quickstart-azure-monitor-stream-logs-to-event-hub/DiagnosticSettings.png)

10. 약 15분 후 이벤트 허브에 이벤트가 표시되는지 확인합니다. 포털에서 이벤트 허브로 이동하여 **들어오는 메시지** 수가 0보다 큰지 확인하면 됩니다. 

    ![감사 로그](./media/quickstart-azure-monitor-stream-logs-to-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>이벤트 허브에서 데이터에 액세스

이벤트 허브에 데이터가 표시되면 다음과 같은 두 가지 방법으로 데이터에 액세스하여 읽을 수 있습니다.

* **지원되는 SIEM 도구를 구성합니다**. 이벤트 허브에서 데이터를 읽기 위해 대부분의 도구는 이벤트 허브 연결 문자열과 Azure 구독을 읽을 수 있는 특정 권한을 요구합니다. Azure Monitor와 통합된 타사 도구도 포함되지만 이에 국한되지는 않습니다.
    
    * **ArcSight**: Splunk와 Azure AD 로그를 통합하는 방법에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure Active Directory 로그를 ArcSight와 통합](howto-integrate-activity-logs-with-arcsight.md)을 참조하세요.
    
    * **Splunk**: Splunk와 Azure AD 로그를 통합하는 방법에 대한 자세한 내용은 [Azure Monitor를 사용하여 Splunk와 Azure AD 로그 통합](./howto-integrate-activity-logs-with-splunk.md)을 참조하세요.
    
    * **IBM QRadar**: DSM 및 Azure 이벤트 허브 프로토콜은 [IBM 지원](https://www.ibm.com/support)에서 다운로드할 수 있습니다. Azure와 통합하는 방법에 대한 자세한 내용은 [IBM QRadar 보안 인텔리전스 플랫폼 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0) 사이트를 참조하세요.
    
    * **Sumo Logic**: 이벤트 허브의 데이터를 사용하도록 Sumo Logic을 설정하는 방법은 [Azure AD 앱을 설치하고 대시보드 보기](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards)를 참조하세요. 

* **사용자 지정 도구 설정** 현재 SIEM이 Azure Monitor 진단에서 아직 지원되지 않는 경우 Event Hubs API를 사용하여 사용자 지정 도구를 설정할 수 있습니다. 자세한 내용은 [이벤트 허브에서 메시지 수신 시작](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

* [Azure Monitor를 사용하여 ArcSight와 Azure Active Directory 로그 통합](howto-integrate-activity-logs-with-arcsight.md)
* [Azure Monitor를 사용하여 Splunk와 Azure AD 로그 통합](./howto-integrate-activity-logs-with-splunk.md)
* [Azure Monitor를 사용하여 SumoLogic과 Azure AD 로그 통합](howto-integrate-activity-logs-with-sumologic.md)
* [Azure Monitor에서 감사 로그 스키마 해석](reference-azure-monitor-audit-log-schema.md)
* [Azure Monitor에서 로그인 로그 스키마 해석](reference-azure-monitor-sign-ins-log-schema.md)