---
title: What If 도구를 사용하여 조건부 액세스 문제 해결 - Azure Active Directory
description: 여기에서 조건부 액세스 정책이 적용된 대상 및 이유를 찾을 수 있습니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb51e8590e2859a9fe8692f79c5b1238ed5a2098
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948184"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>What If 도구를 사용하여 조건부 액세스 문제 해결

조건부 액세스의 [What If 도구](what-if-tool.md)는 특정 상황에서 정책이 사용자에게 적용되거나 적용되지 않은 이유 또는 정책이 알려진 상태에서 적용되는지 여부를 파악하려는 경우 강력합니다.

What If 도구는 **Azure Portal** > **Azure Active Directory** > **조건부 액세스** > **What If**에 있습니다.

![기본 상태의 조건부 액세스 What If 도구](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> What If 도구는 현재 보고서 전용 모드에서 정책을 평가하지 않습니다.

## <a name="gathering-information"></a>정보 수집

What If 도구를 시작하려면 **사용자**만 있으면 됩니다. 

다음 추가 정보는 선택 사항이지만 특정 사례의 범위를 좁히는 데 도움이 됩니다.

* 클라우드 앱 또는 작업
* IP 주소 
* 국가/지역
* 디바이스 플랫폼
* 클라이언트 앱(미리 보기)
* 디바이스 상태(미리 보기) 
* 로그인 위험

이 정보는 사용자, 디바이스 또는 Azure AD 로그인 로그에서 수집할 수 있습니다.

## <a name="generating-results"></a>결과 생성

이전 섹션에서 수집한 조건을 입력하고 **What If**를 선택하여 결과 목록을 생성합니다. 

언제든지 **다시 설정**을 선택하여 모든 조건 입력을 지우고 기본 상태로 돌아갈 수 있습니다.

## <a name="evaluating-results"></a>결과 평가

### <a name="policies-that-will-apply"></a>적용되는 정책

이 목록은 지정된 조건을 적용할 조건부 액세스 정책을 보여 줍니다. 이 목록에는 적용되는 권한 부여 및 세션 컨트롤이 둘 다 포함됩니다. 예를 들어 특정 애플리케이션에 액세스하려면 다단계 인증을 요구하는 기능이 포함됩니다.

### <a name="policies-that-will-not-apply"></a>적용되지 않는 정책

이 목록은 조건이 적용된 경우 적용되지 않는 조건부 액세스 정책을 보여 줍니다. 이 목록에는 정책 및 정책이 적용되지 않는 이유가 포함됩니다. 예를 들어 정책에서 제외될 수 있는 사용자 및 그룹이 포함됩니다.

## <a name="use-case"></a>사용 사례

대부분 조직에서는 네트워크 위치를 기반으로 정책을 만들어 신뢰할 수 있는 위치를 허용하고 액세스가 발생하지 않아야 하는 위치를 차단합니다.

적절하게 구성되었는지 유효성을 검사하기 위해 관리자는 What If 도구를 사용하여 허용해야 하는 위치 및 거부해야 하는 위치의 액세스를 모방할 수 있습니다.

[![블록 액세스를 포함 하는 결과를 보여 주는 What If 도구](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png#lightbox)

이 경우 Contoso가 해당 위치의 액세스를 차단했으므로 사용자는 북한으로 여행 시 클라우드 앱 액세스가 차단됩니다.

이 테스트를 확장하여 범위를 좁힐 수 있는 다른 데이터 요소를 통합할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [조건부 액세스란?](overview.md)
* [Azure Active Directory Identity Protection이란?](../identity-protection/overview-identity-protection.md)
* [디바이스 ID란?](../devices/overview.md)
* [작동 방법: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) 문서를 참조하세요