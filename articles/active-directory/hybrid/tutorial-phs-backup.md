---
title: '자습서:  Azure AD Connect에서 AD FS용 백업으로 PHS 설정 | Microsoft Docs'
description: AD FS용 백업으로 암호 해시 동기화를 설정하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9cd70d534cebf35938285208dc76bd4a6d94533
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89279009"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>자습서:  Azure AD Connect에서 AD FS용 백업으로 PHS 설정

다음 자습서에서는 AD FS용 백업 및 장애 조치(failover)로 암호 해시 동기화를 설정하는 방법을 안내합니다.  또한 AD FS에서 오류가 발생하거나 AD FS를 사용할 수 없는 상태가 되면 기본 인증 방법으로 암호 해시 동기화를 사용하도록 설정하는 방법도 설명합니다.

>[!NOTE] 
>이러한 단계는 대개 응급 또는 중단 상황에서 수행되지만, 정전이 발생하기 전에 이러한 단계를 테스트하고 절차를 확인하는 것이 좋습니다.

>[!NOTE]
>Azure AD Connect 서버에 대한 액세스 권한이 없거나 서버가 인터넷에 연결되어 있지 않으면, Azure AD 쪽에서 변경을 지원하도록 [Microsoft 지원](https://support.microsoft.com/en-us/contactus/)에 문의할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 자습서는 [자습서: 자습서: 클라우드에 단일 AD 포리스트 환경 페더레이션](tutorial-federation.md)을 기준으로 작성되었습니다. 이 자습서의 작업을 시도하려면 먼저 해당 자습서를 진행해야 합니다.  이 자습서를 완료하지 않았다면 이 문서의 단계를 시도하기 전에 완료하세요.

>[!IMPORTANT]
>PHS로 전환하기 전에 AD FS 환경의 백업을 만들어야 합니다.  이 작업은 [AD FS 신속 복원 도구](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool)를 사용하여 수행할 수 있습니다.

## <a name="enable-phs-in-azure-ad-connect"></a>Azure AD Connect에서 PHS를 사용하도록 설정
페더레이션을 사용하는 Azure AD Connect 환경이 준비되면 먼저 암호 해시 동기화를 설정하고 Azure AD Connect가 해시를 동기화하도록 허용해야 합니다.

다음을 수행합니다.

1.  바탕 화면에 생성된 Azure AD Connect 아이콘을 두 번 클릭합니다.
2.  **Configure**를 클릭합니다.
3.  추가 작업 페이지에서 **동기화 옵션 사용자 지정**을 선택하고 **다음**을 클릭합니다.
4.  전역 관리자의 사용자 이름과 암호를 입력합니다.  이 계정은 이전 자습서([여기](tutorial-federation.md#create-a-global-administrator-in-azure-ad))서 만든 것입니다.
5.  **디렉터리 연결** 화면에서 **다음**을 클릭합니다.
6.  **도메인 및 OU 필터링** 화면에서 **다음**을 클릭합니다.
7.  **선택적 기능** 화면에서 **암호 해시 동기화**를 선택하고 **다음**을 클릭합니다.
![Select](media/tutorial-phs-backup/backup1.png)</br>
8.  **구성 준비 완료** 화면에서 **구성**을 클릭합니다.
9.  구성이 완료되면 **끝내기**를 클릭합니다.
10. 이것으로 끝입니다.  이제 구성을 완료했으므로  암호 해시 동기화가 진행되며, AD FS를 사용할 수 없게 되면 암호 해시 동기화를 백업으로 사용할 수 있습니다.

## <a name="switch-to-password-hash-synchronization"></a>암호 해시 동기화로 전환
이제 암호 해시 동기화로 전환하는 방법을 살펴보겠습니다. 시작에 앞서 어떤 조건에서 전환해야 할지 고려합니다. 네트워크 중단, 사소한 AD FS 문제 또는 사용자 하위 집합에 영향을 미치는 문제와 같은 일시적인 이유로는 전환하지 마십시오. 문제를 해결하는 데 시간이 너무 오래 걸려서 전환하기로 결정한 경우, 다음을 수행합니다.

> [!IMPORTANT]
> 암호 해시가 Azure AD와 동기화되는 데는 몇 시간이 걸립니다.  즉, 동기화가 완료되고 암호 해시를 사용하여 인증을 시작하려면 3시간이 걸릴 수 있습니다.

1. 바탕 화면에 생성된 Azure AD Connect 아이콘을 두 번 클릭합니다.
2.  **Configure**를 클릭합니다.
3.  **사용자 로그인 변경**을 선택하고 **다음**을 클릭합니다.
![변경](media/tutorial-phs-backup/backup2.png)</br>
4.  전역 관리자의 사용자 이름과 암호를 입력합니다.  이 계정은 이전 자습서([여기](tutorial-federation.md#create-a-global-administrator-in-azure-ad))서 만든 것입니다.
5.  **사용자 로그인** 화면에서 **암호 해시 동기화**를 선택하고 **사용자 계정 변환 안 함** 확인란을 선택합니다.  
6.  **Single Sign-On 사용**을 기본 상태(선택됨)로 유지하고 **다음**을 클릭합니다.
7.  **Single Sign-On 사용** 화면에서 **다음**을 클릭합니다.
8.  **구성 준비 완료** 화면에서 **구성**을 클릭합니다.
9.  구성이 완료되면 **끝내기**를 클릭합니다.
10. 이제 사용자가 암호를 사용하여 Azure 및 Azure Services에 로그인할 수 있습니다.

## <a name="test-signing-in-with-one-of-our-users"></a>사용자 중 한 명으로 로그인 테스트

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)으로 이동합니다.
2. 새 테넌트에 생성된 사용자 계정으로 로그인합니다.  user@domain.onmicrosoft.com 형식을 사용하여 로그인해야 합니다. 사용자가 온-프레미스 로그인에 사용하는 것과 동일한 암호를 사용합니다.</br>
   ![Verify](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>페더레이션으로 다시 전환
이제 페더레이션으로 다시 전환하는 방법을 살펴보겠습니다.  이 작업은 다음과 같이 수행합니다.

1.  바탕 화면에 생성된 Azure AD Connect 아이콘을 두 번 클릭합니다.
2.  **Configure**를 클릭합니다.
3.  **사용자 로그인 변경**을 선택하고 **다음**을 클릭합니다.
4.  전역 관리자의 사용자 이름과 암호를 입력합니다.  이 계정은 이전 자습서([여기](tutorial-federation.md#create-a-global-administrator-in-azure-ad))에서 만든 계정입니다.
5.  **사용자 로그인** 화면에서 **AD FS로 페더레이션**을 선택하고 **다음**을 클릭합니다.  
6. 도메인 관리자 자격 증명 페이지에서 contoso\Administrator 사용자 이름과 암호를 입력하고 **다음**을 클릭합니다.
7. AD FS 팜 화면에서 **다음**을 클릭합니다.
8. **Azure AD 도메인** 화면의 드롭다운에서 도메인을 선택하고 **다음**을 클릭합니다.
9. **구성 준비 완료** 화면에서 **구성**을 클릭합니다.
10. 구성이 완료되면 **다음**을 클릭합니다.
![구성](media/tutorial-phs-backup/backup4.png)</br>
11. **페더레이션 연결 확인** 화면에서 **확인**을 클릭합니다.  성공적으로 완료하려면 DNS 레코드(A 및 AAAA 레코드 추가)를 구성해야 할 수도 있습니다.
![Verify](media/tutorial-phs-backup/backup5.png)</br>
12. **종료**를 클릭합니다.

## <a name="reset-the-ad-fs-and-azure-trust"></a>AD FS와 Azure 트러스트 다시 설정
이제 AD FS와 Azure AD 간의 트러스트를 다시 설정해야 합니다.

1.  바탕 화면에 생성된 Azure AD Connect 아이콘을 두 번 클릭합니다.
2.  **Configure**를 클릭합니다.
3.  **페더레이션 관리**를 선택하고 **다음**을 클릭합니다.
4.  **Azure AD 트러스트 재설정**을 선택하고 **다음**을 클릭합니다.
![재설정](media/tutorial-phs-backup/backup6.png)</br>
5.  **Azure AD에 연결** 화면에서 전역 관리자의 사용자 이름 및 암호를 입력합니다.
6.  **AD FS에 연결** 화면에서 contoso/Administrator 사용자 이름과 암호를 입력하고 **다음**을 클릭합니다.
7.  **인증서** 화면에서 **다음**을 클릭합니다.

## <a name="test-signing-in-with-one-of-our-users"></a>사용자 중 한 명으로 로그인 테스트

1.  [https://myapps.microsoft.com](https://myapps.microsoft.com)으로 이동합니다.
2. 새 테넌트에 생성된 사용자 계정으로 로그인합니다.  다음 형식을 사용하여 로그인해야 합니다(user@domain.onmicrosoft.com). 사용자가 온-프레미스 로그인에 사용한 것과 동일한 암호를 사용합니다.
![Verify](media/tutorial-password-hash-sync/verify1.png)

Azure에 제공되는 기능을 테스트하고 익히는 데 사용할 수 있는 하이브리드 ID 환경이 성공적으로 설정되었습니다.

## <a name="next-steps"></a>다음 단계


- [하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md) 
- [Express 설정](how-to-connect-install-express.md)
- [암호 해시 동기화](how-to-connect-password-hash-synchronization.md)