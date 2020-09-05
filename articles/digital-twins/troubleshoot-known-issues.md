---
title: 알려진 문제-Azure Digital Twins
description: Azure Digital Twins의 알려진 문제를 인식 하 고 완화 하는 데 도움을 받으세요.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 01d962db45a58781ca5f2ba494de16ad420b0807
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88921072"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure Digital Twins의 알려진 문제

이 문서에서는 Azure Digital Twins와 관련 된 알려진 문제에 대 한 정보를 제공 합니다.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>Cloud Shell의 "400 클라이언트 오류: 잘못 된 요청"

"400 클라이언트 오류: url에 대 한 잘못 된 요청: http://localhost:50342/oauth2/token " 및 전체 스택 추적이 발생 하 여 Cloud Shell의 명령이 간헐적으로 실패할 수 있습니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

명령을 다시 실행 `az login` 하 고 후속 로그인 단계를 완료 하 여이를 해결할 수 있습니다.

그런 다음 명령을 다시 실행할 수 있습니다.

### <a name="possible-causes"></a>가능한 원인

이는 Cloud Shell의 알려진 문제에 대 한 결과입니다. [*Cloud Shell에서 토큰을 일시적으로 가져오지 못했습니다. 400 클라이언트 오류: 잘못 된 요청*](https://github.com/Azure/azure-cli/issues/11749)입니다.

## <a name="missing-role-assignment-after-scripted-setup"></a>스크립팅된 설치 후 역할 할당이 누락 되었습니다.

일부 사용자에 게 [*는 방법: 인스턴스 및 인증 (스크립팅된) 설정*](how-to-set-up-instance-scripted.md)의 역할 할당 부분과 관련 된 문제가 발생할 수 있습니다. 이 스크립트는 실패를 나타내지는 않지만 *Azure Digital Twins 소유자 (미리 보기)* 역할은 사용자에 게 할당 되지 않으며,이로 인해 다른 리소스를 다른 리소스를 만드는 기능에 영향을 줍니다.

스크립트를 실행 한 후 역할 할당이 성공적으로 설정 되었는지 확인 하려면 설치 문서의 [*사용자 역할 할당 확인*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) 섹션에 있는 지침을 따르세요. 사용자가이 역할에 표시 되지 않는 경우이 문제가 영향을 받습니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

해결 하려면 CLI 또는 Azure Portal를 사용 하 여 역할 할당을 수동으로 설정할 수 있습니다. 

다음 지침을 따릅니다.
* [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>가능한 원인

사용자가 [MSA (개인 Microsoft 계정)](https://account.microsoft.com/account)를 사용 하 여 로그인 한 사용자의 경우 사용자의 로그인 전자 메일과 다를 수 있습니다 .이 처럼 사용자의 명령에서 사용자의 사용자 ID는 사용자의 로그인 메일과 다를 수 있으므로 스크립트를 검색 하 여 역할을 적절 하 게 할당 하는 것이 어려워집니다.

## <a name="issue-with-interactive-browser-authentication"></a>대화형 브라우저 인증과 관련 된 문제

Azure **1.2.0** ** [Azure.Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) 라이브러리**의 버전을 사용 하 여 azure Digital twins 응용 프로그램에서 인증 코드를 작성할 때 [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) 메서드와 관련 된 문제가 발생할 수 있습니다.

이는 라이브러리의 최신 버전이 아닙니다. 최신 버전은 **1.2.2**입니다.

영향을 받는 메서드는 다음 문서에서 사용 됩니다. 
* [*자습서: 클라이언트 앱 코딩*](tutorial-code.md)
* [*방법: 앱 인증 코드 작성*](how-to-authenticate-client.md)
* [*방법: Azure Digital Twins Api 및 Sdk 사용*](how-to-use-apis-sdks.md)

이 문제에는 브라우저 창에서 인증을 시도할 때 "AuthenticationFailedException"의 오류 응답이 포함 됩니다. 브라우저 창이 완전히 시작 되지 않거나 사용자를 성공적으로 인증 하는 것 처럼 보이지만 클라이언트 응용 프로그램은 오류와 함께 실패 합니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

이 문제를 해결 하려면 Azure. Identity 버전 **1.2.2**를 사용 하도록 응용 프로그램을 업데이트 합니다. 이 라이브러리 버전을 사용 하는 경우 브라우저에서 예상 대로 로드 하 고 인증 해야 합니다.

### <a name="possible-causes"></a>가능한 원인

이는 최신 버전의 Azure. Identity library (버전 **1.2.0**)와 관련 된 오픈 문제와 관련 된 것입니다. [*InteractiveBrowserCredential를 사용할 때 인증에 실패*](https://github.com/Azure/azure-sdk-for-net/issues/13940)합니다.

Azure Digital Twins 응용 프로그램에서 버전 **1.2.0** 를 사용 하거나 버전을 지정 하지 않고 프로젝트에 라이브러리를 추가 하는 경우이 문제가 표시 됩니다 (이 최신 버전으로도 기본값).

## <a name="next-steps"></a>다음 단계

Azure Digital Twins의 보안 및 사용 권한에 대해 자세히 알아보세요.
* [*개념: Azure Digital Twins 솔루션에 대 한 보안*](concepts-security.md)