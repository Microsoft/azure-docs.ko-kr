---
title: Azure Active Directory 포털에서 사용자 대량 만들기 | Microsoft Docs
description: Azure Active Directory의 Azure AD 관리 센터에서 사용자 대량 추가
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/11/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64fc4e1192a1ec33b1d13c38d66e9aab5cbe256b
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705625"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Azure Active Directory에서 사용자 대량 만들기

Azure AD(Azure Active Directory)는 사용자 대량 만들기 및 삭제 작업을 지원하고 사용자 목록 다운로드를 지원합니다. Azure AD 포털에서 다운로드할 수 있는 쉼표로 구분된 값(CSV) 템플릿만 입력하면 됩니다.

## <a name="required-permissions"></a>필요한 사용 권한

관리 포털에서 사용자를 대량으로 만들려면 전역 관리자 또는 사용자 관리자 권한으로 로그인해야 합니다.

## <a name="understand-the-csv-template"></a>CSV 템플릿 이해

대량 업로드 CSV 템플릿을 다운로드하고 입력하여 대량으로 Azure AD 사용자를 성공적으로 만들 수 있습니다. 다운로드한 CSV 템플릿은 다음 예제와 같이 표시될 수 있습니다.

![업로드용 스프레드시트 및 각 행과 열에 대한 용도 및 값에 대한 설명](./media/users-bulk-add/create-template-example.png)

> [!WARNING]
> CSV 템플릿을 사용 하 여 하나의 항목만 추가 하는 경우 행 3을 유지 하 고 행 4에 새 항목을 추가 해야 합니다.

### <a name="csv-template-structure"></a>CSV 템플릿 구조

다운로드된 CSV 템플릿의 행은 다음과 같습니다.

- **버전 번호**: 버전 번호가 포함된 첫 번째 행은 CSV 업로드에 포함되어야 합니다.
- **열 머리글**: 열 머리글의 형식은 &lt;*항목 이름*&gt; [PropertyName] &lt;*필수 또는 비워 둠*&gt;입니다. `Name [displayName] Required`)을 입력합니다. 템플릿의 일부 이전 버전은 약간 다를 수 있습니다.
- **예제 행**: 템플릿에는 각 열에 대해 허용 가능한 값의 예제 행이 포함되어 있습니다. 예제 행을 제거하고 원하는 항목으로 바꾸어야 합니다.

### <a name="additional-guidance"></a>추가 지침

- 업로드 템플릿의 처음 두 행은 제거하거나 수정하면 안 됩니다. 이렇게 하면 업로드를 처리할 수 없습니다.
- 필수 열이 먼저 나열됩니다.
- 템플릿에 새 열을 추가하지 않는 것이 좋습니다. 새로 추가하는 열은 무시되고 처리되지 않습니다.
- 가능한 자주 최신 버전의 CSV 템플릿을 다운로드하는 것이 좋습니다.
- 필드 앞/뒤에 의도 하지 않은 공백이 없는지 확인 해야 합니다. **사용자 계정 이름의**경우 이러한 공백이 있으면 가져오기 오류가 발생 합니다.

## <a name="to-create-users-in-bulk"></a>사용자를 대량으로 만들려면

1. 조직에서 사용자 관리자인 계정으로 [Azure AD 조직에 로그인](https://aad.portal.azure.com)합니다.
1. Azure AD에서 **사용자** > **대량 만들기**를 선택합니다.
1. **사용자 대량 만들기** 페이지에서 **다운로드**를 선택하여 유효한 사용자 속성의 CSV(쉼표로 구분된 값) 파일을 받은 다음, 만들려는 사용자를 추가합니다.

   ![추가하려는 사용자를 나열하는 로컬 CSV 파일을 선택합니다.](./media/users-bulk-add/upload-button.png)

1. CSV 파일을 열고 만들려는 각 사용자에 대한 줄을 추가합니다. 유일한 필수 값은 **이름**, **사용자 계정 이름**, **초기 암호** 및 **로그인 차단(예/아니요)** 입니다. 그런 다음 파일을 저장합니다.

   [![CSV 파일에는 만들 사용자의 이름 및 ID가 포함되어 있습니다.](media/users-bulk-add/add-csv-file.png)](media/users-bulk-add/add-csv-file.png#lightbox)

1. **사용자 일괄 만들기** 페이지의 CSV 파일 업로드에서 해당 파일을 찾습니다. 파일을 선택하고 **제출**을 클릭하면 CSV 파일의 유효성 검사가 시작됩니다.
1. 파일 콘텐츠의 유효성 검사가 완료되면 **파일 업로드 성공**이 표시됩니다. 오류가 있는 경우 해당 오류를 해결해야 작업을 제출할 수 있습니다.
1. 파일이 유효성 검사를 통과하면 **제출**을 선택하여 새 사용자를 가져오는 Azure 대량 작업을 시작합니다.
1. 가져오기 작업이 완료되면 대량 작업 상태 알림이 표시됩니다.

오류가 있는 경우 **대량 작업 결과** 페이지에서 결과 파일을 다운로드하고 볼 수 있습니다. 파일에는 각 오류의 이유가 포함되어 있습니다. 파일 제출은 제공된 템플릿과 일치하고 정확한 열 이름을 포함해야 합니다.

## <a name="check-status"></a>상태 확인

**대량 작업 결과** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

   [![대량 작업 결과 페이지에서 만들기 상태 확인](media/users-bulk-add/bulk-center.png)](media/users-bulk-add/bulk-center.png#lightbox)

다음으로, Azure Portal 또는 PowerShell을 사용하여 Azure AD 조직에 만든 사용자가 있는지 확인할 수 있습니다.

## <a name="verify-users-in-the-azure-portal"></a>Azure Portal에서 사용자 확인

1. 조직에서 사용자 관리자인 계정으로 [Azure AD 관리 센터에 로그인](https://aad.portal.azure.com)합니다.
1. 탐색 창에서 **Azure Active Directory**를 선택합니다.
1. **관리**에서 **사용자**를 선택합니다.
1. **표시**에서 **모든 사용자**를 선택하여 만든 사용자가 나열되는지 확인합니다.

### <a name="verify-users-with-powershell"></a>PowerShell을 사용하여 사용자 확인

다음 명령 실행:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

만든 사용자가 나열되는지 확인합니다.

## <a name="bulk-import-service-limits"></a>대량 가져오기 서비스 제한

사용자를 만드는 각 대량 활동은 최대 1시간 동안 실행될 수 있습니다. 이와 같이 5만 명 이상의 사용자를 대량으로 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [사용자 대량 삭제](users-bulk-delete.md)
- [사용자 목록 다운로드](users-bulk-download.md)
- [사용자 대량 복원](users-bulk-restore.md)
