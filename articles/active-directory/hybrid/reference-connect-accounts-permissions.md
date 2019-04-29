---
title: 'Azure AD Connect: 계정 및 사용 권한 | Microsoft Docs'
description: 이 항목에서는 사용되고 만든 계정 및 필요한 권한을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d145407331ed652f21510483b51a4617bf28e2fa
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096175"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: 계정 및 사용 권한

## <a name="accounts-used-for-azure-ad-connect"></a>Azure AD Connect에 사용되는 계정

![](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect는 온-프레미스 또는 Windows Server Active Directory의 정보를 Azure Active Directory와 동기화하기 위해 3개의 계정을 사용합니다.  이러한 계정은 다음과 같습니다.

- **AD DS Connect 계정**: Windows Server Active Directory에서 정보를 읽고 쓰는 데 사용됩니다.

- **ADSync 서비스 계정**: 동기화 서비스를 실행하고 SQL Database에 액세스하는 데 사용됩니다.

- **Azure AD Connect 계정**: Azure AD에 정보를 쓰는 데 사용됩니다.

Azure AD Connect를 실행하는 데 사용되는 이러한 세 가지 계정 외에, Azure AD Connect를 설치하기 위해 다음 추가 계정도 필요합니다.  다음과 같습니다.

- **로컬 관리자 계정**: Azure AD Connect를 설치하고 머신의 로컬 관리자 권한을 가진 관리자.

- **AD DS Enterprise 관리자 계정**: 필요에 따라 위의 "AD DS 커넥터 계정"을 만드는 데 사용됩니다.

- **Azure AD 전역 관리자 계정**: Azure AD Connect 계정을 만들고 Azure AD를 구성하는 데 사용됩니다.

- **SQL SA 계정(선택 사항)**: SQL Server의 전체 버전을 사용할 때 ADSync 데이터베이스를 만드는 데 사용됩니다.  이 SQL Server는 Azure AD Connect 설치의 로컬 또는 원격일 수 있습니다.  이 계정은 엔터프라이즈 관리자와 동일한 계정일 수 있습니다.  이제 SQL 관리자가 대역 외에서 데이터베이스를 프로비전한 후 데이터베이스 소유권이 있는 Azure AD Connect 관리자가 설치할 수 있습니다.  이에 대한 내용은 [SQL 위임된 관리자 권한을 사용하여 Azure AD Connect 설치](how-to-connect-install-sql-delegation.md)를 참조하세요.

## <a name="installing-azure-ad-connect"></a>Azure AD Connect 설치
Azure AD Connect 설치 마법사는 두 가지 다른 경로를 제공합니다.

* Express 설정에서, 마법사에는 더 많은 권한이 필요합니다.  이렇게 하면 사용자를 만들거나 사용 권한을 구성할 필요 없이 구성을 쉽게 설정할 수 있습니다.
* 사용자 지정 설정에서, 마법사는 더 많은 선택 사항 및 옵션을 제공합니다. 그러나 자신이 올바른 권한을 가지고 있는지 확인해야 하는 경우가 있습니다.



## <a name="express-settings-installation"></a>Express 설정 설치
기본 설정에서 설치 마법사는 다음을 묻는 메시지를 표시합니다.

  - AD DS 엔터프라이즈 관리자 자격 증명
  - Azure AD 전역 관리자 자격 증명

### <a name="ad-ds-enterprise-admin-credentials"></a>AD DS 엔터프라이즈 관리자 자격 증명
AD DS 엔터프라이즈 관리자 자격 증명은 온-프레미스 Active Directory를 구성하는 데 사용됩니다. 이러한 자격 증명은 설치 중에 사용되고 설치가 완료된 후에 사용되지 않습니다. 도메인 관리자가 아닌 엔터프라이즈 관리자가 모든 도메인에서 Active Directory에 사용 권한을 설정해야 합니다.

DirSync에서 업그레이드하는 경우 AD DS Enterprise 관리자 자격 증명은 DirSync에서 사용되는 계정의 암호를 다시 설정하는 데 사용됩니다. 또한 Azure AD 전역 관리자 자격 증명이 필요합니다.

### <a name="azure-ad-global-admin-credentials"></a>Azure AD 전역 관리자 자격 증명
이러한 자격 증명은 설치 중에 사용되고 설치가 완료된 후에 사용되지 않습니다. Azure AD에 대한 변경 내용을 동기화하기 위해 사용된 Azure AD Connect 계정을 만드는 데 사용됩니다. 또한 계정을 사용하면 Azure AD에서 기능으로 동기화할 수 있습니다.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>기본 설정에 대한 AD DS Connect 계정 필수 권한
AD DS Connector 계정은 Windows Server AD에서 읽고 쓰기 위해 만들어지며, 기본 설정에 의해 생성될 경우 다음과 같은 사용 권한을 갖습니다.

| 사용 권한 | 용도 |
| --- | --- |
| <li>디렉터리 변경 내용 복제</li><li>모든 디렉터리 변경 내용 복제 |암호 해시 동기화 |
| 모든 속성 사용자 읽기/쓰기  |가져오기 및 Exchange 하이브리드 |
| 모든 속성 iNetOrgPerson 읽기/쓰기  |가져오기 및 Exchange 하이브리드 |
| 모든 속성 그룹 읽기/쓰기 |가져오기 및 Exchange 하이브리드 |
| 모든 속성 연락처 읽기/쓰기 |가져오기 및 Exchange 하이브리드 |
| 암호 재설정 |비밀번호 쓰기 저장을 사용하기 위한 준비 |

### <a name="express-installation-wizard-summary"></a>기본 설치 마법사 요약

![기본 설치](./media/reference-connect-accounts-permissions/express.png)

다음은 기본 설치 마법사 페이지, 수집되는 자격 증명 및 용도를 요약한 것입니다.

| 마법사 페이지 | 수집되는 자격 증명 | 필요한 사용 권한 | 용도 |
| --- | --- | --- | --- |
| N/A |설치 마법사를 실행하는 사용자 |로컬 서버의 관리자 |<li>동기화 서비스 실행과 관련해서 사용되는 ADSync 서비스 계정을 만듭니다. |
| Azure에 연결 |Azure AD 디렉터리 자격 증명 |Azure AD에서 글로벌 관리자 역할 |<li>Azure AD 디렉터리에서 동기화를 사용하도록 설정합니다.</li>  <li>Azure AD에서 진행 중인 동기화 작업에 사용되는 Azure AD Connect 계정을 만듭니다.</li> |
| AD DS에 연결 |온-프레미스 Active Directory 자격 증명 |Active Directory의 Enterprise Admins(EA) 그룹의 구성원 |<li>Active Directory에서 AD DS Connector 계정을 만들고 사용 권한을 부여합니다. 만든 계정은 동기화 중에 디렉터리 정보를 읽고 쓰는 데 사용됩니다.</li> |


## <a name="custom-installation-settings"></a>사용자 지정 설치 설정

사용자 지정 설정 설치를 사용할 경우 마법사는 더 많은 선택 사항 및 옵션을 제공합니다. 

### <a name="custom-installation-wizard-summary"></a>사용자 지정 설치 마법사 요약

다음은 사용자 지정 설치 마법사 페이지, 수집되는 자격 증명 및 용도를 요약한 것입니다.

![기본 설치](./media/reference-connect-accounts-permissions/customize.png)

| 마법사 페이지 | 수집되는 자격 증명 | 필요한 사용 권한 | 용도 |
| --- | --- | --- | --- |
| N/A |설치 마법사를 실행하는 사용자 |<li>로컬 서버의 관리자</li><li>전체 SQL Server를 사용하는 경우 사용자는 SQL의 시스템 관리자(SA)여야 합니다.</li> |기본적으로 엔진 서비스 계정 동기화로 사용되는 로컬 계정을 만듭니다. 계정은 관리자가 특정 계정을 지정하지 않은 경우에 만들어집니다. |
| 동기화 서비스, 서비스 계정 옵션을 설치합니다. |AD 또는 로컬 사용자 계정 자격 증명 |사용자, 권한은 설치 마법사에서 부여됩니다. |관리자가 계정을 지정하는 경우, 이 계정은 동기화 서비스에 대한 서비스 계정으로 사용됩니다. |
| Azure에 연결 |Azure AD 디렉터리 자격 증명 |Azure AD에서 글로벌 관리자 역할 |<li>Azure AD 디렉터리에서 동기화를 사용하도록 설정합니다.</li>  <li>Azure AD에서 진행 중인 동기화 작업에 사용되는 Azure AD Connect 계정을 만듭니다.</li> |
| 디렉터리에 연결 |Azure AD에 연결되는 각 포리스트의 온-프레미스 Active Directory 자격 증명 |사용 권한은 어떤 기능을 사용하는지에 따라 달라지며 AD DS Connector 계정 만들기에서 찾을 수 있음 |계정은 동기화 중에 디렉터리 정보를 읽고 쓰는 데 사용됩니다. |
| AD FS 서버 |목록의 각 서버에 대해, 마법사를 실행하는 사용자의 로그온 자격 증명이 연결하기에 충분하지 않으면 마법사는 자격 증명을 수집합니다. |도메인 관리자 |AD FS 서버 역할 설치 및 구성 |
| 웹 애플리케이션 프록시 서버 |목록의 각 서버에 대해, 마법사를 실행하는 사용자의 로그온 자격 증명이 연결하기에 충분하지 않으면 마법사는 자격 증명을 수집합니다. |대상 컴퓨터의 로컬 관리자 |WAP 서버 역할 설치 및 구성 |
| 프록시 트러스트 자격 증명 |페더레이션 서비스 자격 증명(FS에서 프록시가 신뢰 인증서를 등록하는 데 사용하는 자격 증명) |AD FS 서버의 로컬 관리자인 도메인 계정 |FS-WAP 신뢰 인증서의 초기 등록. |
| AD FS 서비스 계정 페이지에서 "도메인 사용자 계정 옵션 사용" |AD 사용자 계정 자격 증명 |도메인 사용자 |해당 자격 증명을 제공하는 AD 사용자 계정이 AD FS 서비스의 로그온 계정으로 사용됩니다. |

### <a name="create-the-ad-ds-connector-account"></a>AD DS Connect 계정 만들기

>[!IMPORTANT]
>ADSyncConfig.psm1이라는 새 PowerShell 모듈은 Azure AD DS 커넥터 계정에 대한 올바른 Active Directory 권한을 구성하는 데 도움이 되는 cmdlet 컬렉션이 포함된 빌드 **1.1.880.0**(2018년 8월에 릴리스됨)에서 소개되었습니다.
>
>자세한 내용은 [Azure AD Connect: AD DS 커넥터 계정 권한 구성](how-to-connect-configure-ad-ds-connector-account.md)을 참조하세요.

**디렉터리에 연결** 페이지에서 지정할 계정은 설치 전에 Active Directory에 있어야 합니다.  Azure AD Connect 버전 1.1.524.0 이상에는 Azure AD Connect 마법사가 Active Directory에 연결하는 데 사용되는 **AD DS Connector 계정**을 만들 수 있는 옵션이 제공됩니다.  

필요한 권한도 부여되어 있어야 합니다. 설치 마법사는 사용 권한을 확인하지 않고 문제는 동기화 중에 발견됩니다.

어떤 사용 권한이 필요한지는 사용하도록 설정할 선택적 기능에 따라 달라집니다. 여러 도메인이 있는 경우 포리스트의 모든 도메인에 대한 사용 권한이 부여되어야 합니다. 이러한 기능을 사용하지 않는 경우 기본 **도메인 사용자** 사용 권한만으로도 충분합니다.

| 기능 | 권한 |
| --- | --- |
| ms-DS-ConsistencyGuid 기능 |[디자인 개념 - ms-DS-ConsistencyGuid를 sourceAnchor로 사용](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)에서 설명하는 ms-DS-ConsistencyGuid 특성에 대한 쓰기 권한. | 
| 암호 해시 동기화 |<li>디렉터리 변경 내용 복제</li>  <li>모든 디렉터리 변경 내용 복제 |
| Exchange 하이브리드 배포 |사용자, 그룹 및 연락처에 대한 [Exchange 하이브리드 쓰기 저장](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback)에 설명된 특성에 사용 권한을 작성합니다. |
| Exchange 메일 공용 폴더 |공용 폴더의 [Exchange Mail 공용 폴더](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder)에서 설명하는 특성에 대한 읽기 권한 | 
| 비밀번호 쓰기 저장 |사용자에 대한 [암호 관리 시작](../authentication/howto-sspr-writeback.md)에 설명된 특성에 사용 권한을 작성합니다. |
| 디바이스 쓰기 저장 |[디바이스 쓰기 저장](how-to-connect-device-writeback.md)에 설명한 대로 PowerShell 스크립트에 부여된 사용 권한입니다. |
| 그룹 쓰기 저장 |설치된 Exchange로 포리스트에 **Office 365 그룹**을 쓰기 저장할 수 있습니다.  자세한 내용은 [그룹 쓰기 저장](how-to-connect-preview.md#group-writeback)을 참조하세요.|

## <a name="upgrade"></a>업그레이드
Azure AD Connect의 한 버전에서 새 릴리스로 업그레이드하는 경우 다음 권한이 필요합니다.

>[!IMPORTANT]
>Azure AD Connect에서는 빌드 1.1.484부터 SQL Database를 업그레이드하기 위해 sysadmin 권한이 필요한 회귀 버그를 도입했습니다.  이 버그는 1.1.647 빌드에서 수정되었습니다.  이 빌드로 업그레이드하는 경우 sysadmin 권한이 필요합니다.  Dbo 권한은 충분하지 않습니다.  sysadmin 권한 없이 Azure AD Connect를 업그레이드하려고 하면 업그레이드에 실패하고 이후로 Azure AD Connect가 더 이상 올바르게 작동하지 않습니다.  Microsoft는 이를 알고 해결하기 위해 노력하고 있습니다.


| 주체 | 필요한 사용 권한 | 용도 |
| --- | --- | --- |
| 설치 마법사를 실행하는 사용자 |로컬 서버의 관리자 |이진을 업데이트합니다. |
| 설치 마법사를 실행하는 사용자 |ADSyncAdmins의 구성원 |동기화 규칙 및 기타 구성을 변경합니다. |
| 설치 마법사를 실행하는 사용자 |전체 SQL 서버를 사용하는 경우: 동기화 엔진 데이터베이스의 DBO(또는 이와 유사한) |새 열을 사용한 테이블 업데이트와 같이 데이터베이스 수준을 변경합니다. |

## <a name="more-about-the-created-accounts"></a>만든 계정에 대한 자세한 내용
### <a name="ad-ds-connector-account"></a>AD DS Connect 계정
Express 설정을 사용하는 경우 계정은 동기화에 사용되는 Active Directory에서 만들어집니다. 만든 계정은 사용자 컨테이너의 포리스트 루트 도메인에 위치하고 **MSOL_** 를 접두사로 하는 이름을 갖습니다. 계정은 만료되지 않은 길고 복잡한 암호를 사용하여 만들어집니다. 사용자 도메인에 암호 정책이 있는 경우 길고 복잡한 암호가 이 계정에 대해 허용되는지 확인합니다.

![AD 계정](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

사용자 지정 설정을 사용하는 경우에는 설치를 시작하기 전에 계정을 만들 책임이 있습니다.  AD DS Connect 계정 만들기를 참조하세요.

### <a name="adsync-service-account"></a>ADSync 서비스 계정
동기화 서비스는 다양한 계정으로 실행할 수 있습니다. **가상 서비스 계정**(VSA), **그룹 관리 서비스 계정**(gMSA/sMSA) 또는 일반 사용자 계정으로 실행할 수 있습니다. 지원되는 옵션은 새로 설치 시 Connect의 2017년 4월 릴리스에서 변경되었습니다. Azure AD Connect의 이전 버전을 업그레이드하는 경우 이러한 추가 옵션을 사용할 수 없습니다.

| 계정 유형 | 설치 옵션 | 설명 |
| --- | --- | --- |
| [가상 서비스 계정](#virtual-service-account) | 빠른 및 사용자 지정, 2017년 4월 이후 | 도메인 컨트롤러에서 설치를 제외한 모든 빠른 설치에 사용되는 옵션입니다. 다른 옵션이 사용되지 않는 한 사용자 지정 설치에 대해 기본 옵션입니다. |
| [그룹 관리 서비스 계정](#group-managed-service-account) | 사용자 지정, 2017년 4월 이후 | 원격 SQL Server를 사용하는 경우에는 그룹 관리 서비스 계정을 사용하는 것이 좋습니다. |
| [사용자 계정](#user-account) | 빠른 및 사용자 지정, 2017년 4월 이후 | AAD_가 접두사로 추가되는 사용자 계정은 Windows Server 2008에 설치되는 경우 및 도메인 컨트롤러에 설치되는 경우 설치 중에만 만들어집니다. |
| [사용자 계정](#user-account) | 빠른 및 사용자 지정, 2017 3월 이전 | AAD_가 접두사로 추가되는 사용자 계정은 설치 중에 만들어집니다. 사용자 지정 설치를 사용하는 경우 다른 계정을 지정할 수 있습니다. |

Connect의 2017년 3월 이전 빌드를 사용하는 경우에는 서비스 계정의 암호를 재설정하지 말아야 합니다. 보안상의 이유로 Windows에서 암호화 키를 제거하기 때문입니다. Azure AD Connect를 다시 설치하지 않고는 계정을 다른 계정으로 변경할 수 없습니다. 2017년 4월 이후의 빌드를 업그레이드하는 경우에는 서비스 계정의 암호를 변경하는 것이 지원되지만 사용된 계정은 변경할 수 없습니다.

> [!Important]
> 서비스 계정은 처음 설치할 때만 설정할 수 있습니다. 설치가 완료된 후에는 서비스 계정을 변경하는 것이 지원되지 않습니다.

다음은 동기화 서비스 계정에 대한 기본, 권장 및 지원 옵션 테이블입니다.

범례:

- **굵은 글꼴**은 기본 옵션 및 대부분의 경우 권장 옵션을 나타냅니다.
- *기울임꼴*은 기본 옵션이 아닌 경우 권장 옵션을 나타냅니다.
- 2008 - Windows Server 2008에 설치하는 경우 기본 옵션
- 굵지 않은 글꼴 - 지원되는 옵션
- 로컬 계정 - 서버의 로컬 사용자 계정
- 도메인 계정 - 도메인 사용자 계정
- sMSA - [독립 실행형 관리 서비스 계정](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [그룹 관리 서비스 계정](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>사용자 지정 | 원격 SQL</br>사용자 지정 |
| --- | --- | --- | --- |
| **독립 실행형/작업 그룹 컴퓨터** | 지원되지 않음 | **VSA**</br>로컬 계정(2008)</br>로컬 계정 |  지원되지 않음 |
| **도메인에 가입된 컴퓨터** | **VSA**</br>로컬 계정(2008) | **VSA**</br>로컬 계정(2008)</br>로컬 계정</br>도메인 계정</br>sMSA,gMSA | **gMSA**</br>도메인 계정 |
| **도메인 컨트롤러** | **도메인 계정** | *gMSA*</br>**도메인 계정**</br>sMSA| *gMSA*</br>**도메인 계정**|

#### <a name="virtual-service-account"></a>가상 서비스 계정
가상 서비스 계정은 암호가 없고 Windows에 의해 관리되는 특수한 유형의 계정입니다.

![VSA](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

VSA는 동기화 엔진과 SQL이 동일한 서버에 있는 시나리오에서 사용됩니다. 원격 SQL을 사용하는 경우 대신 그룹 관리 서비스 계정을 사용하는 것이 좋습니다.

이 기능을 사용하려면 Windows Server 2008 R2 이상이 필요합니다. Windows Server 2008에 Azure AD Connect를 설치하는 경우에는 설치가 [사용자 계정](#user-account)을 대신 사용하도록 대체됩니다.

#### <a name="group-managed-service-account"></a>그룹 관리 서비스 계정
원격 SQL Server를 사용하는 경우에는 **그룹 관리 서비스 계정**을 사용하는 것이 좋습니다. 그룹 관리 서비스 계정에 대해 Active Directory를 준비하는 방법에 대한 자세한 내용은 [그룹 관리 서비스 계정 개요](https://technet.microsoft.com/library/hh831782.aspx)를 참조하세요.

이 옵션을 사용하려면 [필수 구성 요소 설치](how-to-connect-install-custom.md#install-required-components) 페이지에서 **기존 서비스 계정 사용**을 선택하고 **관리 서비스 계정**을 선택합니다.  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
[독립 실행형 관리 서비스 계정](https://technet.microsoft.com/library/dd548356.aspx)을 사용하는 것도 지원됩니다. 하지만 이 계정은 로컬 컴퓨터에서만 사용할 수 있기 때문에 기본 가상 서비스 계정 대신 이 계정을 사용할만한 장점이 없습니다.

이 기능을 사용하려면 Windows Server 2012 이상이 필요합니다. 이전 운영 체제를 사용해야 하고 원격 SQL을 사용하는 경우에는 [사용자 계정](#user-account)을 사용해야 합니다.

#### <a name="user-account"></a>사용자 계정
로컬 서비스 계정은 설치 마법사에서 만듭니다.(사용자 지정 설정에 사용할 계정을 지정하지 않으면) 계정은 **AAD_** 를 접두사로 하며 실행할 실제 동기화 서비스에 사용됩니다. 도메인 컨트롤러에 Azure AD Connect를 설치하는 경우 계정은 도메인에 만들어집니다. 다음과 같은 경우 **AAD_** 서비스 계정이 도메인에 있어야 합니다.
   - SQL Server를 실행하는 원격 서버를 사용합니다.
   - 인증이 필요한 프록시를 사용합니다.

![서비스 계정 동기화](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

계정은 만료되지 않은 길고 복잡한 암호를 사용하여 만들어집니다.

이 계정은 다른 계정의 암호를 안전하게 저장하는 데 사용됩니다. 이러한 다른 계정 암호는 데이터베이스에 암호화된 상태로 저장됩니다. 암호화 키의 개인 키는 Windows DPAPI(데이터 보호 API)를 사용하여 암호화 서비스 비밀 키 암호화로 보호됩니다.

전체 SQL Server를 사용하는 경우 서비스 계정은 동기화 엔진에 대해 만든 데이터베이스의 DBO입니다. 서비스는 다른 사용 권한이 의도한 대로 작동하지 않습니다. SQL 로그인도 생성됩니다.

또한 계정에는 동기화 엔진에 관련된 파일, 레지스트리 키 및 다른 개체에 대한 사용 권한이 부여됩니다.

### <a name="azure-ad-connector-account"></a>Azure AD Connect 계정
Azure AD의 계정은 동기화 서비스의 사용에 생성됩니다. 이 계정은 표시 이름으로 식별할 수 있습니다.

![AD 계정](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

계정을 사용하는 서버의 이름은 사용자 이름의 두 번째 부분에서 식별할 수 있습니다. 그림에서 서버 이름은 DC1입니다. 준비 서버가 있는 경우 각 서버는 고유한 계정을 포함합니다.

계정은 만료되지 않은 길고 복잡한 암호를 사용하여 만들어집니다. 또한 디렉터리 동기화 작업을 수행할 수 있는 유일한 권한이 있는 특별한 역할인 **디렉터리 동기화 계정** 이 부여됩니다. 이 특별한 기본 제공 역할은 Azure AD Connect 마법사 외부에서 부여할 수 없습니다. Azure Portal은 **사용자** 역할에서만 이 계정을 표시합니다.

Azure AD에서 동기화 서비스 계정은 20개로 제한됩니다. Azure AD에서 기존 Azure AD 서비스 계정의 목록을 가져오려면 다음 Azure AD PowerShell cmdlet을 실행합니다. `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

사용하지 않는 Azure AD 서비스 계정을 제거하려면 다음 Azure AD PowerShell cmdlet을 실행합니다. `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

## <a name="related-documentation"></a>관련 설명서
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대한 설명서를 읽지 않은 경우 다음 테이블에서 관련 항목에 대한 링크를 제공합니다.

|항목 |링크|  
| --- | --- |
|Azure AD Connect 다운로드 | [Azure AD Connect 다운로드](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Express 설정을 사용하여 설치 | [Azure AD Connect의 빠른 설치](how-to-connect-install-express.md)|
|사용자 지정 설정을 사용하여 설치 | [Azure AD Connect의 사용자 지정 설치](./how-to-connect-install-custom.md)|
|DirSync에서 업그레이드 | [Azure AD Sync 도구(DirSync)에서 업그레이드](how-to-dirsync-upgrade-get-started.md)|
|설치 후 | [설치를 확인 하 고 라이선스 할당](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
