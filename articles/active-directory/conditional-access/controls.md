---
title: Azure Active Directory 조건부 액세스의 액세스 제어
description: 조건부 액세스 Azure Active Directory의 액세스 제어가 작동 하는 방식을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 12/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad8894078a15bf37a5383cdff3721f4bf7be910
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186221"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Azure Active Directory 조건부 액세스의 액세스 제어 정의

[Azure Active Directory (AZURE AD) 조건부 액세스](../active-directory-conditional-access-azure-portal.md)를 사용 하 여 권한 있는 사용자가 클라우드 앱에 액세스 하는 방법을 제어할 수 있습니다. 조건부 액세스 정책에서 정책을 트리거하는 이유에 대 한 응답 ("do this")을 정의 합니다 ("이 경우").

![제어](./media/controls/10.png)

조건부 액세스의 컨텍스트에서

- “**When this happens**”는 **조건**이라고 합니다.
- “**Then do this**”는 **액세스 제어**라고 합니다.

조건 문과 컨트롤의 조합은 조건부 액세스 정책을 나타냅니다.

![제어](./media/controls/61.png)

각 컨트롤은 로그인하는 사용자 또는 시스템에 의해 충족되어야 하는 요구 사항 또는 사용자가 로그인한 후 수행할 수 있는 작업에 대한 제한입니다.

두 가지 형식의 컨트롤이 있습니다.

- **권한 부여 컨트롤** - 액세스를 제어하기 위해
- **세션 컨트롤** - 세션 내에서 액세스를 제한하기 위해

이 항목에서는 Azure AD 조건부 액세스에서 사용할 수 있는 다양 한 컨트롤에 대해 설명 합니다. 

## <a name="grant-controls"></a>권한 부여 컨트롤

권한 부여 컨트롤을 사용하여 액세스를 완전히 차단하거나 원하는 컨트롤을 선택하여 추가 요구 사항으로 액세스를 허용할 수 있습니다. 여러 컨트롤의 경우 다음이 필요할 수 있습니다.

- 충족되도록 선택한 모든 컨트롤(*AND*)
- 충족되도록 선택한 하나의 컨트롤(*OR*)

![제어](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

이 컨트롤을 사용하여 지정된 클라우드 앱에 액세스하기 위해 다단계 인증을 요구할 수 있습니다. 이 컨트롤은 다음 다단계 공급자를 지원합니다.

- Azure Multi-Factor Authentication
- AD FS(Active Directory Federation Service)와 결합된 온-프레미스 다단계 인증 공급자.

다단계 인증을 사용하면 유효한 사용자의 주 자격 증명에 액세스 할 수 있는 권한이 없는 사용자가 리소스에 액세스하지 못하도록 방지할 수 있습니다.

### <a name="compliant-device"></a>규정 준수 디바이스

장치 기반 조건부 액세스 정책을 구성할 수 있습니다. 장치 기반 조건부 액세스 정책의 목표는 관리 되는 [장치](require-managed-devices.md)에서 선택한 클라우드 앱에 대 한 액세스 권한만 부여 하는 것입니다. 디바이스를 규정 준수 상태로 표시하도록 요구하는 것은 관리 디바이스에 대한 액세스 권한을 제한해야 하는 한 가지 옵션입니다. 디바이스는 Intune(모든 디바이스 OS용) 또는 Windows 10 디바이스용 타사 MDM 시스템에서 규정 준수 상태로 표시할 수 있습니다. Windows 10 이외의 디바이스 OS 유형에 대한 타사 MDM 시스템은 지원되지 않습니다. 

먼저 디바이스를 Azure AD에 등록해야 규정 준수 상태로 표시할 수 있습니다. 디바이스를 등록하려면 다음 세 가지 옵션이 있습니다. 

- Azure AD 등록 디바이스
- Azure AD 조인 디바이스  
- 하이브리드 Azure AD 가입 디바이스

이 세 가지 옵션은 [장치 Id 란?](../devices/overview.md) 문서에서 설명 합니다.

자세한 내용은 [조건부 액세스를 사용 하 여 cloud app access에서 관리 되는 장치를 요구 하는 방법](require-managed-devices.md)을 참조 하세요.

### <a name="hybrid-azure-ad-joined-device"></a>하이브리드 Azure AD 조인 디바이스

하이브리드 Azure AD 조인 장치를 요구 하는 것은 장치 기반 조건부 액세스 정책을 구성 해야 하는 또 다른 옵션입니다. 즉, Windows 데스크톱, 랩톱 및 엔터프라이즈 태블릿을 온-프레미스 Active Directory에 조인해야 합니다. 이 옵션을 선택 하면 조건부 액세스 정책에서 온-프레미스 Active Directory 및 Azure Active Directory에 가입 된 장치에 대 한 액세스 시도에 대 한 액세스를 부여 합니다. Mac 장치는 하이브리드 Azure AD 조인을 지원 하지 않습니다.

자세한 내용은 [Azure Active Directory 장치 기반 조건부 액세스 정책 설정](require-managed-devices.md)을 참조 하세요.

### <a name="approved-client-app"></a>승인된 클라이언트 앱

직원이 개인 및 회사 작업 모두에 모바일 디바이스를 사용하므로 관리되지 않는 경우에도 디바이스를 사용하여 액세스되는 회사 데이터를 보호하는 기능이 있어야 합니다.
[Intune 앱 보호 정책](https://docs.microsoft.com/intune/app-protection-policy)을 사용하여 모든 MDM(모바일 디바이스 관리) 솔루션과는 별도로 회사의 데이터를 보호할 수 있습니다.

승인된 클라이언트 앱을 사용하여 [Intune 앱 보호 정책](https://docs.microsoft.com/intune/app-protection-policy)을 지원하도록 클라우드 앱에 액세스를 시도하는 클라이언트 앱을 요구할 수 있습니다. 예를 들어 Exchange Online에 대한 액세스를 Outlook 앱으로 제한할 수 있습니다. 승인 된 클라이언트 앱을 요구 하는 조건부 액세스 정책을 [앱 기반 조건부 액세스 정책이](app-based-conditional-access.md)라고도 합니다. 지원되는 승인된 클라이언트 앱의 목록은 [승인된 클라이언트 앱 요구 사항](concept-conditional-access-grant.md#require-approved-client-app)을 참조하세요.

### <a name="app-protection-policy-preview"></a>앱 보호 정책 (미리 보기)

직원이 개인 및 회사 작업 모두에 모바일 디바이스를 사용하므로 관리되지 않는 경우에도 디바이스를 사용하여 액세스되는 회사 데이터를 보호하는 기능이 있어야 합니다.
[Intune 앱 보호 정책](https://docs.microsoft.com/intune/app-protection-policy)을 사용하여 모든 MDM(모바일 디바이스 관리) 솔루션과는 별도로 회사의 데이터를 보호할 수 있습니다.

앱 보호 정책을 사용 하 여 Azure AD가 [Intune 앱 보호 정책을](https://docs.microsoft.com/intune/app-protection-policy)수신 했음을 보고 한 클라이언트 응용 프로그램에 대 한 액세스를 제한할 수 있습니다. 예를 들어 Exchange Online에 대 한 액세스를 Intune 앱 보호 정책이 있는 Outlook 앱으로 제한할 수 있습니다. 앱 보호 정책을 필요로 하는 조건부 액세스 정책을 [앱 보호 기반 조건부 액세스 정책](concept-conditional-access-session.md#application-enforced-restrictions)이 라고도 합니다. 

응용 프로그램을 정책으로 보호 된 상태로 표시 하려면 먼저 장치를 Azure AD에 등록 해야 합니다.

지원 되는 정책으로 보호 된 클라이언트 앱의 목록은 [앱 보호 정책 요구 사항](concept-conditional-access-session.md#application-enforced-restrictions)을 참조 하세요.

### <a name="terms-of-use"></a>사용 약관

테넌트의 사용자가 리소스에 대한 액세스 권한을 부여 받기 전에 사용 조건에 동의하도록 요구할 수 있습니다. 관리자는 PDF 문서를 업로드하여 사용 조건을 구성하고 사용자 지정할 수 있습니다. 사용자가 이 제어 범위에 해당하는 경우 사용 조건에 동의한 경우에만 애플리케이션에 대한 액세스가 부여됩니다.

## <a name="custom-controls-preview"></a>사용자 지정 컨트롤(미리 보기)

사용자 지정 컨트롤은 Azure Active Directory Premium P1 버전의 기능입니다. 사용자 지정 컨트롤을 사용하는 경우 Azure Active Directory 외부의 추가 요구 사항을 충족시키기 위해 사용자를 호환 가능한 서비스로 리디렉션합니다. 이 제어를 충족시키기 위해 사용자의 브라우저는 외부 서비스로 리디렉션되고 필요한 인증 또는 유효성 검사 활동을 수행한 다음 Azure Active Directory로 다시 리디렉션됩니다. Azure Active Directory는 응답을 확인 하 고 사용자가 성공적으로 인증 되거나 유효성이 확인 되 면 사용자는 조건부 액세스 흐름을 계속 진행 합니다.

이러한 컨트롤을 사용 하면 조건부 액세스 제어로 특정 외부 또는 사용자 지정 서비스를 사용할 수 있으며, 일반적으로 조건부 액세스의 기능을 확장할 수 있습니다.

현재 호환 서비스를 제공하는 공급자는 다음과 같습니다.

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

이러한 서비스에 대한 자세한 내용은 공급자에게 직접 문의하세요.

### <a name="creating-custom-controls"></a>사용자 지정 컨트롤 만들기

사용자 지정 컨트롤을 만들려면 먼저 사용할 공급자에게 문의해야 합니다. 각 비 Microsoft 공급자에는 등록, 구독 또는 서비스의 일부가 될 수 있는 고유한 프로세스 및 요구 사항이 있으며 조건부 액세스와 통합 하려는 경우를 표시 합니다. 이 시점에서 공급자는 JSON 형식의 데이터 블록을 제공합니다. 이 데이터를 사용 하면 공급자 및 조건부 액세스가 테 넌 트에 대해 함께 작동 하 고, 새 컨트롤이 만들어지고, 사용자가 공급자를 사용 하 여 확인을 성공적으로 수행 했는지 여부를 조건부 액세스에서 확인할 수 있는 방법을 정의 합니다.

사용자 지정 컨트롤은 multi-factor authentication을 요구 하는 Id 보호의 자동화와 함께 사용 하거나 PIM (권한 있는 Id 관리자)에서 역할을 승격 시킬 수 없습니다.

JSON 데이터를 복사한 다음 관련 텍스트 상자에 붙여넣습니다. 변경 사항을 명시적으로 이해하지 못한 경우 JSON을 변경하지 마세요. 변경하면 공급자와 Microsoft 간의 연결이 끊어져 잠재적으로 사용자가 사용자 계정 밖에서 잠길 수 있습니다.

사용자 지정 컨트롤을 만드는 옵션은 **조건부 액세스** 페이지의 **관리** 섹션에 있습니다.

![제어](./media/controls/82.png)

**새 사용자 지정 컨트롤**을 클릭하면 컨트롤의 JSON 데이터에 대한 텍스트 상자가 있는 블레이드가 열립니다.  

![제어](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>사용자 지정 컨트롤 삭제

사용자 지정 컨트롤을 삭제 하려면 먼저 조건부 액세스 정책에서 사용 되 고 있지 않은지 확인 해야 합니다. 작업이 완료되면 다음을 수행합니다.

1. 사용자 지정 컨트롤 목록으로 이동합니다.
1. …를 클릭합니다.  
1. **삭제**를 선택합니다.

### <a name="editing-custom-controls"></a>사용자 지정 컨트롤 편집

사용자 지정 컨트롤을 편집하려면 현재 컨트롤을 삭제하고 업데이트된 정보로 새 컨트롤을 만들어야 합니다.

## <a name="session-controls"></a>세션 컨트롤

세션 컨트롤은 클라우드 앱 내에서 제한된 환경을 활성화합니다. 세션 컨트롤은 클라우드 앱에서 적용되고 Azure AD가 앱에 제공한 세션에 대한 추가 정보에 의존합니다.

![제어](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>앱에서 적용된 제한 사항 사용

이 컨트롤을 사용하여 Azure AD가 디바이스 정보를 선택한 클라우드 앱에 전달하도록 할 수 있습니다. 디바이스 정보를 사용하면 클라우드 앱이 규정 준수 또는 도메인 조인 디바이스에서 연결이 초기화되는지 여부를 알 수 있습니다. 이 컨트롤은 선택한 클라우드 앱으로 SharePoint Online 및 Exchange Online만 지원합니다. 선택하면 클라우드 앱에서는 디바이스 정보를 사용하여 사용자에게 디바이스 상태에 따라 제한된 환경이나 전체 환경을 제공합니다.

자세한 내용은 다음을 참조하세요.

- [SharePoint Online을 사용하여 제한된 액세스를 사용하도록 설정](https://aka.ms/spolimitedaccessdocs)
- [Exchange Online을 사용하여 제한된 액세스를 사용하도록 설정](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory 조건부 액세스를 사용하는 특정 앱에 MFA 요구](app-based-mfa.md)를 참조하세요.
- 사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 Azure Active Directory의 [조건부 액세스 모범 사례](best-practices.md)를 참조하세요.
