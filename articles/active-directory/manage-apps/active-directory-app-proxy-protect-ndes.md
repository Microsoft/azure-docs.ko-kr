---
title: NDES 서버에서 AD 응용 프로그램 프록시와 통합
titleSuffix: Azure Active Directory
description: NDES 서버를 보호 하기 위해 Azure Active Directory 응용 프로그램 프록시를 배포 하는 방법에 대 한 지침입니다.
services: active-directory
author: CelesteDG
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: baselden
ms.reviewer: mimart
ms.openlocfilehash: 4ccd8834671725ace72497391090f81eb197ad6a
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77032258"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>NDES (네트워크 장치 등록 서비스) 서버에서 Azure AD 응용 프로그램 프록시와 통합

AD (Azure Active Directory) 응용 프로그램 프록시를 사용 하면 네트워크 내부에 응용 프로그램을 게시할 수 있습니다. 이러한 응용 프로그램은 SharePoint 사이트, Microsoft Outlook Web App 및 기타 웹 응용 프로그램과 같은 응용 프로그램입니다. 또한 Azure를 통해 네트워크 외부의 사용자에 게 보안 액세스를 제공 합니다.

Azure AD 응용 프로그램 프록시를 처음 사용 하 고 자세히 알아보려면 [azure AD 응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 원격 액세스를](application-proxy.md)참조 하세요.

Azure AD 응용 프로그램 프록시은 Azure를 기반으로 합니다. DDOS (배포 된 서비스 거부) 공격과 뛰어난 가용성에 대 한 보호를 강화 하기 위해 대량의 네트워크 대역폭과 서버 인프라를 제공 합니다. 또한 온-프레미스 네트워크에 대 한 외부 방화벽 포트를 열 필요가 없으며 DMZ 서버가 필요 하지 않습니다. 모든 트래픽은 인바운드로 시작 됩니다. 아웃 바운드 포트의 전체 목록은 [자습서: Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)를 참조 하세요.

> Azure AD 응용 프로그램 프록시는 Azure Active Directory 프리미엄 또는 기본 버전을 사용 하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요. 
> EMS (Enterprise Mobility Suite) 라이선스가 있는 경우이 솔루션을 사용할 수 있습니다.
> Azure AD 응용 프로그램 프록시 커넥터는 Windows Server 2012 R2 이상에만 설치 됩니다. NDES 서버의 요구 사항 이기도 합니다.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>NDES 서버에 커넥터를 설치 하 고 등록 합니다.

1. 애플리케이션 프록시를 사용하는 디렉터리의 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 예를 들어, 테넌트 도메인이 contoso.com이면 관리자는 admin@contoso.com 또는 해당 도메인에 있는 다른 관리자 별칭이어야 합니다.
1. 오른쪽 위 모서리에서 사용자 이름을 선택합니다. 애플리케이션 프록시를 사용하는 디렉터리에 로그인했는지 확인합니다. 디렉터리를 변경해야 할 경우 **디렉터리 전환**을 선택하고 애플리케이션 프록시를 사용하는 디렉터리를 선택합니다.
1. 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택합니다.
1. **관리** 아래에서 **애플리케이션 프록시**를 선택합니다.
1. **커넥터 서비스 다운로드**를 선택합니다.

    ![커넥터 서비스를 다운로드하여 서비스 약관 확인](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. 서비스 약관을 참고하세요. 준비되면 **사용 약관 동의 및 다운로드**를 선택합니다.
1. Azure AD 응용 프로그램 프록시 connector 설치 파일을 NDES 서버에 복사 합니다. 
   > NDES에 대 한 액세스 권한이 있는 회사 네트워크 내의 모든 서버에 커넥터를 설치할 수 있습니다. NDES 서버 자체에 설치할 필요는 없습니다.
1. *Aadapplicationproxyconnectorinstaller.exe*와 같은 설치 파일을 실행 합니다. 소프트웨어 사용 조건에 동의합니다.
1. 설치 하는 동안 Azure AD 디렉터리의 응용 프로그램 프록시를 사용 하 여 커넥터를 등록 하 라는 메시지가 표시 됩니다.
   * Azure AD 디렉터리에서 전역 또는 응용 프로그램 관리자에 대 한 자격 증명을 제공 합니다. Azure AD 전역 또는 응용 프로그램 관리자 자격 증명은 포털에서 Azure 자격 증명과 다를 수 있습니다.

        > [!NOTE]
        > 커넥터를 등록 하는 데 사용 되는 전역 또는 응용 프로그램 관리자 계정은 응용 프로그램 프록시 서비스를 사용 하도록 설정 하는 동일한 디렉터리에 속해야 합니다.
        >
        > 예를 들어 Azure AD 도메인이 *contoso.com*인 경우 전역/응용 프로그램 관리자는 해당 도메인의 다른 유효한 별칭 또는 `admin@contoso.com` 되어야 합니다.

   * 커넥터를 설치 하는 서버에 대해 Internet Explorer 보안 강화 구성이 설정 된 경우 등록 화면이 차단 될 수 있습니다. 액세스를 허용 하려면 오류 메시지의 지침에 따라 설치 하는 동안 Internet Explorer 보안 강화를 해제 합니다.
   * 커넥터 등록이 실패 하는 경우 [응용 프로그램 프록시 문제 해결](application-proxy-troubleshoot.md)을 참조 하세요.
1. 설정이 끝나면 아웃 바운드 프록시를 사용 하는 환경에 대 한 노트가 표시 됩니다. 아웃 바운드 프록시를 통해 작동 하도록 Azure AD 응용 프로그램 프록시 커넥터를 구성 하려면 제공 된 스크립트 (예: `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`)를 실행 합니다.
1. 다음 예제와 같이 Azure Portal의 응용 프로그램 프록시 페이지에 새 커넥터가 *활성*상태로 나열 됩니다.

    ![Azure Portal에서 활성으로 표시 된 새 Azure AD 응용 프로그램 프록시 커넥터](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Azure AD 응용 프로그램 프록시를 통해 인증 하는 응용 프로그램에 고가용성을 제공 하기 위해 여러 Vm에 커넥터를 설치할 수 있습니다. 이전 섹션에 나열 된 것과 동일한 단계를 반복 하 여 Azure AD DS 관리 되는 도메인에 가입 된 다른 서버에 커넥터를 설치 합니다.

1. 설치가 완료 되 면 Azure Portal로 돌아갑니다.

1. **Enterprise 애플리케이션**을 선택합니다.

   ![올바른 관련자에 게 관심이 있는지 확인](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. **+ 새 응용 프로그램**을 선택 하 고 **온-프레미스 응용 프로그램**을 선택 합니다. 

1. 사용자 **고유의 온-프레미스 응용 프로그램 추가**에서 다음 필드를 구성 합니다.

   * **이름**: 응용 프로그램의 이름을 입력 합니다.
   * **내부 url**: 커넥터를 설치한 NDES 서버의 내부 URL/FQDN을 입력 합니다.
   * **사전 인증**: **통과**를 선택 합니다. 모든 형태의 사전 인증을 사용할 수는 없습니다. 인증서 요청 (SCEP)에 사용 되는 프로토콜은 이러한 옵션을 제공 하지 않습니다.
   * 제공 된 **외부 URL** 을 클립보드에 복사 합니다.

1. **+ 추가** 를 선택 하 여 응용 프로그램을 저장 합니다.

1. 10 단계에서 복사한 링크를 브라우저에 붙여넣어 Azure AD 응용 프로그램 프록시를 통해 NDES 서버에 액세스할 수 있는지 테스트 합니다. 기본 IIS 시작 페이지가 표시 됩니다.

1. 최종 테스트로 이전 단계에서 붙여넣은 기존 URL에 *mscep* 경로를 추가 합니다.

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. **HTTP 오류 403 –** 사용할 수 없음 응답이 표시 되어야 합니다.

1. 제공 된 NDES URL (Microsoft Intune을 통해)을 장치로 변경 합니다 .이 변경은 Microsoft 엔드포인트 구성 센터 또는 Intune 클라우드에 있을 수 있습니다.

   * Configuration Center에 대해 CRP (인증서 등록 지점)로 이동 하 여 URL을 조정 합니다. 이 URL은 장치를 호출 하 고 챌린지를 제공 하는 장치입니다.
   * Intune 독립 실행형이 라고도 하는 Intune 클라우드의 경우 새 SCEP 정책을 편집 하거나 만들고 새 URL을 추가 합니다.

## <a name="next-steps"></a>다음 단계

NDES와 통합 된 Azure AD 응용 프로그램 프록시를 사용 하 여 사용자가 액세스할 수 있도록 응용 프로그램을 게시 합니다. 자세한 내용은 [Azure AD 응용 프로그램 프록시를 사용 하 여 응용 프로그램 게시](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)를 참조 하세요.
