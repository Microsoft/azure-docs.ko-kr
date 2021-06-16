---
title: 자습서 - 스토리지 계정에 디렉터리 로그 보관 | Microsoft Docs
description: 스토리지 계정으로 Azure Active Directory 로그를 푸시하도록 Azure Diagnostics를 설정하는 방법 알아보기
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
ms.openlocfilehash: cd278968eeb430bf57f279bb8b7e5d4ee9248ce1
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111965712"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account"></a>자습서: Azure Storage 계정에 Azure AD 로그 보관

이 자습서에서는 Azure AD(Azure Active Directory) 로그를 Azure Storage 계정으로 라우팅하도록 Azure Monitor 진단 설정을 지정하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항 

이 기능을 사용하려면 다음이 필요합니다.

* Azure 구독과 Azure Storage 계정. Azure 구독이 없으면 [평가판](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
* Azure AD 테넌트.
* Azure AD 테넌트의 *글로벌 관리자* 또는 *보안 관리자* 인 사용자.

## <a name="archive-logs-to-an-azure-storage-account"></a>Azure Storage 계정에 로그 보관

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. **Azure Active Directory** > **작업** > **감사 로그** 를 선택합니다. 

3. **설정 내보내기** 를 선택합니다. 

4. **진단 설정** 창에서 다음 중 하나를 수행합니다.
   * 기존 설정을 변경하려면 **설정 편집** 을 선택합니다.
   * 새 설정을 추가하려면 **진단 설정 추가** 를 선택합니다.  
     최대 세 개의 설정을 지정할 수 있습니다. 

     ![설정 내보내기](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. 이름을 보면 용도를 알 수 있는 설정 이름을 입력합니다(예: *Azure Storage 계정으로 보내기*). 

6. **스토리지 계정에 보관** 확인란을 선택하고 **스토리지 계정** 을 선택합니다. 

7. 로그를 라우팅하려는 Azure 구독 및 스토리지 계정을 선택합니다.
 
8. **확인** 을 선택하여 구성을 종료합니다.

9. 다음 중 하나 또는 둘 모두를 수행합니다.
    * 스토리지 계정에 감사 로그를 보내려면 **AuditLogs** 확인란을 선택합니다. 
    * 스토리지 계정에 로그인 로그를 보내려면 **SignInLogs** 확인란을 선택합니다.

10. 슬라이더를 사용하여 로그 데이터의 보존 기간을 설정합니다. 기본적으로 이 값은 *0* 이며, 로그가 스토리지 계정에 무기한 보존된다는 뜻입니다. 다른 값으로 설정하면 선택된 일수보다 오래된 이벤트는 자동으로 정리됩니다.

11. **저장** 을 선택하여 설정을 저장합니다.

    ![진단 설정](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

12. 약 15분 후 스토리지 계정으로 로그가 푸시되었는지 확인합니다. [Azure Portal](https://portal.azure.com)로 이동하고, **스토리지 계정** 을 선택하고, 이전에 사용한 스토리지 계정을 선택하고, **Blob** 을 선택합니다. **감사 로그** 에서 **insights-log-audit** 를 선택합니다. **로그인 로그** 에 **insights-logs-signin** 을 선택합니다.

    ![스토리지 계정](./media/quickstart-azure-monitor-route-logs-to-storage-account/StorageAccount.png)

## <a name="next-steps"></a>다음 단계

* [Azure Monitor에서 감사 로그 스키마 해석](./overview-reports.md)
* [Azure Monitor에서 로그인 로그 스키마 해석](reference-azure-monitor-sign-ins-log-schema.md)
* [질문과 대답 및 알려진 문제](concept-activity-logs-azure-monitor.md#frequently-asked-questions)