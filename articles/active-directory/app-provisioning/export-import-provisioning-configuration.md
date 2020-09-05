---
title: 프로 비전 구성을 내보내고 재해 복구를 위해 알려진 양호한 상태로 롤백합니다.
description: 프로 비전 구성을 내보내고 재해 복구를 위해 알려진 양호한 상태로 롤백하는 방법에 대해 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/19/2020
ms.author: kenwith
ms.openlocfilehash: e34656d6ce515cabe955c101f7b52ac0f2ade8db
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235820"
---
# <a name="how-to-export-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>방법: 프로 비전 구성 내보내기 및 알려진 정상 상태로 롤백

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- Azure Portal에서 프로 비전 구성 내보내기 및 가져오기
- Microsoft Graph API를 사용 하 여 프로 비전 구성 내보내기 및 가져오기

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Azure Portal에서 프로 비전 구성 내보내기 및 가져오기

### <a name="export-your-provisioning-configuration"></a>프로 비전 구성 내보내기

구성을 내보내려면:

1. [Azure Portal](https://portal.azure.com/)의 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택합니다.
1. **Azure Active Directory** 창에서 **엔터프라이즈 응용 프로그램** 을 선택 하 고 응용 프로그램을 선택 합니다.
1. 왼쪽 탐색 창에서 **프로 비전**을 선택 합니다. 프로 비전 구성 페이지에서 **특성 매핑**을 클릭 한 다음 **고급 옵션을 표시**하 고 마지막으로 **스키마를 검토**합니다. 그러면 스키마 편집기로 이동 합니다.
1. 페이지 맨 위에 있는 명령 모음에서 다운로드를 클릭 하 여 스키마를 다운로드 합니다.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>재해 복구-알려진 정상 상태로 롤백

구성을 내보내고 저장 하면 이전 버전의 구성으로 롤백할 수 있습니다. 특성 매핑 또는 범위 지정 필터를 변경 하는 경우 언제 든 지 프로 비전 구성을 내보내고 나중에 사용 하기 위해 저장 하는 것이 좋습니다. 위의 단계에서 다운로드 한 JSON 파일을 열고 JSON 파일의 전체 내용을 복사 하 고 스키마 편집기에서 JSON 페이로드의 전체 내용을 바꾸고 저장 하기만 하면 됩니다. 활성 프로 비전 주기가 있으면 완료 되 고 다음 주기에 업데이트 된 스키마가 사용 됩니다. 또한 다음 주기는 새로운 구성에 따라 모든 사용자 및 그룹을 다시 평가 하는 초기 주기로 사용 됩니다. 이전 구성으로 롤백할 때 다음 사항을 고려 하십시오.

- 사용자를 다시 평가 하 여 범위에 속해야 하는지 확인 합니다. 범위 지정 필터가 변경 된 경우 사용자가 범위를 초과 하지 않으면 사용 하지 않도록 설정 됩니다. 대부분의 경우이 동작은 원하는 동작 이지만,이를 방지 하 고 [범위 삭제 생략](./skip-out-of-scope-deletions.md) 기능을 사용할 수 있는 경우가 있습니다. 
- 프로 비전 구성을 변경 하면 서비스가 다시 시작 되 고 [초기 순환이](./how-provisioning-works.md#provisioning-cycles-initial-and-incremental)트리거됩니다.

## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Microsoft Graph API를 사용 하 여 프로 비전 구성 내보내기 및 가져오기

Microsoft Graph API 및 Microsoft Graph 탐색기를 사용 하 여 사용자 프로 비전 특성 매핑 및 스키마를 JSON 파일로 내보내고 Azure AD로 다시 가져올 수 있습니다. 여기에 캡처된 단계를 사용 하 여 프로 비전 구성의 백업을 만들 수도 있습니다.

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1 단계: 프로 비전 App Service 보안 주체 ID (개체 ID)를 검색 합니다.

1. [Azure Portal](https://portal.azure.com)를 시작 하 고 프로 비전 응용 프로그램의 속성 섹션으로 이동 합니다. 예를 들어 *Workday를 AD 사용자 프로 비전 응용 프로그램* 매핑으로 내보내려는 경우 해당 앱의 속성 섹션으로 이동 합니다.
1. 프로비전 앱의 속성 섹션에서 ‘개체 ID’ 필드와 연결된 GUID 값을 복사합니다.** 이 값은 앱의 **ServicePrincipalId** 라고도 하며 Microsoft Graph 탐색기 작업에 사용 됩니다.

   ![Workday 앱 서비스 주체 ID](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>2 단계: Microsoft Graph 탐색기에 로그인

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 시작
1. “Microsoft에 로그인” 단추를 클릭하고 Azure AD 전역 관리자 또는 앱 관리자 자격 증명을 사용하여 로그인합니다.

    ![로그인 Microsoft Graph](./media/export-import-provisioning-configuration/wd_export_02.png)

1. 로그인에 성공하면 왼쪽 창에 사용자 계정 세부 정보가 표시됩니다.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>3 단계: 프로 비전 앱의 프로 비전 작업 ID 검색

Microsoft Graph Explorer에서 [servicePrincipalId]를 [1단계](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)에서 추출된 **ServicePrincipalId**로 바꾸고 다음 GET 쿼리를 실행합니다.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

다음과 같은 응답이 제공됩니다. 응답에 있는 “id 특성”을 복사합니다. 이 값은 **ProvisioningJobId**이고 기본 스키마 메타데이터를 검색하는 데 사용됩니다.

   [![프로 비전 작업 ID](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>4 단계: 프로 비전 스키마 다운로드

Microsoft Graph Explorer에서 [servicePrincipalId] 및 [ProvisioningJobId]를 이전 단계에서 검색된 ServicePrincipalId 및 ProvisioningJobId로 바꾸고 다음 GET 쿼리를 실행합니다.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

응답에서 JSON 개체를 복사하고 파일에 저장하여 스키마 백업을 만듭니다.

### <a name="step-5-import-the-provisioning-schema"></a>5 단계: 프로 비전 스키마 가져오기

> [!CAUTION]
> Azure Portal을 사용하여 변경할 수 없는 구성의 스키마를 수정해야 하거나 이전에 백업된 파일의 구성을 작동하는 유효한 스키마로 복원해야 하는 경우에만 이 단계를 수행합니다.

Microsoft Graph Explorer에서 다음 PUT 쿼리를 구성하고 [servicePrincipalId] 및 [ProvisioningJobId]를 이전 단계에서 검색된 ServicePrincipalId 및 ProvisioningJobId로 바꿉니다.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

“요청 본문” 탭에서 JSON 스키마 파일의 콘텐츠를 복사합니다.

   [![요청 본문](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

“요청 헤더” 탭에서 “application/json” 값과 함께 Content-Type 헤더 특성을 추가합니다.

   [![요청 헤더](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

**쿼리 실행** 을 선택 하 여 새 스키마를 가져옵니다.