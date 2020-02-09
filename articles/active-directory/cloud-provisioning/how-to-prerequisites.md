---
title: Azure AD에서 클라우드 프로 비전을 Azure AD Connect 하기 위한 필수 구성 요소
description: 이 문서에서는 클라우드 프로 비전에 필요한 필수 구성 요소 및 하드웨어 요구 사항을 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e5c58d29f01cadcc3ea2e8ec48ae67e58c4180
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76909043"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect 클라우드 프로 비전을 위한 필수 구성 요소
이 문서에서는 id 솔루션으로 Azure AD (Azure AD) Connect cloud 프로 비전을 선택 하 고 Azure Active Directory 사용 하는 방법에 대 한 지침을 제공 합니다.



## <a name="cloud-provisioning-agent-requirements"></a>클라우드 프로 비전 에이전트 요구 사항
Azure AD Connect 클라우드 프로 비전을 사용 하려면 다음이 필요 합니다.
    
- Azure AD 테넌트에 대 한 전역 관리자 계정.
- Windows 2012 R2 이상 버전을 사용 하는 프로 비전 에이전트에 대 한 온-프레미스 서버입니다.
- 온-프레미스 방화벽 구성.

>[!NOTE]
>프로 비전 에이전트는 현재 영어 언어 서버에만 설치할 수 있습니다. 영어가 아닌 서버에 영어 언어 팩을 설치 하는 것은 올바른 해결 방법이 아니므로 에이전트가 설치 되지 않습니다. 

문서의 나머지 부분에서는 이러한 필수 조건에 대 한 단계별 지침을 제공 합니다.

### <a name="in-the-azure-active-directory-admin-center"></a>Azure Active Directory 관리 센터에서

1. Azure AD 테넌트에서 클라우드 전용 전역 관리자 계정을 만듭니다. 이러한 방식으로 온-프레미스 서비스가 실패 하거나 사용할 수 없게 되는 경우 테넌트의 구성을 관리할 수 있습니다. [클라우드 전용 전역 관리자 계정을 추가](../active-directory-users-create-azure-portal.md)하는 방법에 대해 알아봅니다. 이 단계를 완료 하는 것은 테넌트에서 잠기지 않도록 하는 데 중요 합니다.
1. Azure AD 테넌트에 [사용자 지정 도메인 이름](../active-directory-domains-add-azure-portal.md)을 하나 이상 추가합니다. 사용자는 이러한 도메인 이름 중 하나로 로그인할 수 있습니다.

### <a name="in-your-on-premises-environment"></a>온-프레미스 환경에서

1. 최소 4gb RAM 및 .NET 4.7.1 + runtime을 사용 하 여 Windows Server 2012 R2 이상을 실행 하는 도메인에 가입 된 호스트 서버를 식별 합니다.

1. 서버와 Azure AD 사이에 방화벽이 있는 경우 다음 항목을 구성 합니다.
   - 에이전트에서 다음 포트를 통해 Azure AD에 대한 *아웃바운드* 요청을 수행할 수 있는지 확인합니다.

        | 포트 번호 | 사용 방법 |
        | --- | --- |
        | **80** | SSL 인증서의 유효성을 검사 하는 동안 Crl (인증서 해지 목록)을 다운로드 합니다.  |
        | **443** | 서비스와의 모든 아웃 바운드 통신을 처리 합니다. |
        | **8080**(선택 사항) | 443 포트를 사용할 수 없는 경우 에이전트는 8080 포트를 통해 10분마다 해당 상태를 보고합니다. 이 상태는 Azure AD 포털에 표시 됩니다. |
     
   - 방화벽이 원래 사용자에 따라 규칙에 적용되는 경우 네트워크 서비스로 실행하는 Windows 서비스의 트래픽에 대해 이러한 포트를 엽니다.
   - 방화벽이 나 프록시를 사용 하 여 안전한 접미사를 지정 하는 경우 msappproxy.net \*및 servicebus.windows.net \*에 연결을 추가 합니다. 그렇지 않으면 매주 업데이트되는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 액세스하도록 허용합니다.
   - 초기 등록을 위해 에이전트에 login.windows.net 및 login.microsoftonline.com에 대 한 액세스 권한이 필요 합니다. 이러한 URL에 대한 방화벽도 엽니다.
   - 인증서 유효성을 검사 하려면 다음 Url을 차단 해제 합니다. mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 및 www\.microsoft.com:80. 이러한 Url은 다른 Microsoft 제품과의 인증서 유효성 검사에 사용 되므로 이러한 Url의 차단이 이미 해제 되어 있을 수 있습니다.

### <a name="verify-the-port"></a>포트 확인
Azure가 포트 443에서 수신 대기 하 고 에이전트와 통신할 수 있는지 확인 하려면 다음 URL을 사용 합니다.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

이 테스트는 에이전트가 포트 443을 통해 Azure와 통신할 수 있는지 확인 합니다. 브라우저를 열고 에이전트가 설치 된 서버에서 이전 URL로 이동 합니다.

![포트 연결 가능성 확인](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>추가 요구 사항
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS 요구 사항

>[!NOTE]
>TLS (전송 계층 보안)는 보안 통신을 위해에서 제공 하는 프로토콜입니다. TLS 설정을 변경 하면 전체 포리스트에 영향을 줍니다. 자세한 내용은 [Windows의 WinHTTP에서 tls 1.1 및 tls 1.2를 기본 보안 프로토콜로 사용 하도록 업데이트를](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)참조 하세요.

Azure AD Connect 클라우드 프로 비전 에이전트를 호스트 하는 Windows server를 설치 하기 전에 TLS 1.2를 사용 하도록 설정 해야 합니다.

TLS 1.2을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. 다음 레지스트리 키를 설정합니다.
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. 서버를 다시 시작합니다.


## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)

