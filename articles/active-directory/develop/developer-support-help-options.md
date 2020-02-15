---
title: Azure AD 앱 개발자를 위한 지원 및 도움말 옵션 | Microsoft Docs
description: Microsoft ID(Azure Active Directory 및 Microsoft 계정)와 통합되는 애플리케이션을 만들 때 개발과 관련된 질문 및 문제에 대한 지원 및 도움말을 얻는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 89bf49fb44d8575b251a0b33698bc4ce8425cc2b
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160970"
---
# <a name="support-and-help-options-for-developers"></a>개발자를 위한 지원 및 도움말 옵션

Azure AD(Azure Active Directory), Microsoft ID 또는 Microsoft Graph API와 통합을 방금 시작한 경우 또는 애플리케이션에 새 기능을 구현 중인 경우 커뮤니티에서 도움을 얻거나 개발자로서 사용할 수 있는 지원 옵션을 파악해야 할 때가 있습니다. 이 문서는

> [!div class="checklist"]
> * 커뮤니티에서 질문에 응답하지 않았는지 여부 또는 구현하려는 기능에 대한 기존 문서가 이미 있는지 여부를 검색하는 방법을 포함하여 이러한 옵션을 파악하는 데 도움이 됩니다.
> * 특정 문제를 디버그하기 위해 지원 도구를 사용하려는 경우도 있습니다.
> * 필요한 답변을 찾을 수 없으면 *Stack Overflow*에서 질문할 수 있습니다.
> * 인증 라이브러리 중 하나에서 문제가 발생하면 *GitHub* 문제를 제기합니다.
> * 마지막으로 누군가와 대화할 필요가 있으면 지원 요청을 열 수 있습니다.

## <a name="search"></a>검색

개발 관련 질문이 있으면 문서, [GitHub 샘플](https://github.com/azure-samples) 또는 [Stack Overflow](https://www.stackoverflow.com) FAQ에서 답변을 찾을 수 있습니다.

### <a name="scoped-search"></a>범위 지정 검색

빠른 결과를 얻으려면 즐겨 찾는 검색 엔진에서 다음 쿼리를 사용하여 Stack Overflow, 문서 및 코드 샘플로 검색 범위를 지정합니다.

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

여기서 *{Your Search Terms}* 는 검색 키워드입니다.

## <a name="use-the-development-support-tools"></a>개발 지원 도구 사용

| 도구  | Description  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | ID 또는 액세스 토큰을 붙여넣어 클레임 이름 및 값을 디코드합니다. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Microsoft Graph API에 대해 요청을 하고 응답을 확인할 수 있는 도구입니다. |

## <a name="post-a-question-to-stack-overflow"></a>Stack Overflow에 질문을 게시합니다.

Stack Overflow는 개발 관련 질문에 대한 기본 설정 채널입니다. 여기서는 개발자 커뮤니티 구성원과 Microsoft 팀 구성원이 문제 해결에 직접 참여하고 있습니다.

검색을 통해 질문에 대한 답변을 찾을 수 없으면 새 질문을 Stack Overflow에 제출합니다. 질문할 때 다음 태그 중 하나를 사용하면 커뮤니티에서 질문을 더 빠르게 식별하고 답변하는 데 도움이 됩니다.

|구성 요소/영역  | 태그들 |
|---------|---------|
| ADAL 라이브러리 | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| MSAL 라이브러리     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| OWIN 미들웨어  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| 인증 또는 권한 부여 주제와 관련된 다른 모든 영역 | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Stack Overflow의 다음 게시물에는 질문하는 방법과 소스 코드를 추가하는 방법에 대한 팁이 포함되어 있습니다. 커뮤니티 구성원이 질문을 빠르게 평가하고 답변할 가능성을 높이려면 다음 지침을 따르세요.

* [좋은 질문을 하는 방법](https://stackoverflow.com/help/how-to-ask)
* [최소한의 완전하고 검증 가능한 예제를 만드는 방법](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>GitHub 문제 만들기

라이브러리와 관련된 버그 또는 문제가 있으면 GitHub 리포지토리에서 문제를 제기합니다. 라이브러리는 오픈 소스이므로 끌어오기 요청을 제출할 수도 있습니다.

라이브러리 및 해당 GitHub 리포지토리 목록은 다음을 참조 하세요.

* [ADAL (Azure Active Directory Authentication Library)](../azuread-dev/active-directory-authentication-libraries.md) 라이브러리 및 GitHub 리포지토리
* [MSAL (Microsoft 인증 라이브러리)](reference-v2-libraries.md) 라이브러리 및 GitHub 리포지토리

## <a name="open-a-support-request"></a>지원 요청 열기

누군가와 대화할 필요가 있으면 지원 요청을 열 수 있습니다. Azure 고객인 경우 몇 가지 지원 옵션을 사용할 수 있습니다. 계획을 비교하려면 [이 페이지](https://azure.microsoft.com/support/plans/)를 참조하세요. 개발자 지원은 Azure 고객에게도 제공됩니다. 개발자 지원 계획을 구매하는 방법에 대한 자세한 내용은 [이 페이지](https://azure.microsoft.com/support/plans/developer/)를 참조하세요.

* Azure 지원 계획이 이미 있는 경우 [여기서 지원 요청을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

* Azure 고객이 아닌 경우 [상용 지원](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)을 통해 Microsoft 지원 요청을 열 수도 있습니다.

[가상 에이전트](https://support.microsoft.com/contactus/?ws=support)를 사용하여 지원을 받거나 질문할 수도 있습니다.
