---
title: Azure Active Directory 디바이스 관리 FAQ | Microsoft Docs
description: Azure Active Directory 디바이스 관리 FAQ
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: cebb59d30dd717e54321ab138f6580947a545961
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185837"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory 디바이스 관리 FAQ

## <a name="general-faq"></a>일반 FAQ

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>Q: 최근에 장치를 등록 했습니다. Azure Portal에서 내 사용자 정보에 디바이스가 표시되지 않는 이유는 무엇인가요? 또는 하이브리드 Azure Active Directory (Azure AD)에 연결 된 장치에 대 한 장치 소유자가 N/A로 표시 되는 이유는 무엇 인가요?

**A:** 하이브리드 Azure AD에 가입 된 Windows 10 장치는 **사용자 장치**아래에 표시 되지 않습니다.
Azure Portal에서 **모든 디바이스** 보기를 사용하세요. PowerShell [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet을 사용할 수도 있습니다.

다음 디바이스만 **사용자 디바이스** 아래에 나열됩니다.

- 하이브리드 Azure AD에 조인되지 않은 모든 개인 디바이스 
- 모든 비 Windows 10 또는 Windows Server 2016 디바이스
- 모든 비 Windows 디바이스 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>Q: 클라이언트의 장치 등록 상태를 확인 어떻게 할까요??

**A:** Azure Portal에서 **모든 장치**로 이동 합니다. 디바이스 ID를 사용하여 디바이스를 검색합니다. 조인 유형 열 아래의 값을 확인합니다. 경우에 따라 디바이스가 다시 설정되거나 다시 설치될 수 있습니다. 따라서 디바이스에서도 디바이스 등록 상태를 반드시 확인해야 합니다.

- Windows 10 및 Windows Server 2016 이상 디바이스인 경우 `dsregcmd.exe /status` 명령을 실행합니다.
- 하위 수준 OS 버전인 경우 `%programFiles%\Microsoft Workplace Join\autoworkplace.exe` 명령을 실행합니다.

**A:** 문제 해결 정보는 다음 문서를 참조 하세요.
- [Dsregcmd.exe 명령을 사용 하 여 장치 문제 해결](troubleshoot-device-dsregcmd.md)
- [Windows 10 및 Windows Server 2016 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-current.md)
- [하위 수준 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>Q: Azure Portal의 사용자 정보 아래에 장치 레코드가 표시 됩니다. 그리고 디바이스에서 상태가 등록됨으로 표시됩니다. 조건부 액세스를 사용 하도록 올바르게 설정 되었습니까?

**A:** **DeviceID**로 표시 되는 장치 가입 상태는 Azure AD의 상태와 일치 하 고 조건부 액세스에 대 한 평가 조건을 충족 해야 합니다. 자세한 내용은 [조건부 액세스를 사용 하 여 클라우드 앱 액세스를 위한 관리 되는 장치 필요](../conditional-access/require-managed-devices.md)를 참조 하세요.

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>Q: 사용자가 Windows 10 장치에서 "조직에서 장치를 삭제 했습니다." 또는 "조직에서 장치를 사용 하지 않도록 설정 했습니다." 라는 오류 메시지가 표시 되는 이유는 무엇 인가요?

**A:** Azure AD에 가입 하거나 등록 된 Windows 10 장치에서 사용자는 single sign-on을 사용 하도록 설정 하는 [PRT (주 새로 고침 토큰)](concept-primary-refresh-token.md) 를 발급 합니다. PRT의 유효성은 장치 자체의 유효성을 기반으로 합니다. 장치 자체에서 작업을 시작 하지 않고 Azure AD에서 장치를 삭제 하거나 사용 하지 않도록 설정한 경우 사용자에 게이 메시지가 표시 됩니다. 다음 시나리오 중 하나를 사용 하 여 Azure AD에서 장치를 삭제 하거나 사용 하지 않도록 설정할 수 있습니다. 

- 사용자가 내 앱 포털에서 장치를 사용 하지 않도록 설정 합니다. 
- 관리자 (또는 사용자)가 Azure Portal 또는 PowerShell을 사용 하 여 장치를 삭제 하거나 사용 하지 않도록 설정 합니다.
- 하이브리드 Azure AD 조인만: 관리자가 장치 OU를 동기화 범위에서 제거 하 여 Azure AD에서 장치를 삭제 함
- Azure AD connect를 버전 1.4. xx. x로 업그레이드 합니다. [Azure AD Connect 1.4. x x x x. x 및 장치 disappearance을 이해](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-device-disappearance)합니다.


이러한 작업을 수정 하는 방법에 대 한 자세한 내용은 아래를 참조 하세요.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>Q: Azure Portal에서 또는 Windows PowerShell을 사용 하 여 장치를 사용 하지 않도록 설정 하거나 삭제 했습니다. 하지만 장치의 로컬 상태는 아직 등록 된 상태 라고 표시 합니다. 어떻게 해야 하나요?

**A:** 이 작업은 의도 된 것입니다. 이 경우 장치는 클라우드의 리소스에 액세스할 수 없습니다. 관리자는 부실 하거나 분실 하거나 도난당 한 장치에 대해이 작업을 수행 하 여 무단 액세스를 방지할 수 있습니다. 이 작업을 실수로 수행한 경우 아래 설명 된 대로 장치를 다시 사용 하도록 설정 하거나 다시 등록 해야 합니다.

- Azure AD에서 장치를 사용 하지 않도록 설정한 경우 충분 한 권한이 있는 관리자가 Azure AD 포털에서 장치를 사용 하도록 설정할 수 있습니다.  
  > [!NOTE]
  > Azure AD Connect를 사용 하 여 장치를 동기화 하는 경우 하이브리드 Azure AD 조인 장치는 다음 동기화 주기 중에 자동으로 다시 사용 하도록 설정 됩니다. 따라서 하이브리드 Azure AD 조인 장치를 사용 하지 않도록 설정 해야 하는 경우 온-프레미스 AD에서 사용 하지 않도록 설정 해야 합니다.

 - Azure AD에서 장치를 삭제 하는 경우 장치를 다시 등록 해야 합니다. 다시 등록 하려면 장치에서 수동 작업을 수행 해야 합니다. 장치 상태에 따라 재등록 하는 방법에 대 한 지침은 아래를 참조 하세요. 

      하이브리드 Azure AD에 가입 된 Windows 10 및 Windows Server 2016/2019 장치를 다시 등록 하려면 다음 단계를 수행 합니다.

      1. 관리자 권한으로 명령 프롬프트를 엽니다.
      1. `dsregcmd.exe /debug /leave`를 입력합니다.
      1. 로그아웃했다가 다시 로그인하여 디바이스를 Azure AD에 다시 등록하는 예약된 작업을 트리거합니다. 

      하이브리드 Azure AD에 가입 된 하위 수준 Windows OS 버전의 경우 다음 단계를 수행 합니다.

      1. 관리자 권한으로 명령 프롬프트를 엽니다.
      1. `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`를 입력합니다.
      1. `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`를 입력합니다.

      Azure AD 조인 장치 Windows 10 장치에 대해 다음 단계를 수행 합니다.

      1. 관리자 권한으로 명령 프롬프트를 엽니다.
      1. `dsregcmd /forcerecovery`를 입력 합니다 (참고:이 작업을 수행 하려면 관리자 여야 합니다).
      1. 열리는 대화 상자에서 "로그인"을 클릭 하 여 로그인 프로세스를 진행 합니다.
      1. 로그 아웃 하 고 다시 장치에 로그인 하 여 복구를 완료 합니다.

      Azure AD에서 등록 된 Windows 10 장치에 대해 다음 단계를 수행 합니다.

      1. **설정** > **계정** > **회사 또는 학교 액세스**로 이동 합니다. 
      1. 계정을 선택 하 고 **연결 끊기**를 선택 합니다.
      1. "+ 연결"을 클릭 하 고 로그인 프로세스를 진행 하 여 장치를 다시 등록 합니다.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>Q: Azure Portal에 중복 장치 항목이 표시 되는 이유는 무엇 인가요?

**A:**

- Windows 10 및 Windows Server 2016의 경우 동일한 디바이스에 조인 취소 및 다시 조인을 반복적으로 시도하면 중복된 항목이 발생할 수 있습니다. 
- **회사 또는 학교 계정 추가**를 사용하는 각 Windows 사용자는 디바이스 이름이 같은 새 디바이스 레코드를 만듭니다.
- 온-프레미스 Azure Directory 도메인에 조인된 하위 수준 Windows OS 버전의 경우 자동 등록에 의해 디바이스에 로그인하는 각 도메인 사용자의 디바이스와 이름이 같은 새 디바이스 레코드가 생성됩니다. 
- 초기화되었다가 같은 이름으로 다시 설치되고 다시 조인된 Azure AD 조인 머신은 디바이스 이름이 같은 다른 레코드로 표시됩니다.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>Q: Azure AD의 Windows 10 장치 등록이 FIPS 모드에서 Tpm을 지원 하나요?

**A:** Windows 10 장치 등록은 FIPS 규격 TPM 2.0에 대해서만 지원 되 고 TPM 1.2에는 지원 되지 않습니다. 장치에 FIPS 규격 TPM 1.2가 있는 경우 Azure AD 조인 또는 하이브리드 Azure AD 조인을 계속 하기 전에 사용 하지 않도록 설정 해야 합니다. TPM은 TPM 제조업체에 따라 다르므로 tpm에서 FIPS 모드를 사용 하지 않도록 설정 하는 도구는 제공 하지 않습니다. 하드웨어 OEM에 지원을 문의 하세요. 

---

**Q: 사용자가 Azure Portal에서 사용 하지 않도록 설정 된 장치에서 리소스에 계속 액세스할 수 있는 이유는 무엇 인가요?**

**A:** Azure AD 장치가 사용 안 함으로 표시 된 시간부터 해지를 적용 하는 데 최대 1 시간이 걸립니다.

>[!NOTE] 
>등록된 디바이스의 경우 사용자가 리소스에 액세스할 수 없도록 디바이스를 초기화하는 것이 좋습니다. 자세한 내용은 [디바이스 등록이란?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)을 참조하세요. 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>Q: Azure Portal 등록 된 열 아래에 "보류 중"으로 표시 된 장치가 있는 이유는 무엇 인가요?

**A**: Pending은 장치가 등록 되지 않았음을 나타냅니다. 이 상태는 장치가 온-프레미스 AD에서 Azure AD connect를 사용 하 여 동기화 되었고 장치 등록을 위한 준비가 되었음을 나타냅니다. 이러한 장치는 "하이브리드 Azure AD 조인"으로 설정 된 조인 유형입니다. [하이브리드 Azure Active Directory 조인 구현을 계획 하는 방법](hybrid-azuread-join-plan.md)에 대해 자세히 알아보세요.

>[!NOTE]
>장치가 등록 된 상태를 "보류 중"으로 변경할 수도 있습니다.
>* 처음으로 Azure AD에서 장치를 삭제 하 고 온-프레미스 AD에서 다시 동기화 하는 경우
>* Azure AD Connect의 동기화 범위에서 장치를 제거 하 고 다시 추가 하는 경우
>
>두 경우 모두 이러한 장치에서 장치를 수동으로 다시 등록 해야 합니다. 장치를 이전에 등록 했는지 여부를 확인 하려면 [dsregcmd.exe 명령을 사용 하 여 장치 문제를 해결할](troubleshoot-device-dsregcmd.md)수 있습니다.

---
## <a name="azure-ad-join-faq"></a>Azure AD 조인 FAQ

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>Q: Azure AD 조인 장치를 장치에서 로컬로 가입을 어떻게 할까요? 하 시겠습니까?

**A:** 순수한 Azure AD 조인 장치의 경우 오프 라인 로컬 관리자 계정이 있는지 확인 하거나 하나를 만들어야 합니다. Azure AD 사용자 자격 증명으로 로그인할 수 없습니다. 그런 다음, **설정** > **계정** > **회사 또는 학교 액세스**로 이동합니다. 계정을 선택하고 **연결 끊기**를 선택합니다. 프롬프트를 따르고, 메시지가 표시되면 로컬 관리자 자격 증명을 제공합니다. 디바이스를 다시 부팅하여 조인 취소 프로세스를 완료합니다.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>Q: 내 사용자가 Azure AD에서 삭제 되거나 사용 하지 않도록 설정 된 Azure AD 조인 장치에 로그인 할 수 있나요?

**A:** 예. Windows에는 이전에 로그인한 사용자가 네트워크 연결 없이도 신속하게 데스크톱에 액세스할 수 있게 해주는 캐시된 사용자 이름 및 암호 기능이 있습니다. 

Azure AD에서 디바이스가 삭제 또는 비활성화되어도 Windows 디바이스에서 그 사실을 알지 못합니다. 따라서 이전에 로그인한 사용자는 캐시된 사용자 이름 및 암호를 사용하여 데스크톱에 계속 액세스할 수 있습니다. 그러나 장치를 삭제 하거나 사용 하지 않도록 설정 하면 사용자는 장치 기반 조건부 액세스로 보호 되는 리소스에 액세스할 수 없습니다. 

이전에 로그인하지 않은 사용자는 디바이스에 액세스할 수 없습니다. 이들에게 활성화된 캐시된 사용자 이름 및 암호가 없기 때문입니다. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>Q: 비활성화 되거나 삭제 된 사용자가 Azure AD 조인 장치에 로그인 할 수 있습니다.

**A:** 예, 하지만 제한 된 시간 동안만 가능 합니다. Azure AD에서 사용자가 삭제 또는 비활성화되어도 Windows 디바이스에서 그 사실을 즉시 알지 못합니다. 따라서 이전에 로그인한 사용자는 캐시된 사용자 이름 및 암호를 사용하여 데스크톱에 액세스할 수 있습니다. 

일반적으로 디바이스는 4시간 이내에 사용자 상태를 인식합니다. 그 후 Windows가 데스크톱에 대한 사용자 액세스를 차단합니다. Azure AD에서 사용자가 삭제 또는 비활성화되면 해당 사용자의 모든 토큰이 취소됩니다. 따라서 리소스에 액세스할 수 없습니다. 

이전에 로그인하지 않았으며 삭제 또는 비활성화된 사용자는 디바이스에 액세스할 수 없습니다. 이들에게 활성화된 캐시된 사용자 이름 및 암호가 없기 때문입니다. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>Q: UPN을 변경한 후 사용자에 게 Azure AD 조인 장치에서 문제가 발생 하는 이유는 무엇 인가요?

**A:** 현재는 Azure AD 조인 장치에서 UPN 변경이 완전히 지원 되지 않습니다. 그러므로 이러한 디바이스에서 UPN을 변경하고 나면 Azure AD 인증이 실패합니다. 그러면 사용자의 디바이스에서 SSO 및 조건부 액세스 문제가 발생합니다. 이 경우 문제를 해결하려면 사용자가 새 UPN을 사용하여 "다른 사용자" 타일을 통해 Windows에 로그인해야 합니다. 현재 이 문제를 해결하기 위한 작업이 진행되고 있습니다. 하지만 비즈니스용 Windows Hello를 통해 로그인하는 사용자에게는 이 문제가 발생하지 않습니다. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>Q: 내 사용자는 Azure AD 조인 장치에서 프린터를 검색할 수 없습니다. 이러한 장치에서 인쇄를 사용 하도록 설정 하려면 어떻게 해야 하나요?

**A:** Azure AD 가입 장치에 대 한 프린터를 배포 하려면 [사전 인증을 사용 하 여 Windows Server 하이브리드 클라우드 인쇄 배포](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)를 참조 하세요. 하이브리드 클라우드 인쇄를 배포하려면 온-프레미스 Windows Server가 필요합니다. 현재는 클라우드 기반 인쇄 서비스를 사용할 수 없습니다. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>Q: 원격 Azure AD 조인 장치에 연결할 어떻게 할까요? 있나요?

**A:** [원격 Azure Active Directory 가입 PC에 연결을](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)참조 하세요.

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>Q: 사용자에 게 표시 되는 이유는 *여기에서 가져올 수 없습니다*.

**A:** 특정 장치 상태를 요구 하도록 특정 조건부 액세스 규칙을 구성 했습니까? 디바이스가 조건을 충족하지 않으면 사용자가 차단되고 해당 메시지가 표시됩니다. 조건부 액세스 정책 규칙을 평가 합니다. 이 메시지가 표시되지 않게 하려면 디바이스가 조건을 충족해야 합니다.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>Q: 일부 사용자가 Azure AD 조인 장치에서 Azure Multi-Factor Authentication 프롬프트를 가져오지 않는 이유는 무엇 인가요?

**A:** 사용자가 Multi-Factor Authentication를 사용 하 여 Azure AD에 장치를 연결 하거나 등록할 수 있습니다. 그러면 디바이스 자체가 해당 사용자의 신뢰할 수 있는 두 번째 요소가 됩니다. 동일한 사용자가 디바이스에 로그인하여 애플리케이션에 액세스할 때마다 Azure AD는 디바이스를 두 번째 요소로 간주합니다. 해당 사용자가 추가 Multi-Factor Authentication 프롬프트 없이 원활하게 애플리케이션에 액세스할 수 있게 해줍니다. 

이 동작은 다음과 같습니다.

- Azure AD 조인 디바이스 및 Azure AD 등록 디바이스에는 적용되지만 하이브리드 Azure AD 조인 디바이스에는 적용되지 않습니다.
- 해당 디바이스에 로그인하는 다른 사용자에게 적용되지 않습니다. 따라서 해당 디바이스에 액세스하는 다른 모든 사용자에게 Multi-Factor Authentication이 요구됩니다. 그 후 Multi-Factor Authentication을 요구하는 애플리케이션에 액세스할 수 있습니다.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>Q: Azure AD에 가입 된 장치에 대 한 *사용자 이름 또는 암호가 잘못* 된 이유는 무엇 인가요?

**A:** 이 시나리오의 일반적인 이유는 다음과 같습니다.

- 사용자 자격 증명이 더 이상 유효하지 않습니다.
- 컴퓨터가 Azure Active Directory와 통신할 수 없습니다. 네트워크 연결 문제가 있는지 확인합니다.
- 페더레이션 로그인을 수행하려면 사용하도록 설정되어 있고 액세스 가능한 WS-Trust 엔드포인트를 페더레이션 서버에서 지원해야 합니다. 
- 통과 인증을 사용하도록 설정했습니다. 따라서 로그인할 때 임시 암호를 변경해야 합니다.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>Q:이 표시 되는 이유는 무엇 인가요?  *오류가 발생 했습니다.* 내 PC에 Azure AD 가입을 시도 하는 경우 대화 상자

**A:** 이 오류는 Intune을 사용 하 여 Azure Active Directory 등록을 설정할 때 발생 합니다. Azure AD 조인을 시도한 사용자에게 올바른 Intune 라이선스가 할당되어야 합니다. 자세한 내용은 [Windows 디바이스에 대한 등록 설정](https://docs.microsoft.com/intune/windows-enroll)을 참조하세요.  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>Q: 오류 정보를 받지 못했지만 PC에 대 한 Azure AD 조인 시도에 실패 하는 이유는 무엇 인가요?

**A:** 로컬 기본 제공 관리자 계정을 사용 하 여 장치에 로그인 했을 수 있습니다. Azure Active Directory 조인을 사용하여 설치를 완료하기 전에 다른 로컬 계정을 만드세요. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q:What는 Windows 10 장치에 있는 MS 조직-P2P 액세스 인증서 인가요?

**A:** Azure ad는 azure ad 조인 및 하이브리드 Azure AD 조인 장치 둘 다에 대해 Azure AD에서 발급 합니다. 이러한 인증서는 원격 데스크톱 시나리오에 대한 동일한 테넌트에 있는 디바이스 간 트러스트를 설정하는 데 사용됩니다. 한 인증서는 디바이스에 발급되고 다른 인증서는 사용자에게 발급됩니다. 디바이스 인증서는 `Local Computer\Personal\Certificates`에 있으며 하루 동안 유효합니다. 디바이스가 Azure AD에서도 활성 상태이면 (새 인증서를 발급하여) 이 인증서가 갱신됩니다. 사용자 인증서는 `Current User\Personal\Certificates`에 있으며 마찬가지로 하루 동안 유효하지만, 사용자가 다른 Azure AD 조인 디바이스에 대한 원격 데스크톱 세션을 시도할 때 주문형으로 발급됩니다. 만료 시 갱신되지 않습니다. 두 인증서는 `Local Computer\AAD Token Issuer\Certificates`에 있는 MS-Organization-P2P-Access 인증서를 사용하여 발급됩니다. 이 인증서는 디바이스를 등록할 때 Azure AD에서 발급합니다. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why Windows 10 장치에서 실행 되는 만료 된 인증서가 여러 개 표시 되는 경우 삭제 하려면 어떻게 해야 하나요?

**A:** Windows 10 버전 1709에서 문제가 발생 하 여 암호화 문제로 인해 만료 된 MS 조직-P2P 액세스 인증서가 컴퓨터 저장소에 계속 남아 있습니다. 많은 수의 만료 된 인증서를 처리할 수 없는 VPN 클라이언트 (예: Cisco AnyConnect)를 사용 하는 경우 사용자에 게 네트워크 연결 문제가 발생할 수 있습니다. 이 문제는 만료된 MS-Organization-P2P-Access 인증서를 자동으로 삭제하는 방법을 사용하여 Windows 10 1803 릴리스에서 해결되었습니다. 디바이스를 Windows 10 1803으로 업데이트하여 이 문제를 해결할 수 있습니다. 업데이트할 수 없는 경우 악영향 없이 이러한 인증서를 삭제할 수 있습니다.  

---

## <a name="hybrid-azure-ad-join-faq"></a>하이브리드 Azure AD 조인 FAQ

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>Q: 하이브리드 Azure AD 조인 장치를 장치에서 로컬로 가입 해제 어떻게 할까요? 있나요?

**A:** 하이브리드 Azure AD 가입 장치의 경우 자동 등록을 해제 해야 합니다. 그러면 예약된 작업에서 디바이스를 다시 등록하지 않습니다. 다음으로, 관리자 권한으로 명령 프롬프트를 열고 `dsregcmd.exe /debug /leave`를 입력합니다. 또는 여러 디바이스에서 이 명령을 스크립트로 실행하여 대량으로 조인 취소합니다.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>Q: 하이브리드 Azure AD 조인 실패를 진단 하기 위한 문제 해결 정보는 어디서 찾을 수 있나요?

**A:** 문제 해결 정보는 다음 문서를 참조 하세요.

- [Windows 10 및 Windows Server 2016 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-current.md)
- [하위 수준 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>Q: Azure AD 장치 목록에 Windows 10 하이브리드 Azure AD 조인 장치에 대 한 중복 Azure AD 등록 레코드가 표시 되는 이유는 무엇 인가요?

**A:** 사용자가 도메인에 가입 된 장치의 앱에 계정을 추가 하는 경우 **Windows에 계정 추가** 메시지가 표시 될 수 있습니다. 사용자가 프롬프트에서 **예**를 입력하면 디바이스가 Azure AD에 등록됩니다. 신뢰 유형은 Azure AD 등록으로 표시됩니다. 조직에서 하이브리드 Azure AD 조인을 사용하도록 설정하면 디바이스도 하이브리드 Azure AD에 조인됩니다. 동일한 디바이스에 대한 두 가지 디바이스 상태가 표시됩니다. 

하이브리드 Azure AD 조인이 Azure AD 등록 상태보다 우선합니다. 따라서 장치는 모든 인증 및 조건부 액세스 평가를 위해 하이브리드 Azure AD에 조인 된 것으로 간주 됩니다. Azure AD 포털에서 Azure AD 등록 디바이스 레코드를 삭제해도 무방합니다. [Windows 10 머신에서 이 이중 상태를 피하는 방법 또는 정리하는 방법](hybrid-azuread-join-plan.md#review-things-you-should-know)을 알아보세요. 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>Q: UPN을 변경한 후 사용자에 게 Windows 10 하이브리드 Azure AD 조인 장치에서 문제가 발생 하는 이유는 무엇 인가요?

**A:** 현재 UPN 변경은 하이브리드 Azure AD 조인 장치에서 완전히 지원 되지 않습니다. 사용자는 디바이스에 로그인하여 온-프레미스 애플리케이션에 액세스할 수는 있지만 UPN을 변경하고 나면 Azure AD 인증이 실패합니다. 그러면 사용자의 디바이스에서 SSO 및 조건부 액세스 문제가 발생합니다. 이번에는 Azure AD에서 장치를 가입 해제 (승격 된 권한으로 "dsregcmd/leave" 실행) 하 고 다시 참가 (자동으로 발생) 하 여 문제를 해결 해야 합니다. 현재 이 문제를 해결하기 위한 작업이 진행되고 있습니다. 하지만 비즈니스용 Windows Hello를 통해 로그인하는 사용자에게는 이 문제가 발생하지 않습니다. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>Q: 클라우드 리소스에 대 한 액세스를 얻으려면 Windows 10 하이브리드 Azure AD 조인 장치에서 도메인 컨트롤러에 대 한 시야를 사용 해야 하나요?

**A:** 아니요. 사용자의 암호가 변경 된 경우는 제외 됩니다. Windows 10 하이브리드 Azure AD 조인이 완료 되 고 사용자가 한 번 이상 로그인 한 후에는 장치에서 클라우드 리소스에 액세스 하기 위해 도메인 컨트롤러에 대 한 시야를 요구 하지 않습니다. Windows 10은 암호를 변경 하는 경우를 제외 하 고 인터넷 연결을 사용 하 여 어디서 나 Azure AD 응용 프로그램에 대 한 Single Sign-On를 가져올 수 있습니다. 비즈니스용 Windows Hello를 사용 하 여 로그인 하는 사용자는 도메인 컨트롤러에 대 한 시야를 갖지 않는 경우에도 암호 변경 후에도 Azure AD 응용 프로그램에 대 한 Single Sign-On를 계속 받습니다. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>Q: 사용자가 암호를 변경 하 고 회사 네트워크 외부의 Windows 10 하이브리드 Azure AD 조인 장치에 로그인 하려고 하면 어떻게 되나요?

**A:** 암호가 회사 네트워크 외부에서 변경 된 경우 (예: Azure AD SSPR를 사용 하 여), 새 암호를 사용 하 여 로그인 하는 사용자는 실패 합니다. 하이브리드 Azure AD 조인 장치의 경우 온-프레미스 Active Directory 기본 인증 기관입니다. 장치가 도메인 컨트롤러에 대 한 시야를 갖지 않는 경우 새 암호의 유효성을 검사할 수 없습니다. 따라서 사용자는 새 암호를 사용 하 여 장치에 로그인 하기 전에 도메인 컨트롤러 (VPN 또는 회사 네트워크에 있음)에 대 한 연결을 설정 해야 합니다. 그렇지 않으면 Windows의 캐시 된 로그인 기능 때문에 이전 암호를 사용 하 여 로그인 할 수 있습니다. 그러나 토큰 요청 중에는 Azure AD에서 이전 암호를 무효화 하므로 Single Sign-On을 방지 하 고 장치 기반 조건부 액세스 정책을 실패 합니다. 비즈니스용 Windows Hello를 사용 하는 경우에는이 문제가 발생 하지 않습니다. 

---

## <a name="azure-ad-register-faq"></a>Azure AD 등록 FAQ

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>Q: 로컬에서 장치에 대 한 Azure AD 등록 상태를 제거할 어떻게 할까요? 있나요?

**A:** 
- Windows 10 Azure AD 등록 장치에 대해 **설정** > **계정** > **회사 또는 학교에 액세스**로 이동 합니다. 계정을 선택하고 **연결 끊기**를 선택합니다. 장치 등록은 Windows 10의 사용자 프로필 당입니다.
- IOS 및 Android의 경우 Microsoft Authenticator 응용 프로그램 **설정** > **장치 등록** 을 사용 하 고 **장치 등록 취소**를 선택할 수 있습니다.
- MacOS의 경우 Microsoft Intune 회사 포털 응용 프로그램을 사용 하 여 관리에서 장치 등록을 취소 하 고 등록을 제거할 수 있습니다. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>Q: 회사 Windows 10 장치에 추가 작업 계정 (Azure AD 등록)을 추가 하는 것을 차단 하려면 어떻게 해야 하나요?

**A:** 사용자가 회사 도메인 가입, Azure AD 조인 또는 하이브리드 Azure AD 조인 Windows 10 장치에 추가 작업 계정을 추가 하지 못하도록 차단 하려면 다음 레지스트리를 사용 하도록 설정 합니다. 이 정책을 사용 하 여 도메인에 가입 된 컴퓨터가 실수로 동일한 사용자 계정으로 Azure AD를 등록 하지 못하도록 차단할 수도 있습니다. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>Q: Android 또는 iOS BYOD 장치를 등록할 수 있나요?

**A:** 예, 하지만 Azure 장치 등록 서비스 및 하이브리드 고객만 사용 합니다. AD FS(Active Directory Federation Services)의 온-프레미스 디바이스 등록 서비스에는 지원되지 않습니다.

---
### <a name="q-how-can-i-register-a-macos-device"></a>Q: macOS 장치를 등록 하려면 어떻게 해야 하나요?

**A:** 다음 단계를 수행 합니다.

1.  [규정 준수 정책 만들기](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
1.  [MacOS 장치에 대 한 조건부 액세스 정책 정의](../active-directory-conditional-access-azure-portal.md) 

**설명:**

- 조건부 액세스 정책에 포함 된 사용자에 게 리소스에 액세스 하려면 [지원 되는 macOS 버전이](../conditional-access/concept-conditional-access-conditions.md) 필요 합니다. 
- 첫 번째 액세스를 시도하는 동안 사용자에게는 회사 포털을 사용하여 디바이스를 등록하라는 메시지가 표시됩니다.

---
## <a name="next-steps"></a>다음 단계

- [Azure AD 등록 디바이스](concept-azure-ad-register.md)에 대한 자세한 정보
- [Azure AD 조인 디바이스](concept-azure-ad-join.md)에 대한 자세한 정보
- [하이브리드 Azure AD 조인 디바이스](concept-azure-ad-join-hybrid.md)에 대한 자세한 정보
