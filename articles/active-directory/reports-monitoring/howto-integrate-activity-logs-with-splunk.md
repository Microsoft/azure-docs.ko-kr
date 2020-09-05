---
title: Azure Monitor를 사용 하 여 Splunk 통합 | Microsoft Docs
description: 를 사용 하 여 SumoLogic와 Azure Active Directory 로그를 통합 하는 방법을 알아봅니다 Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/10/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61cae487c588c7649de638d9ea6d3111bfbe9e1a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229687"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>방법: Azure Monitor를 사용 하 여 Splunk와 Azure Active Directory 로그 통합

이 문서에서는 Azure Monitor를 사용하여 Splunk와 Azure Active Directory(Azure AD) 로그를 통합하는 방법을 알아봅니다. 먼저 Azure 이벤트 허브에 로그를 라우트한 다음, Splunk와 이벤트 허브를 통합합니다.

## <a name="prerequisites"></a>전제 조건

이 기능을 사용하려면 다음이 필요합니다.

- Azure AD 활동 로그를 포함하는 Azure 이벤트 허브입니다. [활동 로그를 이벤트 허브로 스트림](./tutorial-azure-monitor-stream-logs-to-event-hub.md)하는 방법을 알아봅니다. 

-  [Splunk에 대 한 추가 Microsoft Azure](https://splunkbase.splunk.com/app/3757/)입니다. 

## <a name="integrate-azure-active-directory-logs"></a>Azure Active Directory 로그 통합 

1. Splunk 인스턴스를 열고 **데이터 요약**을 선택합니다.

    !["데이터 요약" 단추](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. **Sourcetypes** 탭을 선택한 다음, **amal: aadal:audit**을 선택

    ![데이터 요약 Sourcetypes 탭](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Azure AD 활동 로그가 다음 그림에 표시됩니다.

    ![활동 로그](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Splunk 인스턴스에 추가 기능을 설치할 수 없는 경우(예: 프록시를 사용 중이거나 Splunk Cloud에서 실행 중인 경우) 이러한 이벤트를 Splunk HTTP 이벤트 수집기로 전달할 수 있습니다. 이렇게 하려면 이벤트 허브에서 새 메시지에 의해 트리거되는 이 [Azure 함수](https://github.com/Microsoft/AzureFunctionforSplunkVS)를 사용합니다. 
>

## <a name="next-steps"></a>다음 단계

* [Azure Monitor에서 감사 로그 스키마 해석](reference-azure-monitor-audit-log-schema.md)
* [Azure Monitor에서 로그인 로그 스키마 해석](reference-azure-monitor-sign-ins-log-schema.md)
* [질문과 대답 및 알려진 문제](concept-activity-logs-azure-monitor.md#frequently-asked-questions)