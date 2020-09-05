---
title: 자습서 - Azure Active Directory Domain Services에 대한 LDAPS 구성 | Microsoft Docs
description: 이 자습서에서는 Azure Active Directory Domain Services 관리되는 도메인에 대해 LDAPS(보안 Lightweight Directory Access Protocol)를 구성하는 방법을 알아봅니다.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 1164d838a45496a075d356995a60beb967cdfcca
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88054343"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>자습서: Azure Active Directory Domain Services 관리되는 도메인에 대한 보안 LDAP 구성

LDAP(Lightweight Directory Access Protocol)는 Azure AD DS(Azure Active Directory Domain Services) 관리되는 도메인과 통신하는 데 사용됩니다. 기본적으로 LDAP 트래픽은 암호화되지 않으므로 여러 환경에서 보안 문제가 됩니다.

Azure AD DS를 사용하면 LDAPS(보안 Lightweight Directory Access Protocol)를 사용하도록 관리되는 도메인을 구성할 수 있습니다. 보안 LDAP를 사용하면 트래픽이 암호화됩니다. 보안 LDAP는 SSL(Secure Sockets Layer)/TLS(Transport Layer Security)를 통한 LDAP라고도 합니다.

이 자습서에서는 Azure AD DS 관리되는 도메인에 대한 LDAPS를 구성하는 방법을 보여 줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure AD DS에 사용할 디지털 인증서 만들기
> * Azure AD DS에서 보안 LDAP를 사용하도록 설정
> * 퍼블릭 인터넷을 통해 사용할 보안 LDAP 구성
> * 관리되는 도메인에 대한 보안 LDAP 바인딩 및 테스트

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 [Azure Active Directory Domain Services 관리형 도메인을 만들고 구성합니다][create-azure-ad-ds-instance].
* 컴퓨터에 설치된 *LDP.exe* 도구
    * 필요한 경우 *Active Directory Domain Services 및 LDAP*에 대한 [RSAT(원격 서버 관리 도구)를 설치합니다][rsat].

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

이 자습서에서는 Azure Portal을 사용하여 관리되는 도메인에 대한 보안 LDAP를 구성합니다. 시작하려면 먼저 [Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-certificate-for-secure-ldap"></a>보안 LDAP에 대한 인증서 만들기

보안 LDAP를 사용하려면 디지털 인증서를 사용하여 통신을 암호화합니다. 이 디지털 인증서는 관리되는 도메인에 적용되며, *LDP.exe*와 같은 도구에서 데이터를 쿼리할 때 암호화된 보안 통신을 사용할 수 있습니다. 관리되는 도메인에 대한 보안 LDAP 액세스용 인증서를 만드는 두 가지 방법이 있습니다.

* 퍼블릭 CA(인증 기관) 또는 엔터프라이즈 CA의 인증서
    * 조직에서 퍼블릭 CA의 인증서를 가져오는 경우 해당 퍼블릭 CA에서 보안 LDAP 인증서를 가져옵니다. 조직에서 엔터프라이즈 CA를 사용하는 경우 엔터프라이즈 CA에서 보안 LDAP 인증서를 가져옵니다.
    * 퍼블릭 CA는 관리되는 도메인에서 사용자 지정 DNS 이름을 사용하는 경우에만 작동합니다. 관리되는 도메인의 DNS 도메인 이름이 *.onmicrosoft.com*으로 끝나면 이 기본 도메인과의 연결을 보호하기 위해 디지털 인증서를 만들 수 없습니다. Microsoft에서 *.onmicrosoft.com* 도메인을 소유하고 있으므로 퍼블릭 CA는 인증서를 발급하지 않습니다. 이 시나리오에서는 자체 서명된 인증서를 만들고 이를 사용하여 보안 LDAP를 구성합니다.
* 사용자가 직접 만든 자체 서명된 인증서
    * 이 방법은 테스트 용도에 적합하며 이 자습서에서 설명하는 내용입니다.

요청하거나 만드는 인증서에서 충족해야 하는 요구 사항은 다음과 같습니다. 잘못된 인증서를 사용하여 보안 LDAP를 사용하도록 설정하면 관리되는 도메인에 문제가 발생합니다.

* **신뢰할 수 있는 발급자** - 인증서는 보안 LDAP를 사용하여 관리되는 도메인에 연결하는 컴퓨터에서 신뢰하는 기관에서 발급된 것이어야 합니다. 이 기관은 이러한 컴퓨터에서 신뢰할 수 있는 퍼블릭 CA 또는 엔터프라이즈 CA일 수 있습니다.
* **수명** - 인증서는 다음 3-6개월 이상 동안 유효해야 합니다. 인증서가 만료될 때 관리되는 도메인에 대한 보안 LDAP 액세스가 중단됩니다.
* **주체 이름** - 인증서의 주체 이름은 관리되는 도메인이어야 합니다. 예를 들어 도메인 이름이 *aaddscontoso.com*인 경우 인증서의 주체 이름은 * *.aaddscontoso.com*이어야 합니다.
    * 보안 LDAP가 Azure AD Domain Services에서 제대로 작동하려면 인증서의 DNS 이름 또는 주체 대체 이름이 와일드카드 인증서여야 합니다. 도메인 컨트롤러는 임의의 이름을 사용하며, 서비스를 계속 사용할 수 있도록 제거하거나 추가할 수 있습니다.
* **키 사용** - 인증서를 *디지털 서명* 및 *키 암호화*에 맞게 구성해야 합니다.
* **인증서 용도** - 인증서는 TLS 서버 인증에 대해 유효해야 합니다.

OpenSSL, Keytool, MakeCert, [New-SelfSignedCertificate][New-SelfSignedCertificate] cmdlet 등 자체 서명된 인증서를 만드는 데 사용할 수 있는 여러 도구가 있습니다.

이 자습서에서는 [New-SelfSignedCertificate][New-SelfSignedCertificate] cmdlet을 사용하여 보안 LDAP용 자체 서명된 인증서를 만들어 보겠습니다.

PowerShell 창을 **관리자** 권한으로 열고 다음 명령을 실행합니다. *$dnsName* 변수를 사용자 고유의 관리되는 도메인에서 사용하는 DNS 이름(예: *aaddscontoso.com*)으로 바꿉니다.

```powershell
# Define your own DNS name used by your managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

다음 출력 예제에서는 인증서가 성공적으로 생성되었으며 로컬 인증서 저장소(*LocalMachine\MY*)에 저장되었음을 보여 줍니다.

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>필요한 인증서 이해 및 내보내기

보안 LDAP를 사용하기 위해 네트워크 트래픽이 PKI(퍼블릭 키 인프라)를 사용하여 암호화됩니다.

* **프라이빗** 키는 관리되는 도메인에 적용됩니다.
    * 이 프라이빗 키는 보안 LDAP 트래픽의 *암호를 해독*하는 데 사용됩니다. 프라이빗 키는 관리되는 도메인에만 적용되고 클라이언트 컴퓨터에 광범위하게 배포되지 않아야 합니다.
    * 프라이빗 키가 포함된 인증서는 *.PFX* 파일 형식을 사용합니다.
    * 인증서에 대한 암호화 알고리즘은 *TripleDES-SHA1*여야 합니다.
* **퍼블릭** 키는 클라이언트 컴퓨터에 적용됩니다.
    * 이 퍼블릭 키는 보안 LDAP 트래픽을 *암호화*하는 데 사용됩니다. 퍼블릭 키는 클라이언트 컴퓨터에 배포할 수 있습니다.
    * 프라이빗 키가 없는 인증서는 *.CER* 파일 형식을 사용합니다.

이러한 두 키(*프라이빗* 키 및 *퍼블릭* 키)는 적절한 컴퓨터만 성공적으로 상호 통신할 수 있도록 합니다. 퍼블릭 CA 또는 엔터프라이즈 CA를 사용하는 경우 프라이빗 키가 포함된 인증서가 발급되고 관리되는 도메인에 적용할 수 있습니다. 퍼블릭 키는 클라이언트 컴퓨터에서 이미 알고 있고 신뢰할 수 있어야 합니다.

이 자습서에서는 프라이빗 키를 사용하여 자체 서명된 인증서를 만들었으므로 적절한 프라이빗 및 퍼블릭 구성 요소를 내보내야 합니다.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Azure AD DS에 대한 인증서 내보내기

이전 단계에서 만든 디지털 인증서를 관리되는 도메인에서 사용하려면 먼저 인증서를 프라이빗 키가 포함된 *.PFX* 인증서 파일로 내보냅니다.

1. *실행* 대화 상자를 열려면 **Windows** + **R** 키를 선택합니다.
1. *실행* 대화 상자에서 **mmc**를 입력하여 MMC(Microsoft Management Console)를 열고, **확인**을 선택합니다.
1. **사용자 계정 컨트롤** 프롬프트에서 **예**를 선택하여 MMC를 관리자 권한으로 시작합니다.
1. **파일** 메뉴에서 **스냅인 추가/제거...** 를 선택합니다.
1. **인증서 스냅인** 마법사에서 **컴퓨터 계정**, **다음**을 차례로 선택합니다.
1. **컴퓨터 선택** 페이지에서 **로컬 컴퓨터: (이 콘솔이 실행되고 있는 컴퓨터)** , **마침**을 차례로 선택합니다.
1. **스냅인 추가/제거** 대화 상자에서 **확인**을 선택하여 인증서 스냅인을 MMC에 추가합니다.
1. MMC 창에서 **콘솔 루트**를 펼칩니다. **인증서(로컬 컴퓨터)** 를 선택한 다음, **개인** 노드, **인증서** 노드를 차례로 펼칩니다.

    ![Microsoft Management Console에서 개인 인증서 저장소 열기](./media/tutorial-configure-ldaps/open-personal-store.png)

1. 이전 단계에서 만든 자체 서명된 인증서(예: *aaddscontoso.com*)가 표시됩니다. 마우스 오른쪽 단추로 이 인증서를 선택한 다음, **모든 작업 > 내보내기...** 를 차례로 선택합니다.

    ![Microsoft Management Console에서 인증서 내보내기](./media/tutorial-configure-ldaps/export-cert.png)

1. **인증서 내보내기 마법사**에서 **다음**을 선택합니다.
1. 인증서의 프라이빗 키를 내보내야 합니다. 내보내는 인증서에 프라이빗 키가 포함되지 않으면 관리되는 도메인에서 보안 LDAP를 사용하도록 설정하는 작업이 실패합니다.

    **프라이빗 키 내보내기** 페이지에서 **예, 프라이빗 키를 내보냅니다.** , **다음**을 차례로 선택합니다.
1. 관리되는 도메인은 프라이빗 키가 포함된 *.PFX* 인증서 파일 형식만 지원합니다. 인증서를 프라이빗 키가 포함되지 않은 *.CER* 인증서 파일 형식으로 내보내지 마세요.

    **파일 형식 내보내기** 페이지에서 내보낸 인증서에 대한 파일 형식으로 **개인 정보 교환 – PKCS #12(.PFX)** 를 선택합니다. *가능하면 인증 경로에 있는 인증서 모두 포함* 확인란을 선택합니다.

    ![인증서를 PKCS 12(.PFX) 파일 형식으로 내보내는 옵션 선택](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. 이 인증서는 데이터의 암호를 해독하는 데 사용되므로 액세스를 신중하게 제어해야 합니다. 암호는 인증서의 사용을 보호하는 데 사용할 수 있습니다. 올바른 암호가 없으면 인증서를 서비스에 적용할 수 없습니다.

    *.PFX* 인증서 파일을 보호하려면 **보안** 페이지에서 **암호** 옵션을 선택합니다. 암호화 알고리즘은 *TripleDES-SHA1*여야 합니다. 암호를 입력하여 확인하고, **다음**을 선택합니다. 이 암호는 다음 섹션에서 관리되는 도메인에 보안 LDAP를 사용하도록 설정하는 데 사용됩니다.
1. **내보낼 파일** 페이지에서 인증서를 내보낼 파일 이름과 위치(예: *C:\Users\accountname\azure-ad-ds.pfx*)를 지정합니다. 이 정보는 다음 단계에서 필요하므로 *.PFX* 파일의 암호와 위치를 기록해 둡니다.
1. 검토 페이지에서 **마침**을 선택하여 인증서를 *.PFX* 인증서 파일로 내보냅니다. 인증서를 성공적으로 내보내면 확인 대화 상자가 표시됩니다.
1. 다음 섹션에서 사용할 수 있도록 MMC를 열어 둡니다.

### <a name="export-a-certificate-for-client-computers"></a>클라이언트 컴퓨터에 대한 인증서 내보내기

클라이언트 컴퓨터는 LDAPS를 사용하여 관리되는 도메인에 성공적으로 연결할 수 있도록 보안 LDAP 인증서의 발급자를 신뢰해야 합니다. 클라이언트 컴퓨터에는 Azure AD DS에서 암호 해독할 데이터를 성공적으로 암호화하기 위해 인증서가 필요합니다. 퍼블릭 CA를 사용하는 경우 컴퓨터는 이러한 인증서 발급자를 자동으로 신뢰하고 해당 인증서를 갖추고 있어야 합니다.

이 자습서에서는 자체 서명된 인증서를 사용하고, 이전 단계에서 프라이빗 키가 포함된 인증서를 생성했습니다. 이제 자체 서명된 인증서를 내보낸 다음, 클라이언트 컴퓨터의 신뢰할 수 있는 인증서 저장소로 설치해 보겠습니다.

1. MMC의 *인증서 - 로컬 컴퓨터 > 개인 > 인증서* 저장소로 돌아갑니다. 이전 단계에서 만든 자체 서명된 인증서(예: *aaddscontoso.com*)가 표시됩니다. 마우스 오른쪽 단추로 이 인증서를 선택한 다음, **모든 작업 > 내보내기...** 를 차례로 선택합니다.
1. **인증서 내보내기 마법사**에서 **다음**을 선택합니다.
1. 클라이언트에 대한 프라이빗 키가 필요하지 않으므로 **프라이빗 키 내보내기** 페이지에서 **아니요, 프라이빗 키를 내보내지 않습니다.** , **다음**을 차례로 선택합니다.
1. **파일 형식 내보내기** 페이지에서 내보내는 인증서의 파일 형식으로 **64로 인코딩된 X.509(.CER)** 를 선택합니다.

    ![인증서를 Base-64로 인코딩된 X.509(.CER) 파일 형식으로 내보내는 옵션 선택](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. **내보낼 파일** 페이지에서 인증서를 내보낼 파일 이름과 위치(예: *C:\Users\accountname\azure-ad-ds-client.cer*)를 지정합니다.
1. 검토 페이지에서 **마침**을 선택하여 인증서를 *.CER* 인증서 파일로 내보냅니다. 인증서를 성공적으로 내보내면 확인 대화 상자가 표시됩니다.

이제 *.CER* 인증서 파일을 관리되는 도메인에 대한 보안 LDAP 연결을 신뢰해야 하는 클라이언트 컴퓨터에 배포할 수 있습니다. 인증서를 로컬 컴퓨터에 설치해 보겠습니다.

1. 파일 탐색기를 열고, *CER* 인증서 파일을 저장한 위치(예: *C:\Users\accountname\azure-ad-ds-client.cer*)를 찾습니다.
1. 마우스 오른쪽 단추로 *.CER* 인증서 파일을 선택한 다음, **인증서 설치**를 선택합니다.
1. **인증서 가져오기 마법사**에서 인증서를 *로컬 머신*에 저장하도록 선택하고, **다음**을 선택합니다.

    ![인증서를 로컬 머신 저장소로 가져오는 옵션 선택](./media/tutorial-configure-ldaps/import-cer-file.png)

1. 메시지가 표시되면 **예**를 선택하여 컴퓨터에서 변경하도록 허용합니다.
1. **인증서 종류를 기준으로 인증서 저장소를 자동으로 선택**, **다음**을 차례로 선택합니다.
1. 검토 페이지에서 **마침**을 선택하여 *.CER* 인증서를 가져옵니다. 인증서를 성공적으로 가져오면 확인 대화 상자가 표시됩니다.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Azure AD DS에서 보안 LDAP를 사용하도록 설정

프라이빗 키가 포함된 디지털 인증서를 만들어 내보내고 클라이언트 컴퓨터에서 연결을 신뢰하도록 설정했으면 이제 관리되는 도메인에서 보안 LDAP를 사용하도록 설정합니다. 관리되는 도메인에서 보안 LDAP를 사용하도록 설정하려면 다음 구성 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 검색** 상자에 *도메인 서비스*를 입력합니다. 검색 결과에서 **Azure AD Domain Services**를 선택합니다.
1. 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. Azure AD DS 창의 왼쪽에서 **보안 LDAP**를 선택합니다.
1. 기본적으로 관리되는 도메인에 대한 보안 LDAP 액세스는 사용하지 않도록 설정되어 있습니다. **보안 LDAP**를 **사용**으로 토글합니다.
1. 관리되는 도메인에 대한 인터넷을 통한 보안 LDAP 액세스는 기본적으로 사용하지 않도록 설정됩니다. 퍼블릭 보안 LDAP 액세스를 사용하도록 설정하면 도메인이 인터넷을 통한 무차별 암호 대입 공격(brute force attack)에 취약해질 수 있습니다. 다음 단계에서는 네트워크 보안 그룹에서 필요한 원본 IP 주소 범위에 대한 액세스만 잠그도록 구성됩니다.

    **인터넷을 통한 보안 LDAP 액세스 허용**을 **사용**으로 설정합니다.

1. **보안 LDAP 인증서가 포함된 .PFX 파일** 옆에 있는 폴더 아이콘을 선택합니다. *.PFX* 파일의 경로를 찾은 다음, 이전 단계에서 만든 프라이빗 키가 포함된 인증서를 선택합니다.

    > [!IMPORTANT]
    > 인증서 요구 사항에 대한 이전 섹션에서 설명한 대로 기본 *.onmicrosoft.com* 도메인이 있는 퍼블릭 CA의 인증서는 사용할 수 없습니다. Microsoft에서 *.onmicrosoft.com* 도메인을 소유하고 있으므로 퍼블릭 CA는 인증서를 발급하지 않습니다.
    >
    > 인증서가 적절한 형식인지 확인합니다. 그렇지 않은 경우 보안 LDAP를 사용하도록 설정하면 Azure 플랫폼에서 인증서 유효성 검사 오류가 생성됩니다.

1. 인증서를 *.PFX* 파일로 내보낸 이전 단계에서 설정한 **.PFX 파일을 해독하기 위한 암호**를 입력합니다.
1. **저장**을 선택하여 보안 LDAP를 사용하도록 설정합니다.

    ![Azure Portal을 통해 관리되는 도메인에서 보안 LDAP를 사용하도록 설정](./media/tutorial-configure-ldaps/enable-ldaps.png)

관리되는 도메인에 보안 LDAP를 구성하고 있다는 알림이 표시됩니다. 이 작업이 완료될 때까지 관리되는 도메인에 대한 다른 설정은 수정할 수 없습니다.

관리되는 도메인에서 보안 LDAP를 사용하도록 설정하는 데 몇 분 정도 걸립니다. 제공한 보안 LDAP 인증서가 필요한 조건과 일치하지 않으면 관리되는 도메인에서 보안 LDAP를 사용하도록 설정하는 작업이 실패합니다.

도메인 이름이 올바르지 않거나, 인증서의 암호화 알고리즘이 *TripleDES-SHA1*이 아니거나, 인증서가 곧 만료되거나 이미 만료된 경우 몇 가지 일반적인 오류가 발생합니다. 유효한 매개 변수를 사용하여 인증서를 다시 만든 다음, 이 업데이트된 인증서를 사용하여 보안 LDAP를 사용하도록 설정할 수 있습니다.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>인터넷을 통한 보안 LDAP 액세스 잠금

관리되는 도메인에 대한 인터넷을 통한 보안 LDAP 액세스를 사용하도록 설정하면 보안 위협이 만들어집니다. 관리되는 도메인은 636 TCP 포트의 인터넷에서 연결할 수 있습니다. 관리되는 도메인에 대한 액세스를 사용자 환경의 알려진 특정 IP 주소로 제한하는 것이 좋습니다. Azure 네트워크 보안 그룹 규칙을 사용하여 보안 LDAP에 대한 액세스를 제한할 수 있습니다.

지정된 IP 주소 세트에서 636 TCP 포트를 통한 인바운드 보안 LDAP 액세스를 허용하는 규칙을 만들어 보겠습니다. 우선 순위가 낮은 기본 *DenyAll* 규칙은 인터넷의 다른 모든 인바운드 트래픽에 적용되므로 지정된 주소만 보안 LDAP를 사용하여 관리되는 도메인에 연결할 수 있습니다.

1. Azure Portal의 왼쪽 탐색 영역에서 *리소스 그룹*을 선택합니다.
1. 리소스 그룹(예: *myResourceGroup*)을 선택한 다음, 네트워크 보안 그룹(*aaads-nsg*)을 선택합니다.
1. 기존 인바운드 및 아웃바운드 보안 규칙의 목록이 표시됩니다. 네트워크 보안 그룹 창의 왼쪽에서 **설정 > 인바운드 보안 규칙**을 차례로 선택합니다.
1. **추가**를 선택한 다음, *636* *TCP* 포트를 허용하는 규칙을 만듭니다. 보안을 강화하려면 *IP 주소*로 원본을 선택한 다음, 조직에 대한 사용자 고유의 유효한 IP 주소 또는 범위를 지정합니다.

    | 설정                           | 값        |
    |-----------------------------------|--------------|
    | 원본                            | IP 주소 |
    | 원본 IP 주소/CIDR 범위 | 사용자 환경에 유효한 IP 주소 또는 범위 |
    | 원본 포트 범위                | *            |
    | 대상                       | 모두          |
    | 대상 포트 범위           | 636          |
    | 프로토콜                          | TCP          |
    | 작업                            | Allow        |
    | 우선 순위                          | 401          |
    | Name                              | AllowLDAPS   |

1. 준비가 되면 **추가**를 선택하여 규칙을 저장하고 적용합니다.

    ![인터넷을 통한 LDAPS 액세스를 보호하는 네트워크 보안 그룹 규칙 만들기](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>외부 액세스를 위한 DNS 영역 구성

인터넷을 통한 보안 LDAP 액세스를 사용하도록 설정하면 클라이언트 컴퓨터에서 이 관리되는 도메인을 찾을 수 있도록 DNS 영역을 업데이트합니다. *보안 LDAP 외부 IP 주소*가 관리되는 도메인의 **속성** 탭에 나열됩니다.

![Azure Portal에서 관리되는 도메인의 보안 LDAP 외부 IP 주소 보기](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

이 외부 IP 주소로 확인할 호스트 레코드(예: *ldaps*)를 만들도록 외부 DNS 공급자를 구성합니다. 먼저 머신에서 로컬로 테스트하기 위해 항목을 Windows 호스트 파일에 만들 수 있습니다. 로컬 머신의 호스트 파일을 성공적으로 편집하려면 *메모장*을 관리자 권한으로 연 다음, *C:\Windows\System32\drivers\etc\hosts* 파일을 엽니다.

외부 DNS 공급자 또는 로컬 호스트 파일에 있는 다음 DNS 항목 예제에서는 *ldaps.aaddscontoso.com*의 트래픽을 *168.62.205.103*의 외부 IP 주소로 확인합니다.

```
168.62.205.103    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>관리되는 도메인에 대한 쿼리 테스트

관리되는 도메인에 연결하여 바인딩하고 LDAP를 검색하려면 *LDP.exe* 도구를 사용합니다. 이 도구는 RSAT(원격 서버 관리 도구) 패키지에 포함되어 있습니다. 자세한 내용은 [원격 서버 관리 도구 설치][rsat]를 참조하세요.

1. *LDP.exe*를 열고 관리되는 도메인에 연결합니다. **연결**, **연결...** 을 차례로 선택합니다.
1. 이전 단계에서 만든 관리되는 도메인의 보안 LDAP DNS 도메인 이름(예: *ldaps.aaddscontoso.com*)을 입력합니다. 보안 LDAP를 사용하려면 **포트**를 *636*으로 설정한 다음, **SSL** 확인란을 선택합니다.
1. **확인**을 선택하여 관리되는 도메인에 연결합니다.

다음으로 관리되는 도메인에 바인딩합니다. 관리되는 도메인에서 NTLM 암호 해시 동기화를 사용하지 않도록 설정한 경우 사용자(및 서비스 계정)는 LDAP 단순 바인딩을 수행할 수 없습니다. NTLM 암호 해시 동기화를 사용하지 않도록 설정하는 방법에 대한 자세한 내용은 [관리되는 도메인 보호][secure-domain]를 참조하세요.

1. **연결** 메뉴 옵션, **바인딩...** 을 차례로 선택합니다.
1. 관리되는 도메인에 속한 사용자 계정의 자격 증명을 제공합니다. 사용자 계정의 암호를 입력한 다음, 도메인(예: *aaddscontoso.com*)을 입력합니다.
1. **바인드 종류**에 대해 *자격 증명으로 바인딩* 옵션을 선택합니다.
1. **확인**을 선택하여 관리되는 도메인에 바인딩합니다.

관리되는 도메인에 저장된 개체를 확인하려면 다음을 수행합니다.

1. **보기** 메뉴 옵션, **트리**를 차례로 선택합니다.
1. *BaseDN* 필드를 비워 둔 다음, **확인**을 선택합니다.
1. 컨테이너(예: *AADDC Users*)를 선택한 다음, 마우스 오른쪽 단추로 컨테이너를 선택하고, **검색**을 선택합니다.
1. 미리 채워진 필드를 설정한 상태로 둔 다음, **실행**을 선택합니다. 다음 예제 출력에 표시된 것처럼 쿼리 결과가 오른쪽 창에 표시됩니다.

    ![LDP.exe를 사용하여 관리되는 도메인에서 개체 검색](./media/tutorial-configure-ldaps/ldp-query.png)

특정 컨테이너를 직접 쿼리하려면 **보기 > 트리** 메뉴에서 **BaseDN**(예: *OU=AADDC Users,DC=AADDSCONTOSO,DC=COM* 또는 *OU=AADDC Computers,DC=AADDSCONTOSO,DC=COM*)을 지정할 수 있습니다. 쿼리를 형식 지정하고 만드는 방법에 대한 자세한 내용은 [LDAP 쿼리 기본 사항][ldap-query-basics]을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 연결을 테스트하기 위해 DNS 항목을 컴퓨터의 로컬 호스트 파일에 추가한 경우 이 항목을 제거하고 정식 레코드를 DNS 영역에 추가합니다. 로컬 호스트 파일에서 항목을 제거하려면 다음 단계를 수행합니다.

1. 로컬 머신에서 *메모장*을 관리자 권한으로 엽니다.
1. *C:\Windows\System32\drivers\etc\hosts* 파일을 찾아서 엽니다.
1. 추가한 레코드에 대한 줄(예: `168.62.205.103    ldaps.aaddscontoso.com`)을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure AD DS에 사용할 디지털 인증서 만들기
> * Azure AD DS에서 보안 LDAP를 사용하도록 설정
> * 퍼블릭 인터넷을 통해 사용할 보안 LDAP 구성
> * 관리되는 도메인에 대한 보안 LDAP 바인딩 및 테스트

> [!div class="nextstepaction"]
> [하이브리드 Azure AD 환경에 대한 암호 해시 동기화 구성](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate
