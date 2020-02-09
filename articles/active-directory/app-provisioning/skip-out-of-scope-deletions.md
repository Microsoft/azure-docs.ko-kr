---
title: 범위를 벗어난 사용자의 삭제 생략 | Microsoft Docs
description: 범위 사용자에서 프로 비전 해제의 기본 동작을 재정의 하는 방법에 대해 알아봅니다.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b1261e641afe1be1b298b4b19198782bf57eaa1
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066162"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>범위를 벗어나는 사용자 계정 삭제 건너뛰기

기본적으로 Azure AD 프로 비전 엔진은 범위를 벗어난 사용자를 일시적으로 삭제 하거나 사용 하지 않도록 설정 합니다. 그러나 Workday에서 AD 사용자 인바운드 프로 비전과 같은 특정 시나리오의 경우이 동작이 예상과 다를 수 있으며이 기본 동작을 재정의할 수 있습니다.  

이 가이드에서는 Microsoft Graph API 및 Microsoft Graph API 탐색기를 사용 하 여 범위를 벗어나는 계정 처리를 제어 하는 플래그 ***SkipOutOfScopeDeletions*** 설정 하는 방법을 설명 합니다. 
* ***SkipOutOfScopeDeletions*** 가 0 (false)으로 설정 된 경우 범위를 벗어나는 계정은 대상에서 사용 하지 않도록 설정 됩니다.
* ***SkipOutOfScopeDeletions*** 가 1 (true)로 설정 된 경우 범위를 벗어난 계정은 대상에서 사용 하지 않도록 설정 됩니다 .이 플래그는 *프로 비전 앱* 수준에서 설정 되며 Graph API를 사용 하 여 구성할 수 있습니다. 

이 구성은 *workday에서 사용자 프로 비전* 앱을 Active Directory 하는 데 널리 사용 되므로 다음 단계에서는 workday 응용 프로그램의 스크린샷를 포함 합니다. 그러나이는 **다른 모든 앱** (ServiceNow, Salesforce, Dropbox 등) 에서도 사용할 수 있습니다.

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1 단계: 프로 비전 App Service 보안 주체 ID (개체 ID)를 검색 합니다.

1. [Azure Portal](https://portal.azure.com)를 시작 하 고 프로 비전 응용 프로그램의 속성 섹션으로 이동 합니다. 예를 들어 *Workday를 AD 사용자 프로 비전 응용 프로그램* 매핑으로 내보내려는 경우 해당 앱의 속성 섹션으로 이동 합니다. 
1. 프로비전 앱의 속성 섹션에서 ‘개체 ID’ 필드와 연결된 GUID 값을 복사합니다. 이 값은 앱의 **ServicePrincipalId**라고도 하고 Graph Explorer 작업에서 사용됩니다.

   ![Workday 앱 서비스 주체 ID](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>2 단계: Microsoft Graph 탐색기에 로그인

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 시작
1. “Microsoft에 로그인” 단추를 클릭하고 Azure AD 전역 관리자 또는 앱 관리자 자격 증명을 사용하여 로그인합니다.

    ![Graph 로그인](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. 로그인에 성공하면 왼쪽 창에 사용자 계정 세부 정보가 표시됩니다.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>3 단계: 기존 앱 자격 증명 및 연결 정보 가져오기

Microsoft Graph Explorer에서 [servicePrincipalId]를 **1단계**에서 추출된 [ServicePrincipalId](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)로 바꾸고 다음 GET 쿼리를 실행합니다.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![작업 쿼리 가져오기](./media/skip-out-of-scope-deletions/skip-03.png)

응답을 텍스트 파일로 복사 합니다. 이 값은 아래에 표시 된 JSON 텍스트와 같이 표시 됩니다. 값은 배포와 관련 하 여 노란색으로 강조 표시 됩니다. 녹색으로 강조 표시 된 줄을 끝에 추가 하 고 blue로 강조 표시 된 Workday 연결 암호를 업데이트 합니다. 

   ![작업 응답 가져오기](./media/skip-out-of-scope-deletions/skip-04.png)

매핑에 추가할 JSON 블록은 다음과 같습니다. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>4 단계: SkipOutOfScopeDeletions 플래그를 사용 하 여 비밀 엔드포인트 업데이트

그래프 탐색기에서 아래 명령을 실행 하 여 비밀 엔드포인트을 ***SkipOutOfScopeDeletions*** 플래그로 업데이트 합니다. 

아래 URL에서 [servicePrincipalId]을 [1 단계](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)에서 추출한 **servicePrincipalId** 로 바꿉니다. 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
3 단계에서 "요청 본문"에 업데이트 된 텍스트를 복사 하 고 "요청 헤더"에서 "Content-type" 헤더를 "application/json"으로 설정 합니다. 

   ![PUT 요청](./media/skip-out-of-scope-deletions/skip-05.png)

"쿼리 실행"을 클릭 합니다. 

출력을 "성공 – 상태 코드 204"으로 가져와야 합니다. 

   ![응답 배치](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>5 단계: 범위를 벗어나는 사용자가 사용 하지 않도록 설정 되었는지 확인

특정 사용자를 건너뛰도록 범위 지정 규칙을 업데이트 하 여 예상 된 동작으로이 플래그를 테스트할 수 있습니다. 아래 예제에서는 새 범위 지정 규칙을 추가 하 여 ID가 21173 (이전에는) 인 직원을 제외 합니다. 

   ![범위 지정 예제](./media/skip-out-of-scope-deletions/skip-07.png)

다음 프로 비전 주기에서 Azure AD 프로 비전 서비스는 사용자 21173이 범위를 벗어난 것을 식별 하 고 SkipOutOfScopeDeletions 속성을 사용 하도록 설정한 경우 해당 사용자에 대 한 동기화 규칙은 아래와 같이 메시지를 표시 합니다. 

   ![범위 지정 예제](./media/skip-out-of-scope-deletions/skip-08.png)


