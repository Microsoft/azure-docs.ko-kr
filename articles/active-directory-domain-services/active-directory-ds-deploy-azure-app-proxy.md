---
title: 'Azure Active Directory Domain Services: Azure Active Directory 애플리케이션 프록시 배포 | Microsoft Docs'
description: Azure Active Directory Domain Services 관리되는 도메인에서 Azure AD 애플리케이션 프록시 사용
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 867d061e46494e5ef65340ce325a71638acc8dfa
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104136"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리되는 도메인에서 Azure AD 애플리케이션 프록시 배포
Azure AD(Active Directory) 애플리케이션 프록시를 사용하면 인터넷을 통해 액세스할 수 있는 온-프레미스 애플리케이션을 게시하여 원격 작업자를 지원할 수 있습니다. 이제 Azure AD Domain Services를 통해 온-프레미스를 운영 중인 레거시 애플리케이션을 Azure Infrastructure Services로 전환할 수 있습니다. 그러면 Azure AD 애플리케이션 프록시를 사용하는 이러한 애플리케이션을 게시하여 조직 내 사용자에게 안전한 원격 액세스를 제공할 수 있습니다.

Azure AD 애플리케이션 프록시를 처음 사용하는 경우 다음에 나오는 [온-프레미스 애플리케이션에 보안된 원격 액세스를 제공하는 방법](../active-directory/manage-apps/application-proxy.md) 문서에서 이 기능에 대해 자세히 알아보세요.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>시작하기 전에
이 문서에 나열된 작업을 수행하려면 다음이 필요합니다.

1. 유효한 **Azure 구독**.
2. **Azure AD 디렉터리** - 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화됩니다.
3. Azure AD 애플리케이션 프록시를 사용하려면 **Azure AD Basic 또는 Premium 라이선스**가 필요합니다.
4. **Azure AD 도메인 서비스**를 사용하도록 설정해야 합니다. 그렇지 않은 경우 [시작 가이드](active-directory-ds-getting-started.md)에 간략히 설명된 모든 작업을 따릅니다.

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>작업 1 - Azure AD Directory에 대한 Azure AD 애플리케이션 프록시 활성화
다음 단계를 수행하여 Azure AD Directory에 대해 Azure AD 애플리케이션 프록시를 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com)에서 관리자로 로그인합니다.

2. **Azure Active Directory**를 클릭하여 디렉터리 개요를 표시합니다. **엔터프라이즈 애플리케이션**을 클릭합니다.

    ![Azure AD Directory 선택](./media/app-proxy/app-proxy-enable-start.png)
3. **애플리케이션 프록시**를 클릭합니다. Azure AD Basic 또는 Azure AD Premium 구독이 없을 경우 평가판을 사용하도록 설정하는 옵션이 표시됩니다. **애플리케이션 프록시 사용?** 을 **사용**으로 설정/해제하고 **저장**을 클릭합니다.

    ![앱 프록시 사용](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. 커넥터를 다운로드하려면 **커넥터** 단추를 클릭합니다.

    ![커넥터 다운로드](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. 다운로드 페이지에서 사용 약관 및 개인 정보 보호 계약에 동의하고 **다운로드** 단추를 클릭합니다.

    ![다운로드 확인](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>작업 2 - 도메인에 가입된 Windows 서버를 프로비전하여 Azure AD 애플리케이션 프록시 커넥터를 배포
Azure AD 애플리케이션 프록시 커넥터를 설치할 수 있는 도메인에 가입된 Windows Server 가상 머신이 필요합니다. 애플리케이션에 따라 커넥터가 설치된 여러 서버를 프로비전하도록 선택할 수도 있습니다. 이 배포 옵션을 선택하면 가용성이 높아지고 더 많은 인증 부하를 처리하는 데 도움이 됩니다.

Azure AD Domain Services 관리되는 도메인을 사용할 수 있는 동일한 가상 네트워크(또는 연결/피어링된 가상 네트워크)에서 커넥터 서버를 프로비전합니다. 마찬가지로, 애플리케이션 프록시를 통해 게시한 애플리케이션을 호스팅하는 서버는 동일한 Azure 가상 네트워크에 설치해야 합니다.

커넥터 서버를 프로비전하려면 [Windows 가상 머신을 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md) 문서에 설명된 작업을 수행하세요.


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>작업 3 - Azure AD 애플리케이션 프록시 커넥터 설치 및 등록
사전에 Windows Server 가상 컴퓨터를 프로비전하고 관리되는 도메인에 가입했습니다. 이 작업에서는 이 가상 머신에 Azure AD 애플리케이션 프록시 커넥터를 설치합니다.

1. Azure AD 웹 애플리케이션 프록시 커넥터를 설치하는 VM에 커넥터 설치 패키지를 복사합니다.

2. **AADApplicationProxyConnectorInstaller.exe**를 가상 머신에서 실행합니다. 소프트웨어 사용 조건에 동의합니다.

    ![설치 조건에 동의](./media/app-proxy/app-proxy-install-connector-terms.png)
3. 설치하는 동안 Azure AD Directory의 애플리케이션 프록시를 사용하여 커넥터를 등록하라는 메시지가 표시됩니다.
   * **Azure AD 전역 관리자 자격 증명**을 제공합니다. 전역 관리자 테넌트는 Microsoft Azure 자격 증명과 다를 수 있습니다.
   * 커넥터를 등록하는 데 사용되는 관리자 계정은 애플리케이션 프록시 서비스를 사용할 수 있는 동일한 디렉터리에 있어야 합니다. 예를 들어, 테넌트 도메인이 contoso.com이면 관리자는 admin@contoso.com 또는 해당 도메인에 있는 다른 유효한 별칭이어야 합니다.
   * 커넥터를 설치하는 서버에 IE 보안 강화 구성이 켜져 있으면 등록 화면이 차단될 수 있습니다. 액세스를 허용하려면 오류 메시지의 지침에 따릅니다. Internet Explorer 보안 강화가 해제되어 있는지 확인하세요.
   * 커넥터 등록에 실패한 경우 [애플리케이션 프록시 문제 해결](../active-directory/manage-apps/application-proxy-troubleshoot.md)을 참조하세요.

     ![커넥터 설치됨](./media/app-proxy/app-proxy-connector-installed.png)
4. 커넥터가 제대로 작동하는지 확인하려면 Azure AD 애플리케이션 프록시 커넥터 문제 해결사를 실행합니다. 문제 해결사를 실행한 후 성공적인 보고서가 표시됩니다.

    ![문제 해결사 성공](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. 새롭게 설치된 커넥터가 Azure AD Directory의 애플리케이션 프록시 페이지에 나열됩니다.

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> 커넥터를 여러 서버에 설치하도록 선택하면 Azure AD 애플리케이션 프록시를 통해 게시된 애플리케이션을 인증하는 데 고가용성을 보장할 수 있습니다. 관리되는 도메인에 가입된 다른 서버에 커넥터를 설치하려면 위에 나열된 것과 동일한 단계를 수행합니다.
>
>

## <a name="next-steps"></a>다음 단계
지금까지 Azure AD 애플리케이션 프록시를 설정하고 Azure AD Domain Services 관리되는 도메인에 통합했습니다.

* **애플리케이션을 Azure 가상 머신으로 마이그레이션:** 애플리케이션을 온-프레미스 서버에서 관리되는 도메인에 가입된 Azure 가상 머신으로 전환할 수 있습니다. 이를 통해 온-프레미스 운영 서버의 인프라 비용을 절감할 수 있습니다.

* **Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시:** Azure AD 애플리케이션 프록시를 사용하여 Azure 가상 머신에서 실행 중인 애플리케이션을 게시합니다. 자세한 내용은 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)를 참조하세요.


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>배포 참고 - Azure AD 애플리케이션 프록시를 사용하여 IWA(Windows 통합 인증) 애플리케이션 게시
애플리케이션 프록시 커넥터 사용 권한을 부여하여 사용자를 가장하고 사용자를 대신해서 토큰을 보내고 받음으로써 IWA(Windows 통합 인증)를 사용하여 애플리케이션에 Single Sign-On을 사용하도록 설정합니다. 커넥터에 대한 Kerberos 제한 위임(KCD)을 구성하여 관리되는 도메인의 리소스에 액세스하는 데 필요한 권한을 부여합니다. 보안 향상을 위해 관리되는 도메인에서 리소스 기반 KCD 메커니즘을 사용합니다.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Azure AD 애플리케이션 프록시 커넥터에 대한 리소스 기반 Kerberos 제한 위임을 사용하도록 설정
관리되는 도메인에서 사용자를 가장할 수 있도록 Azure 애플리케이션 프록시 커넥터는 Kerberos 제한 위임(KCD)에 대해 구성되어야 합니다. Azure AD Domain Services 관리되는 도메인에서 도메인 관리자 권한이 없습니다. 따라서 **관리되는 도메인에 기존 계정 수준 KCD를 구성할 수 없습니다**.

이 [문서](active-directory-ds-enable-kcd.md)에 설명된 대로 리소스 기반 KCD를 사용합니다.

> [!NOTE]
> AD PowerShell cmdlet을 사용하여 관리되는 도메인을 관리하려면 'AAD DC 관리자' 그룹의 구성원이어야 합니다.
>
>

Get-ADComputer PowerShell cmdlet을 사용하여 Azure AD 애플리케이션 프록시 커넥터가 설치된 컴퓨터에 대한 설정을 검색합니다.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

그 후 Set-ADComputer cmdlet을 사용하여 리소스 서버에 대한 리소스 기반 KCD를 설정합니다.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

관리되는 도메인에서 여러 애플리케이션 프록시 커넥터를 배포한 경우 이러한 각 커넥터 인스턴스에 대한 리소스 기반 KCD를 구성해야 합니다.


## <a name="related-content"></a>관련 콘텐츠
* [Azure AD Domain Services - 시작 가이드](active-directory-ds-getting-started.md)
* [관리되는 도메인에서 Kerberos 제한 위임 구성](active-directory-ds-enable-kcd.md)
* [Kerberos 제한 위임 개요](https://technet.microsoft.com/library/jj553400.aspx)
