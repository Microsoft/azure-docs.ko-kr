---
title: Azure Active Directory 통과 인증 보안 심층 분석 | Microsoft Docs
description: 이 문서에서는 Azure AD(Active Directory) 통과 인증이 온-프레미스 계정을 보호하는 방법에 대해 설명합니다.
services: active-directory
keywords: Azure AD Connect 통과 인증, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ddce8d4d7ca1f03c0a57d0f0c8c41ac122973e0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185552"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory 통과 인증 보안 심층 분석

이 문서에서는 Azure Active Directory (Azure AD) 통과 인증이 작동하는 방식에 대해 자세히 설명합니다. 기능의 보안 측면에 대해 보다 중점적으로 설명합니다. 이 문서는 보안 및 IT 관리자, 최고 준수 및 보안 책임자, 그 밖에 중소기업 또는 대기업의 IT 보안 및 규정 준수를 담당하는 IT 전문가들이 관심을 가질 내용입니다.

이 문서에서 다루는 내용은 다음과 같습니다.
- 인증 에이전트 설치 및 등록 방법에 대한 자세한 기술 정보.
- 사용자 로그인 시 암호의 암호화에 대한 자세한 기술 정보.
- 온-프레미스 인증 에이전트와 Azure AD 간의 채널 보안.
- 인증 에이전트가 운영상 안전하게 유지되는 방법에 대한 자세한 기술 정보.
- 기타 보안 관련 항목

## <a name="key-security-capabilities"></a>주요 보안 기능

다음은 이 기능의 주요 보안 측면입니다.
- 테넌트 간의 로그인 요청을 격리하는 안전한 멀티 테넌트 아키텍처를 기반으로 합니다.
- 온-프레미스 암호가 어떤 형태로든 클라우드에 저장되지 않습니다.
- 온-프레미스 인증 에이전트는 네트워크 내에서만 아웃바운드 연결을 설정한 암호 유효성 검사 요청만 수신 대기하고 응답합니다. DMZ(경계 네트워크)에 이러한 인증 에이전트를 설치해야 할 필요는 없습니다. 모범 사례로, 인증 에이전트를 실행하는 모든 서버를 계층 0 시스템으로 처리합니다([참조](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) 항목 참조).
- 인증 에이전트에서 Azure AD로의 아웃바운드 통신에는 표준 포트(80 및 443)만 사용됩니다. 방화벽에서 인바운드 포트를 열지 않아도 됩니다. 
  - 인증된 모든 아웃바운드 통신에는 포트 443이 사용됩니다.
  - 포트 80은 통과 인증에서 해지된 인증서가 사용되지 않도록 CRL(인증서 해지 목록)을 다운로드하는 데만 사용됩니다.
  - 네트워크 요구 사항의 전체 목록은 [Azure Active Directory 통과 인증: 빠른](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)시작을 참조 하세요.
- 사용자가 로그인 시 입력하는 암호는 먼저 클라우드에서 암호화된 뒤에 Active Directory에 대한 유효성 검사를 위해 온-프레미스 인증 에이전트에서 수신됩니다.
- Azure AD 및 온-프레미스 인증 에이전트 간의 HTTPS 채널은 상호 인증으로 보호됩니다.
- MFA(Multi-Factor Authentication)를 포함하는 [Azure AD 조건부 액세스 정책](../active-directory-conditional-access-azure-portal.md)을 사용하여 원활하게 작동하고, [레거시 인증을 차단](../conditional-access/concept-conditional-access-conditions.md)하고, [무차별 암호 대입 공격을 필터링](../authentication/howto-password-smart-lockout.md)하여 사용자 계정을 보호합니다.

## <a name="components-involved"></a>관련 구성 요소

Azure AD의 운영, 서비스 및 데이터 보안에 대한 일반적인 정보는 [보안 센터](https://azure.microsoft.com/support/trust-center/)를 참조하세요. 사용자 로그인에 통과 인증이 사용된 경우 다음과 같은 구성 요소가 사용됩니다.
- **Azure AD STS**: 로그인 요청을 처리하고 필요에 따라 사용자의 브라우저, 클라이언트 또는 서비스에 보안 토큰을 발행하는 상태 비저장 STS(보안 토큰 서비스)입니다.
- **Azure Service Bus**: 온-프레미스 솔루션을 클라우드에 연결할 수 있는 릴레이된 통신과 엔터프라이즈 메시지를 사용하여 클라우드 사용 통신을 제공합니다.
- **Azure AD Connect 인증 에이전트**: 암호 유효성 검사 요청을 수신 대기하고 응답하는 온-프레미스 구성 요소입니다.
- **Azure SQL Database**: 테넌트의 인증 에이전트에 대한 정보(메타데이터 및 암호화 키 포함)가 저장됩니다.
- **Active Directory**: 사용자 계정 및 암호가 저장되는 온-프레미스 Active Directory입니다.

## <a name="installation-and-registration-of-the-authentication-agents"></a>인증 에이전트의 설치 및 등록

인증 에이전트는 다음과 같은 경우에 Azure AD에 설치 및 등록됩니다.
   - [Azure AD Connect를 통해 통과 인증을 활성화하는 경우](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [로그인 요청의 고가용성을 보장하기 위해 더 많은 인증 에이전트를 추가하는 경우](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
인증 에이전트 작업은 세 가지 주요 단계로 구성되어 있습니다.

1. 인증 에이전트 설치
2. 인증 에이전트 등록
3. 인증 에이전트 초기화

이어지는 섹션에서는 각 단계에 대해 자세히 설명합니다.

### <a name="authentication-agent-installation"></a>인증 에이전트 설치

오직 전역 관리자만 Azure AD Connect 또는 독립 실행형을 사용하여 온-프레미스 서버에 인증 에이전트를 설치할 수 있습니다. 설치하면 **제어판** > **프로그램** > **프로그램 및 기능** 목록에 다음과 같은 두 가지 항목이 추가됩니다.
- 인증 에이전트 애플리케이션 자체. 인증 에이전트 애플리케이션은 [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) 권한으로 실행됩니다.
- 인증 에이전트를 자동으로 업데이트하는 업데이트 애플리케이션. 업데이트 애플리케이션은 [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) 권한으로 실행됩니다.

### <a name="authentication-agent-registration"></a>인증 에이전트 등록

인증 에이전트를 설치한 후 Azure AD에 자체 등록해야 합니다. Azure AD는 각 인증 에이전트에 Azure AD와의 보안 통신에 사용할 고유한 디지털 ID 인증서를 할당합니다.

등록 절차가 완료되면 인증 에이전트가 테넌트와 바인딩됩니다. 따라서 Azure AD는 이 특정 인증 에이전트가 해당 테넌트의 암호 유효성 검사 요청을 처리할 권한이 있는 유일한 에이전트임을 알게 됩니다. 이 절차를 등록할 각 새 인증 에이전트에 대해 반복합니다.

인증 에이전트는 다음 단계를 사용하여 자신을 Azure AD에 등록합니다.

![에이전트 등록](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. 먼저 전역 관리자가 본인의 자격 증명을 사용하여 Azure AD에 로그인하도록 요청합니다. 로그인 시 인증 에이전트는 전역 관리자를 대신하여 사용할 액세스 토큰을 획득합니다.
2. 그런 다음, 공개 키와 프라이빗 키 쌍을 생성합니다.
    - 키 쌍은 표준 RSA 2048비트 암호화를 통해 생성됩니다.
    - 프라이빗 키는 인증 에이전트가 있는 온-프레미스 서버에 유지됩니다.
3. 인증 에이전트는 HTTPS를 통해 Azure AD에 “등록” 요청을 생성하고 요청에는 다음 구성 요소가 포함됩니다.
    - 1단계에서 획득한 액세스 토큰.
    - 2단계에서 생성된 공개 키.
    - 인증서 서명 요청(CSR 또는 인증서 요청). 이 요청은 디지털 ID 인증서에 적용됩니다. 이때 Azure AD가 인증 기관(CA)으로 사용됩니다.
4. Azure AD는 등록 요청에 포함된 액세스 토큰의 유효성을 검사하고 해당 요청이 전역 관리자로부터 왔는지 확인합니다.
5. Azure AD가 디지털 ID 인증서에 서명하고 서명된 인증서를 인증 에이전트로 돌려줍니다.
    - 인증서 서명에는 Azure AD의 루트 CA가 사용됩니다. 

      > [!NOTE]
      > 이 CA는 Windows의 신뢰할 수 있는 루트 인증 기관 저장소에 _있지 않습니다_.
    - 이 CA는 통과 인증 기능에만 사용됩니다. 이 CA는 인증 에이전트 등록 시 CSR에 서명하는 데만 사용됩니다.
    -  다른 Azure AD 서비스는 이 CA를 사용하지 않습니다.
    - 인증서의 제목(고유 이름 또는 DN)은 테넌트 ID로 설정됩니다. DN은 테넌트를 고유하게 식별하는 GUID입니다. DN은 인증서가 해당 테넌트에만 사용되도록 범위를 지정합니다.
6. Azure AD는 인증 에이전트의 공개 키를 Azure AD만 액세스할 수 있는 Azure SQL 데이터베이스에 저장합니다.
7. 5단계에서 발급된 인증서가 Windows 인증서 저장소([CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) 위치)의 온-프레미스 서버에 저장됩니다. 저장소는 인증 에이전트와 업데이트 애플리케이션이 모두 사용합니다.

### <a name="authentication-agent-initialization"></a>인증 에이전트 초기화

인증 에이전트가 등록 후 처음으로 또는 서버가 재시작 이후 시작되면 Azure AD 서비스와 안전하게 통신하고 암호 유효성 검사 요청을 받을 방법이 필요합니다.

![에이전트 초기화](./media/how-to-connect-pta-security-deep-dive/pta2.png)

인증 에이전트는 다음과 같이 초기화됩니다.

1. 인증 에이전트가 Azure AD로 아웃바운드 부트스트랩 요청을 전송합니다. 
    - 이 요청은 포트 443을 통해 상호 인증된 HTTPS 채널을 통해 이루어집니다. 이 요청은 인증 에이전트 등록 시 발급된 인증서를 사용합니다.
2. Azure AD는 해당 테넌트의 고유하며 테넌트 ID로 식별되는 Azure Service Bus 큐에 선택키를 제공함으로써 요청에 응답합니다.
3. 인증 에이전트는 큐에 영구적 아웃바운드 HTTPS 연결(포트 443을 통해)을 설정합니다. 
    - 이제 인증 에이전트가 암호 유효성 검사 요청을 가져오고 처리할 준비가 되었습니다.

테넌트에 여러 인증 에이전트가 등록된 경우 초기화 절차를 통해 각 인증 에이전트가 하나의 동일한 Azure Service Bus 큐에 연결되게 됩니다. 

## <a name="process-sign-in-requests"></a>로그인 요청 처리 

아래 다이어그램에서는 통과 인증이 사용자 로그인 요청을 처리하는 방식을 보여줍니다.

![로그인 처리](./media/how-to-connect-pta-security-deep-dive/pta3.png)

통과 인증에서 사용자 로그인 요청을 다음과 같이 처리합니다. 

1. 사용자가 애플리케이션(예: [Outlook 웹앱](https://outlook.office365.com/owa))에 대한 액세스를 시도합니다.
2. 사용자가 아직 로그인하지 않은 경우 해당 애플리케이션은 브라우저를 Azure AD 로그인 페이지로 리디렉션합니다.
3. Azure AD STS 서비스가 **사용자 로그인 페이지**로 응답합니다.
4. 사용자가 **사용자 로그인** 페이지에 사용자 이름을 입력하고 **다음** 단추를 선택합니다.
5. 사용자가 **사용자 로그인** 페이지에 암호를 입력하고 **로그인** 단추를 선택합니다.
6. 사용자 이름 및 암호가 HTTPS POST 요청을 통해 Azure AD STS로 전송됩니다.
7. Azure AD STS가 Azure SQL 데이터베이스에서 해당 테넌트에 등록된 모든 인증 에이전트의 공개 키를 가져오고 가져온 공개 키를 사용하여 암호를 암호화합니다.
    - 해당 테넌트에 등록된 “N”개의 인증 에이전트에 대해 “N”개의 암호화된 암호 값을 생성합니다.
8. Azure AD STS가 사용자 이름과 암호화된 암호 값으로 구성된 암호 유효성 검사 요청을 해당 테넌트의 Service Bus 큐에 배치합니다.
9. 초기화된 인증 에이전트는 Service Bus 큐에 영구적으로 연결되므로 사용 가능한 인증 에이전트 중 하나가 암호 유효성 검사 요청을 가져옵니다.
10. 인증 에이전트가 식별자를 사용하여 자신의 공개 키에 고유한 암호화된 암호 값을 찾고 자신의 프라이빗 키를 사용하여 암호를 해독합니다.
11. 인증 에이전트가 [dwLogonType](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) 매개 변수가 **LOGON32_LOGON_NETWORK**로 설정된 **Win32 LogonUser API**를 사용하여 온-프레미스 Active Directory에 대해 사용자 이름 및 암호의 유효성을 검사합니다. 
    - Win32 LogonUser API는 AD FS(Active Directory Federation Service)가 페더레이션 로그인 시나리오에서 사용자 로그인에 사용하는 API이기도 합니다.
    - Win32 LogonUser API는 Windows Server의 표준 확인 프로세스를 사용하여 도메인 컨트롤러를 찾습니다.
12. 인증 에이전트가 Active Directory로부터 성공, 사용자 이름 또는 암호 불일치, 암호 만료와 같은 결과를 수신합니다.

   > [!NOTE]
   > 로그인 프로세스 중에 인증 에이전트가 실패 하면 전체 로그인 요청이 삭제 됩니다. 한 인증 에이전트에서 온-프레미스로의 다른 인증 에이전트로 로그인 요청이 발생 하는 것은 아닙니다. 이러한 에이전트는 클라우드와만 통신 하 고 서로는 통신 하지 않습니다.
   
13. 인증 에이전트는 포트 443을 통한 아웃바운드 상호 인증 HTTPS 채널을 통해 결과를 다시 Azure AD STS로 전달합니다. 상호 인증은 이전 단계에서 등록 시 인증 에이전트로 발급된 인증서를 사용합니다.
14. Azure AD STS는 이 결과가 테넌트의 특정 로그인 요청과 관련이 있는지 확인합니다.
15. Azure AD STS는 구성된 대로 로그인 절차를 계속 진행합니다. 예를 들어, 암호 유효성 검사가 성공적인 경우 사용자에게 Multi-Factor Authentication을 요청하거나 사용자를 애플리케이션으로 리디렉션합니다.

## <a name="operational-security-of-the-authentication-agents"></a>인증 에이전트의 운영 보안

통과 인증의 운영 안전을 보장하기 위해 Azure AD는 인증 에이전트의 인증서를 주기적으로 갱신합니다. 갱신은 Azure AD에 의해 트리거되며 인증 에이전트가 자체적으로 관리하지 않습니다.

![운영 보안](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Azure AD에서 인증 에이전트의 신뢰를 갱신하기 위해:

1. 인증 에이전트가 Azure AD를 몇 시간마다 주기적으로 ping하여 인증서를 갱신할 때인지 확인합니다. 인증서가 만료되기 30일 전에 갱신됩니다.
    - 확인은 상호 인증된 HTTPS 채널을 통해 이루어지고, 등록 시 발급된 인증서가 사용됩니다.
2. 갱신할 시간임을 확인한 경우 인증 에이전트가 새로운 공개 키와 프라이빗 키 쌍을 생성합니다.
    - 키 쌍은 표준 RSA 2048비트 암호화를 사용하여 생성됩니다.
    - 프라이빗 키는 온-프레미스 서버를 벗어나지 않습니다.
3. 그러면 인증 에이전트는 요청에 포함된 다음 구성 요소와 함께 HTTPS를 통해 Azure AD에 “인증서 갱신” 요청을 합니다.
    - Windows 인증서 저장소(CERT_SYSTEM_STORE_LOCAL_MACHINE 위치)에서 가져온 기존 인증서. 이 절차에는 전역 관리자가 관여하지 않으므로 전역 관리자를 대신하여 사용할 액세스 토큰이 필요하지 않습니다.
    - 2단계에서 생성된 공개 키.
    - 인증서 서명 요청(CSR 또는 인증서 요청). 이 요청은 새로운 디지털 ID 인증서에 적용됩니다. 이때 Azure AD가 인증 기관으로 사용됩니다.
4. Azure AD는 인증서 갱신 요청에서 기존 인증서의 유효성을 검사합니다. 그런 다음 요청이 테넌트에 등록된 인증 에이전트에서 발생한 것인지 확인합니다.
5. 기존 인증서가 여전히 유효한 경우 Azure AD는 새 디지털 ID 인증서를 서명하고 인증 에이전트에 새 인증서를 다시 발급합니다. 
6. 기존 인증서가 만료된 경우 Azure AD는 테넌트의 등록된 인증 에이전트 목록에서 인증 에이전트를 삭제합니다. 이때 전역 관리자가 새 인증 에이전트를 수동으로 설치하고 등록해야 합니다.
    - Azure AD 루트 CA를 사용하여 인증서에 서명합니다.
    - 인증서의 제목(고유 이름 또는 DN)을 해당 테넌트를 고유하게 식별하는 GUID인 테넌트 ID로 설정합니다. DN은 인증서가 해당 테넌트에만 사용되도록 범위를 지정합니다.
6. Azure AD는 인증 에이전트의 새로운 공개 키를 Azure AD만 액세스할 수 있는 Azure SQL 데이터베이스에 저장합니다. 인증 에이전트에 연결되었던 이전 공개 키를 무효화합니다.
7. 5단계에서 발급된 새 인증서가 Windows 인증서 저장소([CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) 위치)의 서버에 저장됩니다.
    - 신뢰 갱신 절차는 전역 관리자의 개입 없이 비대화형으로 이루어지기 때문에 인증 에이전트는 더 이상 CERT_SYSTEM_STORE_LOCAL_MACHINE 위치에 있는 기존 인증서를 업데이트할 액세스 권한이 없습니다. 
    
   > [!NOTE]
   > 이 절차로 인해 CERT_SYSTEM_STORE_LOCAL_MACHINE 위치에서 인증서가 제거되지는 않습니다.
8. 새 인증서는 이 시점부터 인증에 사용됩니다. 이후 인증서 갱신이 이루어지면 CERT_SYSTEM_STORE_LOCAL_MACHINE 위치에 있는 인증서가 대체됩니다.

## <a name="auto-update-of-the-authentication-agents"></a>인증 에이전트의 자동 업데이트

업데이트 프로그램 응용 프로그램은 새 버전 (버그 수정 또는 성능 향상이 포함 됨)이 릴리스되면 인증 에이전트를 자동으로 업데이트 합니다. 업데이트 프로그램 응용 프로그램은 테 넌 트에 대 한 암호 유효성 검사 요청을 처리 하지 않습니다.

Azure AD는 새로운 소프트웨어 버전을 서명된 **Windows Installer 패키지(MSI)** 로서 호스팅합니다. MSI는 [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx)를 사용하여 서명됩니다. 이때 SHA256이 다이제스트 알고리즘으로 사용됩니다. 

![자동 업데이트](./media/how-to-connect-pta-security-deep-dive/pta5.png)

인증 에이전트를 자동 업데이트하기 위해:

1. 업데이트 애플리케이션이 Azure AD를 한 시간마다 주기적으로 ping하여 새로운 버전의 인증 에이전트가 있는지 확인합니다. 
    - 확인은 상호 인증된 HTTPS 채널을 통해 이루어지고, 등록 시 발급된 인증서가 사용됩니다. 인증 에이전트와 업데이트 프로그램은 서버에 저장된 인증서를 공유합니다.
2. 새 버전이 제공되는 경우 Azure AD는 서명된 MSI를 업데이트 프로그램에 다시 반환합니다.
3. 업데이트 프로그램은 Microsoft에서 MSI를 서명했는지 확인합니다.
4. 업데이트 프로그램은 MSI를 실행합니다. 이때 다음과 같은 단계가 수행됩니다.

   > [!NOTE]
   > 업데이트 프로그램은 [로컬 시스템](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) 권한으로 실행됩니다.

    - 인증 에이전트 서비스를 중지합니다.
    - 서버에 새 인증 에이전트 버전을 설치합니다.
    - 인증 에이전트 서비스를 다시 시작합니다.

>[!NOTE]
>테넌트에 여러 인증 에이전트가 등록된 경우 Azure AD는 인증서를 갱신하거나 동시에 업데이트하지 않습니다. 대신 Azure AD는 로그인 요청의 고가용성을 보장 하기 위해 한 번에 한 번씩 수행 합니다.
>


## <a name="next-steps"></a>다음 단계
- [현재 제한 사항](how-to-connect-pta-current-limitations.md): 지원되는 시나리오와 지원되지 않는 시나리오를 알아봅니다.
- [빠른](how-to-connect-pta-quick-start.md)시작: Azure AD 통과 인증을 시작 하 고 실행 합니다.
- [AD FS에서 통과 인증으로 마이그레이션](https://aka.ms/adfstoptadpdownload) - AD FS(또는 기타 페더레이션 기술)에서 통과 인증으로 마이그레이션하는 방법에 대한 자세한 가이드입니다.
- [스마트 잠금](../authentication/howto-password-smart-lockout.md): 테넌트에서 스마트 잠금 기능을 구성하여 사용자 계정을 보호합니다.
- [작동 방법](how-to-connect-pta-how-it-works.md):- Azure AD 통과 인증이 작동하는 기본적인 방식을 알아봅니다.
- [질문과 대답](how-to-connect-pta-faq.md): 자주 하는 질문과 대답을 살펴봅니다.
- [문제 해결](tshoot-connect-pass-through-authentication.md): 통과 인증 기능의 일반적인 문제를 해결하는 방법을 알아봅니다.
- [Azure AD Seamless SSO](how-to-connect-sso.md): 보완적인 Azure AD Seamless SSO 기능을 알아봅니다.
