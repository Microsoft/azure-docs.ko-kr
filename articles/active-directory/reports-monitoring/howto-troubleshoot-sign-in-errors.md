---
title: 로그인 오류 보고서 문제를 해결 하는 방법 | Microsoft Docs
description: Azure Portal에서 Azure Active Directory 보고서를 사용하여 로그인 오류 문제를 해결하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec5fe7f62e8537a7f687202d365eb37d43b48b78
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008054"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>방법: Azure Active Directory 보고서를 사용하여 로그인 오류 문제 해결

Azure AD(Azure Active Directory)의 [ 로그인 보고서](concept-sign-ins.md)를 사용하면 다음을 포함하여 조직의 애플리케이션에 대한 액세스 관리와 관련된 질문에 대한 답변을 찾을 수 있습니다.

- 사용자의 로그인 패턴이란?
- 한 주 동안 얼마나 많은 사용자가 로그인했나요?
- 이러한 로그인의 상태란?


또한 로그인 보고서는 조직의 사용자에 대한 로그인 실패 문제를 해결하는 데 도움이 될 수 있습니다. 이 가이드에서는 로그인 보고서에서 로그인 실패를 격리하고, 이를 사용하여 실패의 근본 원인을 파악하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>선행 조건

다음 작업을 수행해야 합니다.

* 프리미엄(P1/P2) 라이선스가 있는 Azure AD 테넌트 [Azure Active Directory Premium 시작하기](../fundamentals/active-directory-get-started-premium.md)를 참조하여 Azure Active Directory 버전을 업그레이드하세요.
* **전역 관리자**, **보안 관리자**, **보안 읽기 권한자**또는 테넌트에 대 한 **보고서 읽기 권한자** 역할을 하는 사용자입니다. 또한 모든 사용자는 고유한 로그인에 액세스할 수 있습니다. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>로그인 보고서를 사용하여 로그인 오류 문제 해결

1. [Azure Portal](https://portal.azure.com)로 이동하고 디렉터리를 선택합니다.
2. **모니터링** 섹션에서 **Azure Active Directory**를 선택하고, **로그인**을 선택합니다. 
3. 제공된 필터를 사용하여 사용자 이름 또는 개체 식별자, 애플리케이션 이름 또는 날짜에 따라 오류를 좁힐 수 있습니다. 또한 **상태** **드롭다운에서 실패를 선택 하** 여 실패 한 로그인만 표시 합니다. 

    ![결과 필터링](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. 조사 하려는 실패 한 로그인을 식별 합니다. 실패 한 로그인에 대 한 자세한 정보가 포함 된 추가 세부 정보 창을 열려면이를 선택 합니다. **로그인 오류 코드** 및 **실패 이유**를 적어 둡니다. 

    ![레코드 선택](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. 또한 이 정보는 세부 정보 창의 **문제 해결 및 지원** 탭에서도 찾을 수 있습니다.

    ![문제 해결 및 지원](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. 실패 이유는 오류를 설명합니다. 예를 들어 위의 시나리오에서 실패 이유는 **잘못 된 사용자 이름 또는 암호 이거나 잘못 된 온-프레미스 사용자 이름 또는 암호**입니다. 해결 방법은 올바른 사용자 이름과 암호를 사용하여 다시 로그인하는 것입니다.

7. **로그인 오류 코드 참조**에서 이 예의 [50126](reference-sign-ins-error-codes.md) 오류 코드를 검색하여 수정 아이디어를 포함한 추가 정보를 얻을 수 있습니다. 

8. 다른 모든 방법이 실패하거나 추천된 작업 과정을 수행했는데도 문제가 지속되면 [문제 해결 및 지원](../fundamentals/active-directory-troubleshooting-support-howto.md) 탭의 단계에 따라 **지원 티켓을 엽니다**. 

## <a name="next-steps"></a>다음 단계

* [로그인 오류 코드 참조](reference-sign-ins-error-codes.md)
* [로그인 보고서 개요](concept-sign-ins.md)
