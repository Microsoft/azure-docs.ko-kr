---
title: 'Azure AD Connect: 개체 동기화 문제 해결 | Microsoft Docs'
description: 이 항목에서는 문제 해결 작업을 사용하여 개체 동기화 관련 문제를 해결하는 방법에 대한 단계를 제공합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e56d4d94e38e5095ef2223d0cc2875cbf1dcd46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64919114"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Azure AD Connect 동기화를 사용하여 개체 동기화 문제 해결
이 문서에서는 문제 해결 작업을 사용하여 개체 동기화 관련 문제를 해결하는 단계를 제공합니다. Azure AD(Active Directory) 연결에서 문제 해결이 작동하는 방식은 [이 짧은 비디오](https://aka.ms/AADCTSVideo)에서 확인하세요.

## <a name="troubleshooting-task"></a>문제 해결 작업
버전 1.1.749.0 이상인 Azure AD Connect 배포의 경우 마법사에서 문제 해결 작업을 사용하여 개체 동기화 문제를 해결합니다. 이전 버전의 경우 [여기](tshoot-connect-object-not-syncing.md)에 설명된 대로 수동으로 문제를 해결하세요.

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>마법사에서 문제 해결 작업을 실행합니다.
마법사에서 문제 해결 작업을 실행하려면 다음 단계를 수행하세요.

1.  [관리자 권한으로 실행] 옵션을 사용하여 Azure AD Connect 서버에서 새 Windows PowerShell 세션을 엽니다.
2.  `Set-ExecutionPolicy RemoteSigned` 또는 `Set-ExecutionPolicy Unrestricted`를 실행합니다.
3.  Azure AD Connect 마법사를 시작합니다.
4.  [추가 작업] 페이지로 이동하고 [문제 해결]을 선택하고 [다음]을 클릭합니다.
5.  [문제 해결] 페이지에서 [시작]을 클릭하여 PowerShell의 문제 해결 메뉴를 시작합니다.
6.  주 메뉴에서 [개체 동기화 문제 해결]을 선택합니다.
![개체 동기화 문제 해결](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>입력 매개 변수 문제 해결
다음 입력 매개 변수가 문제 해결 작업에 필요합니다.
1.  **개체 고유 이름** - 문제 해결이 필요한 개체의 고유 이름입니다.
2.  **AD 커넥터 이름** - 위의 개체가 있는 AD 포리스트의 이름입니다.
3.  Azure AD 테넌트 전역 관리자 자격 증명 ![전역 관리자 자격 증명](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>문제 해결 작업의 결과 이해
문제 해결 작업에서는 다음 검사를 수행합니다.

1.  개체가 Azure Active Directory로 동기화된 경우 UPN 불일치 검색
2.  도메인 필터링으로 인해 객체가 필터링되었는지 확인
3.  개체가 OU 필터링으로 인해 필터링되는지 확인
4.  연결된 사서함으로 인해 개체 동기화가 차단되었는지 확인
5. 개체가 동기화되지 않는 동적 배포 목록인지 확인

이 섹션의 나머지 부분에서는 이 작업에서 반환되는 특정 결과에 대해 설명합니다. 각각의 경우 문제 해결 작업에서는 분석을 제공한 후 문제를 해결하기 위해 권장되는 조치를 제공합니다.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>개체가 Azure Active Directory로 동기화된 경우 UPN 불일치 검색
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Azure AD 테넌트로 UPN 접미사가 확인되지 않음
Azure AD 테넌트로 UserPrincipalName(UPN)/대체 로그인 ID 접미사가 확인되지 않으면 Azure Active Directory는 기본 도메인 이름 “onmicrosoft.com”으로 UPN 접미사를 바꿉니다.

![Azure AD를 사용 하면 UPN](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>하나의 페더레이션된 도메인에서 다른 페더레이션된 도메인으로 UPN 접미사 변경
Azure Active Directory는 하나의 페더레이션된 도메인에서 다른 페더레이션된 도메인으로 UserPrincipalName(UPN)/대체 로그인 ID 접미사 변경의 동기화를 허용하지 않습니다. 이는 Azure AD 테넌트로 확인되고 인증 유형이 페더레이션됨인 도메인에 적용됩니다.

![다른 하나의 페더레이션된 도메인에서 UPN 동기화 없음](media/tshoot-connect-objectsync/objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Azure AD 테넌트 디렉터리 동기화 기능 ‘SynchronizeUpnForManagedUsers’가 사용하지 않도록 설정됨
Azure AD 테넌트 디렉터리 동기화 기능 ‘SynchronizeUpnForManagedUsers’가 사용하지 않도록 설정된 경우 Azure Active Directory는 관리되는 인증이 있는 라이선스가 부여된 사용자 계정의 UserPrincipalName/대체 로그인 ID로 동기화 업데이트를 허용하지 않습니다.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>도메인 필터링으로 인해 개체가 필터링됨
### <a name="domain-is-not-configured-to-sync"></a>도메인이 동기화되도록 구성되지 않음
도메인이 구성되지 않아 객체가 범위를 벗어납니다. 아래 예제에서는 개체가 속한 도메인이 동기화에서 필터링되어 제외되었으므로 해당 개체가 동기화 범위를 벗어납니다.

![도메인이 동기화되도록 구성되지 않음](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>도메인이 동기화되도록 구성되었지만 실행 프로필/실행 단계가 없습니다.
도메인에 실행 프로필/실행 단계가 없기 때문에 개체가 범위를 벗어났습니다. 개체가 속하는 도메인에 전체 가져오기 실행 프로필에 대한 실행 단계가 없기 때문에 아래 예제에서 개체는 동기화 범위를 벗어납니다.
![누락 된 실행된 프로필](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>개체가 OU 필터링으로 인해 필터링됨
OU 필터링 구성으로 인해 개체가 동기화 범위를 벗어납니다. 아래 예제에서 개체는 OU=NoSync,DC=bvtadwbackdc,DC=com에 속합니다.  이 OU는 동기화 범위에 포함되지 않습니다.</br>

![OU](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>연결된 사서함 문제
연결된 사서함은 다른 신뢰할 수 있는 계정 포리스트에 위치한 외부 마스터 계정과 연결되어야 합니다. 이러한 외부 마스터 계정이 없는 경우 Azure AD Connect에서는 Exchange 포리스트의 연결된 사서함에 해당하는 사용자 계정을 Azure AD 테넌트에 동기화하지 않습니다.</br>
![연결된 사서함](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>동적 배포 목록 문제
온-프레미스 Active Directory와 Azure Active Directory 간의 다양한 차이 때문에 Azure AD Connect는 Azure AD 테넌트에 동적 배포 목록을 동기화하지 않습니다.

![동적 메일 그룹](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>HTML 보고서
문제 해결 작업은 개체를 분석하는 것 외에 개체에 대해 알려진 모든 정보가 포함된 HTML 보고서도 생성합니다. 필요한 경우 추가 문제 해결을 위해 이 HTML 보고서를 지원 팀과 공유할 수 있습니다.

![HTML 보고서](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
