---
title: Azure Active Directory B2C에 대한 지원 | Microsoft Docs
description: Azure Active Directory B2C에 대한 지원 요청을 제출하는 방법입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e426837f7f06d6726bf3edc18e6c93a943dea884
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848266"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: 파일 지원 요청
다음 단계를 사용 하 여 Azure Portal에서 Azure Active Directory B2C (Azure AD B2C)에 대한 지원 요청을 수행할 수 있습니다.

1. B2C 테넌트를, 연결된 Azure 구독이 있는 다른 테넌트로 전환합니다. 일반적으로 후자는 직원 테넌트 또는 Azure 구독에 등록할 경우 만들어진 기본 테넌트입니다. 자세한 내용은 [Azure 구독과 Azure AD의 연관 관계](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)를 참조하세요.

    ![선택한 테넌트 선택이 강조 표시 된 Azure Portal](./media/support-options/support-switch-dir.png)

1. 테넌트를 전환한 후 **도움말 + 지원**을 클릭합니다.

    ![도움말 + 지원 타일이 Azure Portal 강조 표시 됨](./media/support-options/support-support.png)

1. **새 지원 요청**을 클릭합니다.

    ![Azure Portal에 강조 표시 된 새 지원 요청 타일](./media/support-options/support-new.png)

1. **기본 사항** 블레이드에서 이러한 세부 정보를 사용하고 **다음**을 클릭합니다.

    * **문제점 유형**은 **기술**입니다.
    * 적합한 **구독**을 선택합니다.
    * **서비스**는 **Active Directory**입니다.
    * 적합한 **지원 계획**을 선택합니다. 아직 선택하지 않은 경우 [여기](https://azure.microsoft.com/support/plans/)에서 하나를 등록할 수 있습니다.

     ![Azure Portal에서 다음 단추가 강조 표시 된 기본 페이지](./media/support-options/support-basics.png)

1. **문제** 블레이드에서 이러한 세부 정보를 사용하고 **다음**을 클릭합니다.

    * 적합한 **심각도** 수준을 선택합니다.
    * **문제 유형**은 **B2C**입니다.
    * 적합한 **범주**를 선택합니다.
    * **세부 정보** 필드에서 문제에 대해 설명합니다. B2C 테넌트 이름, 문제 설명, 오류 메시지, 상관 관계 ID(있는 경우) 등과 같은 세부 정보를 제공합니다.
    * **시간 프레임** 필드에서 문제가 발생한 날짜 및 시간(표준 시간대 포함)을 제공합니다.
    * **파일 업로드**에서 문제 해결에 도움이 될만한 모든 스크린샷과 파일을 업로드합니다.

     ![Azure Portal에서 다음 단추가 강조 표시 된 문제 페이지](./media/support-options/support-problem.png)

1. **연락처 정보** 블레이드에서 연락처 정보를 추가합니다. **만들기**를 클릭합니다.

    ![포털에서 만들기 단추가 강조 표시 된 연락처 정보 페이지](./media/support-options/support-contact.png)

1. 지원 요청을 제출한 후에 시작 보드에서 **도움말 + 지원** 및 **지원 요청 관리**를 차례로 클릭하여 모니터링할 수 있습니다.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>알려진 문제: B2C 테넌트의 컨텍스트에서 지원 요청 제출하기

위에서 설명한 2단계를 누락하고 B2C 테넌트의 컨텍스트에서 지원 요청을 만들려면 다음과 같은 오류가 표시됩니다.

> [!IMPORTANT]
> B2C 테넌트에서 새 Azure 구독에 등록하려 하지 마세요.

![에 구독 오류가 표시 되지 않았습니다 Azure Portal](./media/support-options/support-no-sub.png)
