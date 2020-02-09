---
title: 수정 된 기본 규칙-Azure AD Connect를 해결 하는 방법 | Microsoft Docs
description: Azure AD Connect를 사용 하 여 제공 되는 수정 된 기본 규칙을 수정 하는 방법에 알아봅니다.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2f0956b44d6df64fb73e5eee7844574237d8755
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067627"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Azure AD Connect에서 수정 된 기본 규칙 수정

Azure Active Directory (Azure AD) Connect 사용 하 여 기본 동기화 규칙입니다.  그러나 이러한 규칙은 모든 조직에 보편적으로 적용 되지 않습니다. 요구 사항에 따라 수정 하 해야 할 수 있습니다. 이 문서에 가장 일반적인 사용자 지정의 두 가지 예제를 설명 하 고 이러한 사용자 지정을 구현 하는 올바른 방법에 설명 합니다.

>[!NOTE] 
> 필요한 사용자 지정을 위해 기존 기본 규칙을 수정이 지원 되지 않습니다. 이렇게 하면 이러한 규칙을 나중에 최신 버전으로 업데이트 되지 않도록 하는 경우 해제 합니다. 다음 작업을 수행 해야 합니다, 버그 수정 또는 새로운 기능 얻을 수 없습니다. 이 문서에서는 기존 기본 규칙을 수정 하지 않고 동일한 결과 달성 하는 방법을 설명 합니다. 

## <a name="how-to-identify-modified-default-rules"></a>수정 된 기본 규칙을 식별 하는 방법
Azure AD Connect의 1.3.7.0 버전부터 수정 된 기본 규칙을 식별 하 쉽습니다. 로 이동 **바탕 화면에서 앱**, 선택한 **동기화 규칙 편집기**합니다.

![강조 표시 하는 동기화 규칙 편집기를 사용 하 여 azure AD Connect](media/how-to-connect-fix-default-rules/default1.png)

편집기에서 모든 수정 된 기본 규칙 이름 앞에 경고 아이콘이 표시 됩니다.

![경고 아이콘](media/how-to-connect-fix-default-rules/default2.png)

 비활성화 된 규칙을 동일한 이름 옆에 표시 됩니다 (이것이 표준 기본 규칙).

![표준 기본 규칙 및 수정 된 기본 규칙을 보여 주는 동기화 규칙 편집기](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>일반적인 사용자 지정
다음은 일반적인 사용자 지정 기본 규칙입니다.

- 특성 흐름 변경
- 범위 지정 필터를 변경 합니다.
- 조인 조건 변경

모든 규칙 변경 하기 전에:

- 동기화 스케줄러를 사용 하지 않도록 설정 합니다. 스케줄러는 기본적으로 30 분 마다 실행 됩니다. 변경 하 고 새 규칙 문제를 해결 하는 동안 스케줄러가 시작 되지 있는지 확인 합니다. 스케줄러를 일시적으로 사용 하지 않으려면 PowerShell을 시작 하 고 실행 `Set-ADSyncScheduler -SyncCycleEnabled $false`합니다.
 ![동기화 스케줄러를 비활성화 하는 PowerShell 명령](media/how-to-connect-fix-default-rules/default3.png)

- 범위 지정 필터 변경 대상 디렉터리 개체의 삭제 될 수 있습니다. 개체의 범위에서 변경 하기 전에 주의 해야 합니다. 활성 서버에서 변경 하기 전에 변경 내용을 스테이징 서버에 확인 하는 것이 좋습니다.
- 에 설명 된 대로 미리 보기는 단일 개체에 대해 실행 합니다 [동기화 규칙의 유효성을 검사](#validate-sync-rule) 섹션에서는 모든 새 규칙을 추가한 후 합니다.
- 새 규칙을 추가 하거나 사용자 지정 동기화 규칙이 수정 후 전체 동기화를 실행 합니다. 이 동기화는 모든 개체에 새 규칙을 적용합니다.

## <a name="change-attribute-flow"></a>특성 흐름 변경
특성 흐름을 변경 하기 위한 세 가지 시나리오는
- 새 특성을 추가합니다.
- 기존 특성의 값을 재정의합니다.
- 기존 특성을 동기화 하지 않도록 선택 합니다.

표준 기본 규칙을 변경 하지 않고이 수행할 수 있습니다.

### <a name="add-a-new-attribute"></a>새 특성을 추가 합니다.
특성 흐르지 않는 소스 디렉터리에서 대상 디렉터리를 찾을 경우 사용 된 [Azure AD Connect 동기화: 디렉터리 확장](how-to-connect-sync-feature-directory-extensions.md) 이 문제를 해결 합니다.

확장 하기 작동 하지 않으면 다음 섹션에서 설명 하는 두 개의 새 동기화 규칙을 추가 하십시오.


#### <a name="add-an-inbound-sync-rule"></a>인바운드 동기화 규칙을 추가 합니다.
인바운드 동기화 규칙을 특성에 대 한 원본이 커넥터 공간의 메타 버스 대상이 의미 합니다. 예를 들어, 흐름에서 새 특성이 온-프레미스 Active Directory를 Azure Active Directory, 새 인바운드 동기화 규칙을 만듭니다. 시작 합니다 **동기화 규칙 편집기**를 선택 **인바운드** 방향 및 선택 **새 규칙 추가**합니다. 

 ! 동기화 규칙 Editor](media/how-to-connect-fix-default-rules/default3a.png)

규칙 이름을 고유한 명명 규칙을 따릅니다. 여기에서 사용 하 여 **사용자 지정 In from AD-사용자**합니다. 이 규칙은 사용자 지정 규칙을은 메타 버스에 Active Directory 커넥터 공간에서 인바운드 규칙을 의미 합니다.   

 ![인바운드 동기화 규칙 만들기](media/how-to-connect-fix-default-rules/default3b.png)

규칙의 향후 유지 관리는 쉽게 해당 규칙의 고유한 설명을 제공 합니다. 예를 들어 설명은 규칙의 목적은 무엇 인지 왜 필요에 따라 수 있습니다.

에 대 한 내용을 확인 합니다 **연결 된 시스템**를 **연결 된 시스템 개체 유형**, 및 **메타 버스 개체 형식** 필드입니다.

0에서 99 사이의 우선 순위 값 지정 (낮은 수, 높을수록 우선 순위). 에 대 한 합니다 **태그**를 **암호 동기화를 사용 하도록 설정**, 및 **사용 안 함** 필드 기본값을 사용 합니다.

유지할 **범위 지정 필터** 비어 있습니다. 이 즉, 규칙이 모든 개체에 적용 되는 Active Directory 연결 된 시스템 및 메타 버스 간에 조인 합니다.

유지할 **조인 규칙** 비어 있습니다. 즉,이 규칙은 표준 기본 규칙에 정의 된 조인 조건입니다. 사용 하지 않도록 설정 하거나 표준 기본 규칙을 삭제 하지 다른 이유입니다. 조인 조건이 없는 경우 특성 흐르지 않습니다. 

특성에 대 한 적절 한 변환을 추가 합니다. 상수를 확인 하는 상수를 할당할 수 있습니다 흐름을 대상 특성 값입니다. 원본 또는 대상 특성 간의 직접 매핑을 사용할 수 있습니다. 또는 특성에 대 한 식을 사용할 수 있습니다. 다양 한 같습니다 [식 함수](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference) 사용할 수 있습니다.

#### <a name="add-an-outbound-sync-rule"></a>아웃 바운드 동기화 규칙을 추가 합니다.
특성 대상 디렉터리에 연결할 아웃 바운드 규칙을 만드는 해야 합니다. 즉, 원본이 메타 버스, 대상에 연결 된 시스템입니다. 아웃 바운드 규칙을 만들려면 시작를 **동기화 규칙 편집기**를 변경 합니다 **방향** 에 **아웃 바운드**, 선택한 **새 규칙 추가**. 

![동기화 규칙 편집기](media/how-to-connect-fix-default-rules/default3c.png)

인바운드 규칙을 규칙 이름을 고유한 명명 규칙을 사용할 수 있습니다. 선택 된 **연결 된 시스템** 를 원하는 개체를 Azure AD 테넌트와 연결된 된 시스템 선택 특성 값을 설정 합니다. 0에서 99 사이의 우선 순위를 설정 합니다. 

![아웃바운드 동기화 규칙 만들기](media/how-to-connect-fix-default-rules/default3d.png)

유지할 **범위 지정 필터** 하 고 **조인 규칙** 비어 있습니다. 상수, 직접, 또는 식으로 변환에 입력 합니다. 

이제 Azure Active Directory에 Active Directory에서 사용자 개체 흐름에 새 특성을 확인 하는 방법을 알고 있습니다. 원본 및 대상 개체에서 모든 특성을 매핑할 다음이 단계를 사용할 수 있습니다. 자세한 내용은 [사용자 지정 동기화 규칙 만들기](how-to-connect-create-custom-sync-rule.md) 하 고 [사용자를 프로 비전 하기 위한 준비](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization)합니다.

### <a name="override-the-value-of-an-existing-attribute"></a>기존 특성 값을 재정의
이미 매핑되는 특성 값을 재정의 하려는 경우. 예를 들어, 항상 Azure AD의 특성에 null 값을 설정 하려는 경우 인바운드 규칙만 단순히 만듭니다. 상수 값을 확인 `AuthoritativeNull`, 대상 특성에 대 한 흐름입니다. 

>[!NOTE] 
> 사용 하 여 `AuthoritativeNull` 대신 `Null` 이 경우. 더 낮은 우선 순위 (규칙에 더 높은 숫자 값)는 경우에 null이 아닌 값을 null 값을 대체 때문입니다. `AuthoritativeNull`다른 한편으로 대체 되지 않습니다는 null이 아닌 값을 사용 하 여 다른 규칙이 있습니다. 

### <a name="dont-sync-existing-attribute"></a>기존 특성을 동기화 하지 않음
동기화 할 특성을 제외 하려는 경우 Azure AD Connect에서 제공 하는 기능을 필터링 하는 특성을 사용 합니다. 시작할 **Azure AD Connect** 바탕 화면 아이콘 및 선택 **동기화 옵션 사용자 지정**합니다.

![Azure AD Connect 추가 작업 옵션](media/how-to-connect-fix-default-rules/default4.png)

 했는지 **Azure AD 앱 및 특성 필터링** 선택한 선택 **다음**합니다.

![Azure AD Connect의 선택적 기능](media/how-to-connect-fix-default-rules/default5.png)

동기화에서 제외 하려는 특성의 선택을 취소 합니다.

![Azure AD Connect 특성](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>범위 지정 필터를 변경 합니다.
개체의 대부분의 azure AD Sync를 담당합니다. 개체의 범위를 줄일 수 있으며 표준 기본 동기화 규칙을 변경 하지 않고 내보낼 개체의 수를 줄일 수 있습니다. 

동기화 하는 개체의 범위를 줄이기 위해 다음 방법 중 하나를 사용 합니다.

- cloudFiltered 특성
- 필터링 하는 조직 구성 단위

동기화 중인 사용자의 범위를 줄이면 암호 해시 동기화 필터링 스케일 아웃 사용자도 중지 됩니다. 개체는 이미 동기화 범위를 줄입니다 후, 필터링 된 스케일 아웃 개체는 대상 디렉터리에서 삭제 됩니다. 이 따라서 매우 신중 하 게 범위를 확인 합니다.

>[!IMPORTANT] 
> Azure AD Connect에서 구성 하는 개체의 범위를 늘려 권장 되지 않습니다. 이렇게 하면 사용자 지정 항목을 이해 하려면 Microsoft 지원 팀에 대 한 어려워집니다. 개체의 범위를 늘려야 하는 경우 기존 규칙을 편집, 복제 한 다음, 및 원래 규칙을 사용 하지 않도록 설정 합니다. 

### <a name="cloudfiltered-attribute"></a>cloudFiltered 특성
Active Directory에서이 특성을 설정할 수 없습니다. 새 인바운드 규칙을 추가 하 여이 특성의 값을 설정 합니다. 사용할 수 있습니다 **변환** 및 **식** 메타 버스에이 특성을 설정 합니다. 다음 예제에서는 부서 이름이 시작 하는 모든 사용자를 동기화 않으려는 **HRD** (대/소문자 구분):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

원본 (Active Directory)에서 부서 소문자로 변환 먼저입니다. 다음을 사용 하는 `Left` 함수에서는 처음 세 문자만 사용 하 여 비교 `hrd`합니다. 와 일치 하는 값으로 설정 됩니다 `True`고, 그렇지 않으면 `NULL`합니다. 값을 null로 설정에서 더 낮은 우선 순위 (더 높은 숫자 값)를 사용 하 여 일부 다른 규칙 쓸 수 있습니다 다른 조건을 사용 하 여 합니다. 에 설명 된 대로 동기화 규칙의 유효성을 검사 한 개체에 preview를 실행된 합니다 [동기화 규칙 유효성 검사](#validate-sync-rule) 섹션입니다.

![인바운드 동기화 규칙 옵션 만들기](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>필터링 하는 조직 구성 단위
하나 이상의 조직 구성 단위 (Ou)를 만들고 이러한 Ou를 동기화 하지 않을 개체를 이동할 수 있습니다. 그런 다음 Azure AD Connect에서 필터링 하는 OU를 구성 합니다. 시작할 **Azure AD Connect** 바탕 화면 아이콘에서 다음 옵션을 선택 합니다. 또한 Azure AD Connect 설치 시 필터링 OU를 구성할 수 있습니다. 

![Azure AD Connect 추가 작업](media/how-to-connect-fix-default-rules/default8.png)

마법사를 따르고 동기화 하지 않을 Ou의 선택을 취소 합니다.

![Azure AD Connect 도메인 및 OU 필터링 옵션](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>조인 조건 변경
Azure AD Connect에서 구성 된 기본 조인 조건을 사용 합니다. 기본 조인 조건이 변경 되 면 사용자 지정 항목을 이해 하 고 제품을 지원 하려면 Microsoft 지원에 대 한 어려워집니다.

## <a name="validate-sync-rule"></a>동기화 규칙 유효성 검사
전체 동기화 주기를 실행 하지 않고 미리 보기 기능을 사용 하 여 새로 추가 된 동기화 규칙을 확인할 수 있습니다. Azure AD Connect에서 선택 **동기화 서비스**합니다.

![강조 표시 하는 동기화 서비스를 사용 하 여 azure AD Connect](media/how-to-connect-fix-default-rules/default10.png)

선택 **메타 버스 검색**합니다. 범위 개체를 선택 **person**를 선택 **절 추가**, 검색 조건에 언급 한 합니다. 다음으로, 선택 **검색**, 검색 결과에 개체를 두 번 클릭 합니다. 이 단계를 실행 하기 전에 포리스트 가져오기 및 동기화를 실행 하 여 Azure AD Connect에서 데이터 개체에 대 한 최신 상태 인지 확인 합니다.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

온 **메타 버스 개체 속성**를 선택 **커넥터**, 해당 커넥터 (포리스트)의 개체를 선택 하 고 선택 **속성...** .

![메타버스 개체 속성](media/how-to-connect-fix-default-rules/default12.png)

선택 **미리 보기...**

![커넥터 공간 개체 속성](media/how-to-connect-fix-default-rules/default13a.png)

미리 보기 창에서 선택 **미리 보기 생성** 하 고 **가져오기 특성 흐름** 왼쪽된 창에서.

![미리 보기](media/how-to-connect-fix-default-rules/default14.png)
 
여기에 새로 추가 된 규칙 개체에 대해 실행 되 고이이 설정 하는 확인 된 `cloudFiltered` 특성을 true로 합니다.

![미리 보기](media/how-to-connect-fix-default-rules/default15a.png)
 
기본 규칙을 사용 하 여 수정 된 규칙을 비교 하려면 내보내기 규칙 모두 개별적으로 텍스트 파일로. 이러한 규칙을 PowerShell 스크립트 파일로 내보내집니다. 변경 내용을 보려면 파일 비교 도구 (예를 들어 windiff)를 사용 하 여 비교할 수 있습니다. 
 
수정 된 규칙에서의 `msExchMailboxGuid` 특성으로 변경 됩니다 합니다 **식** 형식 대신 **직접**입니다. 에 값이 변경 하는 또한 **NULL** 하 고 **ExecuteOnce** 옵션입니다. 식별 된 및 우선 순위 차이 무시할 수 있습니다. 

![windiff 도구 출력](media/how-to-connect-fix-default-rules/default17.png)
 
기본 설정을 변경 하려면 규칙을 해결 하려면 수정 된 규칙을 삭제 하 고 기본 규칙을 사용 합니다. 달성 하고자 하는 사용자 지정을 손실 되지 않도록 확인 합니다. 준비 된 경우 실행할 **전체 동기화**합니다.

## <a name="next-steps"></a>다음 단계
- [하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md) 
- [Express 설정](how-to-connect-install-express.md)
- [사용자 지정된 설정](how-to-connect-install-custom.md)



