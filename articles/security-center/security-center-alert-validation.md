---
title: Azure Security Center에서 경고 유효성 검사 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 보안 경고를 유효성 검사하는 방법을 설명합니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: 009f5fe7243b8ce597c2be9f9c6874cdb56d103c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706064"
---
# <a name="alerts-validation-in-azure-security-center"></a>Azure Security Center에서 경고 유효성 검사
이 문서에서는 시스템이 Azure Security Center 경고에 대해 제대로 구성되었는지 확인하는 방법을 알아봅니다.

## <a name="what-are-security-alerts"></a>보안 경고란?
Security Center는 방화벽 및 엔드포인트 보호 솔루션과 같은 Azure 리소스, 네트워크 및 연결된 파트너 솔루션의 로그 데이터를 자동으로 수집하고 분석하며 통합하여 위협을 감지하고 사용자에게 경고해 줍니다. 보안 경고에 대한 자세한 내용은 [Azure 보안 센터에서 보안 경고 관리 및 대응](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)을 읽어보고 [Azure Security Center에서 보안 경고 관리 및 대응](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)에서 다양한 경고 유형에 대해 알아봅니다.

## <a name="alert-validation"></a>경고 유효성 검사
Security Center 에이전트가 컴퓨터에 설치된 경우 경고의 공격받은 리소스에 해당하는 컴퓨터에서 아래 단계를 따릅니다.

1. 실행 파일(예: calc.exe)을 컴퓨터의 바탕 화면 또는 사용자가 편리한 다른 디렉터리에 복사합니다.
2. 이 파일을 이름 **ASC_AlertTest_662jfi039N.exe**로 변경합니다.
3. 명령 프롬프트를 열고 이 파일을 인수(가짜 인수 이름)와 함께 실행합니다(예: *ASC_AlertTest_662jfi039N.exe -foo*).
4. 5~10분 정도 기다렸다가 Security Center 경고를 엽니다. 다음과 유사한 경고를 확인하게 됩니다.

    ![경고 유효성 검사](./media/security-center-alert-validation/security-center-alert-validation-fig2.png)

이 경고를 검토할 때는 [Arguments Auditing Enabled(인수 감사 사용됨)] 필드가 true로 나타나는지 확인합니다. False로 표시되는 경우 명령줄 인수 감사를 사용하도록 설정해야 합니다. 다음 명령줄을 사용하여 이 옵션을 설정할 수 있습니다.

*reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"*


> [!NOTE]
> 이 기능의 데모를 확인하려면 [Azure Security Center에서 경고 유효성 검사](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Alert-Validation-in-Azure-Security-Center) 비디오를 보세요.

## <a name="see-also"></a>참고 항목
이 문서에서는 경고 유효성 검사 프로세스에 대해 소개했습니다. 이제 유효성 검사에 익숙해졌으므로 다음 문서를 시도해 보세요.

* [Azure Security Center에서 보안 경고 관리 및 대응](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md). Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). 다양한 유형의 보안 경고에 대해 알아봅니다.
* [Azure Security Center 문제 해결 가이드](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Security Center의 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md)로 설정합니다. 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
