---
title: 하이브리드 Azure AD 조인에 대 한 제어 된 유효성 검사-Azure AD
description: 전체 조직에서 한 번에 사용 하도록 설정 하기 전에 하이브리드 Azure AD 조인에 대 한 제어 된 유효성 검사를 수행 하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2c1a088e4c200dcc4a2ff35db942e3eb8480674
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512094"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인의 제어된 유효성 검사

모든 필수 구성 요소가 준비 되 면 Windows 장치가 자동으로 Azure AD 테넌트에 장치로 등록 됩니다. Azure AD에서 이러한 장치 id의 상태를 하이브리드 Azure AD 조인 이라고 합니다. 이 문서에서 설명 하는 개념에 대 한 자세한 내용은 [Azure Active Directory의 장치 관리 소개](overview.md) 문서와 [하이브리드 Azure Active Directory 조인 구현 계획](hybrid-azuread-join-plan.md)문서에서 찾을 수 있습니다.

조직에서는 전체 조직에서 한 번에 사용 하도록 설정 하기 전에 하이브리드 Azure AD 조인에 대 한 제어 된 유효성 검사를 수행할 수 있습니다. 이 문서에서는 하이브리드 Azure AD 조인에 대 한 제어 된 유효성 검사를 수행 하는 방법을 설명 합니다.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Windows 현재 장치에서 하이브리드 Azure AD 조인의 제어 된 유효성 검사

Windows 데스크톱 운영 체제를 실행하는 디바이스의 경우 지원되는 버전은 Windows 10주년 업데이트(버전 1607) 이상입니다. 가장 좋은 방법은 최신 버전의 Windows 10으로 업그레이드하는 것입니다.

Windows 현재 장치에서 하이브리드 Azure AD 조인의 유효성을 제어 하는 유효성 검사를 수행 하려면 다음을 수행 해야 합니다.

1. Active Directory (AD)에서 SCP (서비스 연결 지점) 항목의 선택을 취소 합니다 (있는 경우).
1. GPO (그룹 정책 개체)를 사용 하 여 도메인에 가입 된 컴퓨터에서 SCP에 대 한 클라이언트 쪽 레지스트리 설정 구성
1. AD FS를 사용 하는 경우 GPO를 사용 하 여 AD FS 서버에서 SCP에 대 한 클라이언트 쪽 레지스트리 설정도 구성 해야 합니다.  
1. Azure AD Connect에서 [동기화 옵션을 사용자 지정](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) 하 여 장치 동기화를 사용 하도록 설정 해야 할 수도 있습니다. 


### <a name="clear-the-scp-from-ad"></a>AD에서 SCP 지우기

ADSI 편집 (Active Directory Services 인터페이스 편집기)을 사용 하 여 AD의 SCP 개체를 수정할 수 있습니다.

1. 및 관리 워크스테이션에서 **ADSI 편집** 데스크톱 응용 프로그램을 시작 하거나 엔터프라이즈 관리자로 도메인 컨트롤러를 시작 합니다.
1. 도메인의 **구성 명명 컨텍스트에** 연결 합니다.
1. **Cn = Configuration, dc = contoso, dc = com** > **cn = Services** > **cn = 장치 등록 구성** 으로 이동 합니다.
1. **CN = Device Registration Configuration** 에서 리프 개체를 마우스 오른쪽 단추로 클릭 하 고 **속성** 을 선택 합니다.
   1. **특성 편집기** 창에서 **키워드** 를 선택 하 고 **편집** 을 클릭 합니다.
   1. **AzureADId** 및 **azureADName** 값 (한 번에 하나씩)을 선택 하 고 **제거** 를 클릭 합니다.
1. **ADSI 편집** 닫기


### <a name="configure-client-side-registry-setting-for-scp"></a>SCP에 대 한 클라이언트 쪽 레지스트리 설정 구성

다음 예제를 사용 하 여 GPO (그룹 정책 개체)를 만들어 장치의 레지스트리에 SCP 항목을 구성 하는 레지스트리 설정을 배포 합니다.

1. 그룹 정책 Management console을 열고 도메인에 새 그룹 정책 개체를 만듭니다.
   1. 새로 만든 GPO에 이름 (예: ClientSideSCP)을 제공 합니다.
1. GPO를 편집 하 고 다음 경로를 찾습니다. **컴퓨터 구성** > **기본 설정** > **Windows 설정** > **레지스트리**
1. 레지스트리를 마우스 오른쪽 단추로 클릭 하 고 **새로 만들기** > **레지스트리 항목** 을 선택 합니다.
   1. **일반** 탭에서 다음을 구성 합니다.
      1. 작업: **업데이트**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. 키 경로: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 값 이름: **TenantId**
      1. 값 형식: **REG_SZ**
      1. 값 데이터: Azure AD 인스턴스의 GUID 또는 **디렉터리 id** 입니다 .이 값은 **Azure Portal** > **AZURE ACTIVE DIRECTORY** > **속성** > **디렉터리 id**)에서 찾을 수 있습니다.
   1. **확인**을 클릭합니다.
1. 레지스트리를 마우스 오른쪽 단추로 클릭 하 고 **새로 만들기** > **레지스트리 항목** 을 선택 합니다.
   1. **일반** 탭에서 다음을 구성 합니다.
      1. 작업: **업데이트**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. 키 경로: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. 값 이름: **Tenantname**
      1. 값 형식: **REG_SZ**
      1. 값 데이터: AD FS와 같은 페더레이션된 환경을 사용 하는 경우 확인 된 **도메인 이름** 입니다. 확인 된 **도메인 이름** 또는 onmicrosoft.com 도메인 이름 (예: 관리 되는 환경을 사용 하는 경우 `contoso.onmicrosoft.com`)
   1. **확인**을 클릭합니다.
1. 새로 만든 GPO에 대 한 편집기를 닫습니다.
1. 사용자가 제어 하는 출시 모집단에 속한 도메인 가입 컴퓨터를 포함 하는 원하는 OU에 새로 만든 GPO를 연결 합니다.

### <a name="configure-ad-fs-settings"></a>AD FS 설정 구성

AD FS를 사용 하는 경우 먼저 AD FS 서버에 GPO를 연결 하 여 위에서 설명한 지침을 사용 하 여 클라이언트 쪽 SCP를 구성 해야 합니다. SCP 개체는 장치 개체에 대 한 기관의 원본을 정의 합니다. 온-프레미스 또는 Azure AD 일 수 있습니다. 클라이언트 쪽 SCP가 AD FS에 대해 구성 된 경우 장치 개체의 원본은 Azure AD로 설정 됩니다.

> [!NOTE]
> AD FS 서버에서 클라이언트 쪽 SCP를 구성 하지 않은 경우 장치 id의 원본은 온-프레미스로 간주 됩니다. 그러면 adfs는 ADFS 장치 등록의 특성 "MaximumInactiveDays"에 정의 된 규정 된 기간 후 온-프레미스 디렉터리에서 장치 개체 삭제를 시작 합니다. ADFS 장치 등록 개체는 [합니다. cmdlet](https://docs.microsoft.com/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps)을 사용 하 여 찾을 수 있습니다.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Windows 하위 수준 장치에서 하이브리드 Azure AD 조인에 대 한 제어 된 유효성 검사

Windows 하위 수준 디바이스를 등록하려면 조직에서는 Microsoft Download Center에 제공되는 [비 Windows 10 컴퓨터용 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)을 설치해야 합니다.

 [Microsoft 엔드포인트 Configuration Manager](https://docs.microsoft.com/configmgr/)와 같은 소프트웨어 배포 시스템을 사용 하 여 패키지를 배포할 수 있습니다. 패키지는 quiet 매개 변수를 사용하여 표준 자동 설치 옵션을 지원합니다. 구성 관리자의 현재 분기는 완료된 등록을 추적하는 기능과 같은 이전 버전보다 나은 이점이 추가로 제공됩니다.

설치 관리자는 사용자 컨텍스트에서 실행되는 예약된 작업을 시스템에 만듭니다. 사용자가 Windows에 로그인할 때 이 작업이 트리거됩니다. 이 작업은 Azure AD에서 인증을 받은 후 사용자 자격 증명으로 Azure AD에 디바이스를 자동으로 가입합니다.

장치 등록을 제어 하려면 선택한 Windows 하위 수준 장치 그룹에 Windows Installer 패키지를 배포 해야 합니다.

> [!NOTE]
> SCP가 AD에서 구성 되지 않은 경우 GPO (그룹 정책 개체)를 사용 하 여 도메인에 가입 된 컴퓨터에서 [scp에 대 한 클라이언트 쪽 레지스트리 설정 구성](#configure-client-side-registry-setting-for-scp)에 설명 된 것과 동일한 방법을 따라야 합니다.


모든 것이 예상 대로 작동 하는지 확인 한 후 [Azure AD Connect를 사용 하 여 SCP를 구성](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)하 여 Windows 현재 및 하위 장치를 Azure AD에 자동으로 등록할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[하이브리드 Azure Active Directory 조인 구현 계획](hybrid-azuread-join-plan.md)
