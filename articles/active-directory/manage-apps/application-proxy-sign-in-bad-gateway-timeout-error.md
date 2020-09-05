---
title: App Proxy 앱으로 이 회사 애플리케이션에 액세스할 수 없음 오류
description: Azure AD 애플리케이션 프록시 애플리케이션의 일반적인 액세스 문제를 해결하는 방법입니다.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18c3b0397a0ad3e9e368d1a14e20a999ced5545b
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690275"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>애플리케이션 프록시 애플리케이션을 사용하는 경우 발생하는 "이 회사 애플리케이션에 액세스할 수 없습니다." 오류

이 문서는 Azure AD 애플리케이션 프록시 애플리케이션에서 "이 회사 앱에 액세스할 수 없습니다" 오류에 대한 일반적인 문제를 해결하는 데 도움이 됩니다.

## <a name="overview"></a>개요

이 오류가 표시되면 오류 페이지에서 상태 코드를 찾습니다. 해당 코드는 다음 상태 코드 중 하나일 수 있습니다.

- **게이트웨이 시간 초과**: 애플리케이션 프록시 서비스를 커넥터에 연결할 수 없습니다. 이 오류는 일반적으로 커넥터 할당, 커넥터 자체 또는 커넥터 주위의 네트워킹 규칙에 문제가 있음을 나타냅니다.
- **잘못된 게이트웨이**: 커넥터를 백 엔드 애플리케이션에 연결할 수 없습니다. 이 오류는 애플리케이션이 잘못 구성되었음을 나타낼 수 있습니다.
- **사용 권한 없음**: 사용자는 애플리케이션에 액세스할 수 있는 권한이 없습니다. 이 오류는 사용자가 Azure Active Directory에서 애플리케이션에 할당되지 않거나 백 엔드에서 사용자에게 애플리케이션에 액세스할 수 있는 권한이 없는 경우에 발생할 수 있습니다.

코드를 찾으려면 오류 메시지의 왼쪽 아래에 있는 "상태 코드" 필드의 텍스트를 확인 합니다. 또한 페이지 아래쪽의 추가 팁도 찾아봅니다.

![예제: 게이트웨이 시간 초과 오류](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

이러한 오류의 근본 원인을 해결하는 방법에 대한 세부 정보 및 제안된 해결에 대한 자세한 내용은 아래의 해당 섹션을 참조하세요.

## <a name="gateway-timeout-errors"></a>게이트웨이 시간 초과 오류

서비스가 커넥터에 연결하려고 하지만 제한 시간 내에 연결할 수 없는 경우 게이트웨이 시간 초과가 발생합니다. 이 오류는 일반적으로 커넥터가 작동하지 않는 커넥터 그룹에 애플리케이션이 할당되었거나 커넥터에 필요한 일부 포트가 열리지 않는 경우에 발생합니다.

## <a name="bad-gateway-errors"></a>잘못된 게이트웨이 오류

잘못된 게이트웨이는 커넥터가 백 엔드 애플리케이션에 연결할 수 없음을 나타냅니다. 올바른 애플리케이션을 게시했는지 확인합니다. 이 오류의 원인이 되는 일반적인 실수는 다음과 같습니다.

- 오타 또는 내부 URL의 실수
- 애플리케이션의 루트 게시 안 함 예를 들어 `http://expenses/reimbursement`를 게시했지만 `http://expenses`에 액세스하려고 하는 경우입니다.
- KCD(Kerberos 제한 위임) 구성 문제
- 백 엔드 애플리케이션 문제

## <a name="forbidden-errors"></a>금지된 오류

금지된 오류가 표시되는 경우 사용자가 애플리케이션에 할당되지 않았습니다. 이 오류는 Azure Active Directory 또는 백 엔드 애플리케이션에서 발생할 수 있습니다.

Azure의 애플리케이션에 사용자를 할당하는 방법을 알아보려면 [구성 문서](application-proxy-add-on-premises-application.md#test-the-application)를 참조하세요.

Azure의 애플리케이션에 사용자가 할당되었는지 확인하려면 백 엔드 애플리케이션에서 사용자 구성을 확인합니다. Kerberos 제한 위임/Windows 통합 인증을 사용하는 경우 KCD 문제 해결 페이지에 나와 있는 지침을 참조하세요.

## <a name="check-the-applications-internal-url"></a>애플리케이션의 내부 URL 확인

첫 번째 빠른 단계로, **엔터프라이즈 애플리케이션**을 통해 애플리케이션을 연 다음, **애플리케이션 프록시** 메뉴를 선택하여 내부 URL을 다시 확인하고 수정합니다. 내부 URL이 온-프레미스 네트워크에서 애플리케이션에 액세스하는 데 사용된 URL인지 확인합니다.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>애플리케이션이 작동하는 커넥터 그룹에 할당되었는지 확인합니다.

애플리케이션이 작동하는 커넥터 그룹에 할당되었는지 확인하려면 다음을 수행합니다.

1. **Azure Active Directory**로 이동하고 **엔터프라이즈 애플리케이션**을 클릭한 다음 **애플리케이션 프록시**를 클릭하여 포털에서 애플리케이션을 엽니다. 애플리케이션을 열고 왼쪽 메뉴에서 **애플리케이션 프록시**를 선택합니다.
1. 커넥터 그룹 필드를 확인합니다. 그룹에 활성 커넥터가 없는 경우 경고가 표시됩니다. 경고가 표시 되지 않으면 [필요한 모든 포트가](application-proxy-add-on-premises-application.md) 허용 되는지 확인 하기 위해 이동 합니다.
1. 잘못된 커넥터 그룹이 표시되면 드롭다운을 사용하여 올바른 그룹을 선택하고 경고가 더 이상 표시되지 않는지 확인합니다. 원하는 커넥터 그룹이 표시되면 경고 메시지를 클릭하여 커넥터 관리가 있는 페이지를 엽니다.
1. 여기에서부터는 자세히 다룰 몇 가지 방법이 있습니다.

   - 활성 커넥터를 그룹으로 이동합니다: 그룹에 속해야 하며 대상 백 엔드 애플리케이션에 직접 연결된 활성 커넥터가 있는 경우 지정된 그룹으로 커넥터를 이동할 수 있습니다. 이렇게 하려면 커넥터를 클릭합니다. "커넥터 그룹" 필드에서 드롭다운을 사용 하 여 올바른 그룹을 선택 하 고 저장을 클릭 합니다.
   - 해당 그룹의 새 커넥터 다운로드: 이 페이지에서 [새 커넥터 다운로드](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download)에 연결할 수 있습니다. 백 엔드 애플리케이션에 직접 연결되는 컴퓨터에 커넥터를 설치합니다. 일반적으로 커넥터는 애플리케이션과 동일한 서버에 설치됩니다. 다운로드 커넥터 링크를 사용하여 대상 컴퓨터에 커넥터를 다운로드합니다. 그런 다음 커넥터를 클릭 하 고 "커넥터 그룹" 드롭다운을 사용 하 여 해당 그룹에 속하는지 확인 합니다.
   - 비활성 커넥터 조사: 커넥터가 비활성으로 표시되는 경우 서비스에 연결할 수 없습니다. 이 오류는 일반적으로 필요한 포트 일부가 차단되어 발생합니다. 이 문제를 해결하려면 필요한 모든 포트가 허용되는지 확인으로 이동합니다.

작동하는 커넥터를 사용하여 애플리케이션을 그룹에 할당했는지 확인하기 위해 이러한 단계를 사용한 후에 애플리케이션을 다시 테스트합니다. 여전히 작동하지 않는 경우 다음 섹션으로 계속합니다.

## <a name="check-all-required-ports-are-open"></a>필요한 모든 포트가 열려 있는지 확인

필요한 모든 포트가 열려 있는지 확인 합니다. 필요한 포트에 대해서는 [자습서: 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램을 Azure Active Directory에 추가](application-proxy-add-on-premises-application.md)에서 열기 포트 섹션을 참조 하세요. 모든 필요한 포트가 열려 있으면 다음 섹션으로 이동합니다.

## <a name="check-for-other-connector-errors"></a>다른 커넥터 오류에 대한 확인

이 문제를 해결하는 사항이 하는 경우 다음 단계에서는 커넥터를 사용하여 문제 또는 오류를 확인합니다. [문제 해결 문서](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors)에서 일반적인 오류를 확인할 수 있습니다.

오류를 식별하기 위해 커넥터 로그를 직접 찾아볼 수도 있습니다. 대부분의 오류 메시지는 수정에 대한 특정 권장 사항을 공유합니다. 로그를 보려면 [커넥터 설명서](application-proxy-connectors.md#under-the-hood)를 참조하세요.

## <a name="additional-resolutions"></a>추가 해상도

위의 방법으로 문제가 해결 되지 않으면 몇 가지 원인이 있을 수 있습니다. 이 문제를 식별하려면 다음을 수행합니다.

애플리케이션이 IWA(통합 Windows 인증)를 사용하도록 구성된 경우 Single Sign-On 없이 애플리케이션을 테스트합니다. 그렇지 않으면 다음 단락으로 이동합니다. Single Sign-On 없이 애플리케이션을 확인하려면 **엔터프라이즈 애플리케이션**을 통해 애플리케이션을 열고 **Single Sign-On** 메뉴로 이동합니다. "Windows 통합 인증"에서 "Azure AD Single Sign-On 사용 안 함"으로 드롭다운을 변경 합니다.

브라우저를 열고 애플리케이션에 다시 액세스를 시도합니다. 인증을 위한 메시지가 표시되고 애플리케이션을 가져와야 합니다. 인증할 수 있는 경우 Single Sign-On을 사용할 수 있게 하는 KCD(Kerberos 제한 위임) 구성에 문제가 있습니다. 자세한 내용은 KCD 문제 해결 페이지를 참조하세요.

오류가 계속 표시되면 커넥터를 설치한 머신으로 이동하여 브라우저를 열고 애플리케이션에 사용되는 내부 URL에 대한 연결을 시도합니다. 커넥터는 동일한 컴퓨터에서 다른 클라이언트처럼 작동합니다. 응용 프로그램에 연결할 수 없는 경우 컴퓨터가 응용 프로그램에 연결할 수 없는 이유를 조사 하거나 응용 프로그램에 액세스할 수 있는 서버에서 커넥터를 사용 합니다.

해당 머신의 애플리케이션에 연결하면 커넥터 자체의 문제 또는 오류를 찾습니다. [문제 해결 문서](application-proxy-troubleshoot.md#connector-errors)에서 일반적인 오류를 확인할 수 있습니다. 오류를 식별하기 위해 커넥터 로그를 직접 찾아볼 수도 있습니다. 다양한 오류 메시지는 수정하기 위해 보다 구체적인 권장 사항을 공유합니다. 로그를 보는 방법을 알아보려면 [커넥터 문서](application-proxy-connectors.md#under-the-hood)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure AD 애플리케이션 프록시 커넥터 이해](application-proxy-connectors.md)
