---
title: 자습서 - Azure Front Door용 사용자 지정 도메인에 HTTPS 구성 | Microsoft Docs
description: 이 자습서에서는 사용자 지정 도메인에 대한 Azure Front Door 구성에서 HTTPS를 활성화하거나 비활성화하는 방법을 알아봅니다.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2018
ms.author: duau
ms.openlocfilehash: 3956a843e67dba82486f350fc4380d4c8f6065f1
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399823"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>자습서: Front Door 사용자 지정 도메인에서 HTTPS 구성

이 자습서에서는 프런트 엔드 호스트 섹션의 Front Door에 연결된 사용자 지정 도메인에 HTTPS 프로토콜을 사용하도록 설정하는 방법을 보여줍니다. 사용자 지정 도메인에서 HTTPS 프로토콜을 사용하면(예: https:\//www.contoso.com) 인터넷을 통해 중요한 데이터를 보낼 때 TLS/SSL 암호화를 통해 안전하게 보호됩니다. 웹 브라우저가 HTTPS를 통해 웹 사이트에 연결되면 웹 사이트 보안 인증서의 유효성을 검사하고 합법적인 인증 기관에서 발급되었는지를 확인합니다. 이 프로세스는 보안을 제공하며 공격으로부터 웹 애플리케이션을 보호합니다.

Azure Front Door는 기본적으로 Front Door 기본 호스트 이름에 HTTPS를 지원합니다. 예를 들어 Front Door(예: `https://contoso.azurefd.net`)를 만들면 `https://contoso.azurefd.net`에 수행되는 요청에 대해 HTTPS가 자동으로 활성화됩니다. 그러나 사용자 지정 도메인 'www.contoso.com'을 등록하고 나면 이 프런트 엔드 호스트에 HTTPS를 별도로 활성화해야 합니다.   

사용자 지정 HTTPS 기능의 몇 가지 주요 특성은 다음과 같습니다.

- 추가 비용 없음: 인증서 얻기 또는 갱신에 대해 비용이 없으며 HTTPS 트래픽에 대한 추가 비용이 없습니다. 

- 간단한 사용: [Azure Portal](https://portal.azure.com)에서 한 번 클릭으로 프로비전을 사용할 수 있습니다. REST API 또는 기타 개발자 도구를 사용하여 기능을 활성화할 수도 있습니다.

- 완전한 인증서 관리: 사용자를 위해 모든 인증서 조달 및 관리가 처리됩니다. 만료되기 전에 인증서가 자동으로 프로비전되고 갱신되므로 인증서 만료로 인해 서비스가 중단될 위험이 없습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - 사용자 지정 도메인에서 HTTPS 프로토콜을 사용하도록 설정
> - AFD 관리 인증서 사용 
> - 사용자 고유의 인증서, 즉 사용자 지정 TLS/SSL 인증서 사용
> - 도메인의 유효성 검사
> - 사용자 지정 도메인에서 HTTPS 프로토콜을 사용하지 않도록 설정


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서 단계를 완료하기 전에 먼저 Front Door를 만들고 하나 이상의 사용자 지정 도메인을 등록해야 합니다. 자세한 내용은 [자습서: Front Door에 사용자 지정 도메인 추가](front-door-custom-domain.md)를 참조하세요.

## <a name="tlsssl-certificates"></a>TLS/SSL 인증서

Front Door 사용자 지정 도메인에서 콘텐츠를 안전하게 제공하기 위해 HTTPS 프로토콜을 활성화하려면 TLS/SSL 인증서를 사용해야 합니다. Azure Front Door에서 관리되는 인증서를 사용하거나 사용자 고유의 인증서를 사용하도록 선택할 수 있습니다.


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>옵션 1(기본값): Front Door에서 관리되는 인증서 사용

Azure Front Door에서 관리되는 인증서를 사용하면 단 몇 번의 클릭으로 HTTPS 기능을 설정할 수 있습니다. Azure Front Door는 조달 및 갱신과 같은 인증서 관리 작업을 완전히 처리합니다. 기능을 활성화하면 프로세스는 즉시 시작됩니다. 사용자 지정 도메인이 Front Door의 기본 프런트 엔드 호스트(`{hostname}.azurefd.net`)에 이미 매핑된 경우 추가적인 조치가 필요하지 않습니다. Front Door는 단계를 처리하고 자동으로 요청을 완료합니다. 그러나 사용자 지정 도메인이 다른 곳에 매핑된 경우 이메일을 사용하여 도메인 소유권의 유효성을 검사해야 합니다.

사용자 지정 도메인에서 HTTPS를 활성화하려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com)에서 **Front Door** 프로필로 이동합니다.

2. 프런트 엔드 호스트 목록에서 사용자 지정 도메인을 포함하기 위해 HTTPS를 활성화하려는 사용자 지정 도메인을 선택합니다.

3. **사용자 지정 도메인 HTTPS** 섹션에서 **사용**을 클릭하고 인증서 원본으로 **Front Door 관리**를 선택합니다.

4. 저장을 클릭합니다.

5. [도메인의 유효성 검사](#validate-the-domain)를 진행합니다.

> [!NOTE]
> AFD 관리형 인증서의 경우 DigiCert의 64자 제한이 적용됩니다. 해당 제한을 초과하면 유효성 검사가 실패합니다.


### <a name="option-2-use-your-own-certificate"></a>옵션 2: 사용자 고유의 인증서 사용

사용자 고유의 인증서를 사용하여 HTTPS 기능을 활성화합니다. 이 프로세스는 인증서를 안전하게 저장할 수 있도록 하는 Azure Key Vault와의 통합을 통해 수행됩니다. Azure Front Door는 이 보안 메커니즘을 사용하여 인증서를 가져오며 몇 가지 추가 단계를 수행해야 합니다. TLS/SSL 인증서를 만들 때 허용된 CA(인증 기관)에서 만들어야 합니다. 그렇지 않고 허용되지 않는 CA를 사용하는 경우 요청이 거부됩니다. 허용되는 CA 목록은 [Azure Front Door에서 사용자 지정 HTTPS를 사용하기 위해 허용되는 인증 기관](front-door-troubleshoot-allowed-ca.md)을 참조하세요.

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Azure 키 자격 증명 모음 계정 및 인증서 준비
 
1. Azure Key Vault: 사용자 지정 HTTPS를 활성화하려는 Front Door와 동일한 구독에서 Azure Key Vault 계정을 실행해야 합니다. 아직 Azure Key Vault 계정이 없는 경우 새로 하나 만듭니다.

> [!WARNING]
> Azure Front Door는 현재 Front Door 구성과 동일한 구독의 Key Vault 계정만 지원합니다. Front Door와 다른 구독의 Key Vault를 선택하면 실패하게 됩니다.

2. Azure Key Vault 인증서: 인증서가 이미 있는 경우 Azure Key Vault 계정에 직접 업로드하거나 Azure Key Vault와 통합하는 파트너 CA 중 하나에서 Azure Key Vault를 통해 직접 새 인증서를 만들 수 있습니다. 인증서를 **비밀** 대신 **인증서** 개체로 업로드합니다.

> [!NOTE]
> 자체 TLS/SSL 인증서의 경우, Front Door는 EC 암호화 알고리즘을 사용하는 인증서를 지원하지 않습니다.

#### <a name="register-azure-front-door"></a>Azure Front Door 등록

PowerShell을 통해 Azure Active Directory에서 Azure Front Door의 서비스 사용자를 앱으로 등록합니다.

> [!NOTE]
> 이 작업을 수행하려면 글로벌 관리자 권한이 필요하며, 테넌트 당 **한 번**만 수행하면 됩니다.

1. 필요한 경우 로컬 컴퓨터의 PowerShell에 [Azure PowerShell](/powershell/azure/install-az-ps)을 설치합니다.

2. PowerShell에서 다음 명령을 실행합니다.

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>키 자격 증명 모음에 Azure Front Door 액세스 권한 부여
 
Azure Key Vault 계정의 인증서에 액세스하기 위한 Azure Front Door 사용 권한을 부여합니다.

1. 키 자격 증명 모음 계정의 설정에서 **액세스 정책**을 선택한 다음, **새로 추가**를 선택하여 새 정책을 만듭니다.

2. **주체 선택**에서 **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037**을 검색하고 **Microsoft.Azure.Frontdoor**를 선택합니다. **선택**을 클릭합니다.

3. **비밀 사용 권한**에서 **가져오기**를 선택하여 Front Door가 인증서를 검색하도록 허용합니다.

4. **인증서 사용 권한**에서 **가져오기**를 선택하여 Front Door가 인증서를 검색하도록 허용합니다.

5. **확인**을 선택합니다. 

    Azure Front Door는 이제 이 Key Vault에 저장된 이 Key Vault 및 인증서에 액세스할 수 있습니다.
 
#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>배포할 Azure Front Door 인증서 선택
 
1. 포털에서 Front Door로 돌아갑니다. 

2. 사용자 지정 도메인 목록에서 HTTPS를 사용하도록 설정할 사용자 지정 도메인을 선택합니다.

    **사용자 지정 도메인** 페이지가 나타납니다.

3. 인증서 관리 유형에서 **내 인증서 사용**을 선택합니다. 

4. Azure Front Door를 사용하려면 Key Vault 계정의 구독이 Front Door와 동일해야 합니다. 키 자격 증명 모음, 인증서(비밀)및 인증서 버전을 선택합니다.

    Azure Front Door는 다음과 같은 정보를 나열합니다. 
    - 구독 ID에 대한 키 자격 증명 모음 계정 
    - 선택한 키 자격 증명 모음의 인증서(비밀) 
    - 사용 가능한 인증서 버전 
 
5. 사용자 고유의 인증서를 사용할 경우 도메인 유효성 검사가 필요하지 않습니다. [전파 대기](#wait-for-propagation)를 진행합니다.

## <a name="validate-the-domain"></a>도메인의 유효성 검사

CNAME 레코드를 사용하여 사용자 지정 엔드포인트에 매핑되는 사용자 지정 도메인을 이미 사용 중이거나 사용자 고유의 인증서를 사용 중인 경우 다음을 진행합니다.  
[사용자 지정 도메인이 Front Door에 매핑됨](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record)을 진행합니다. 그렇지 않고 도메인의 CNAME 레코드 항목이 더 이상 존재하지 않거나 afdverify 하위 도메인을 포함하는 경우 [사용자 지정 도메인이 Front Door에 매핑되지 않음](#custom-domain-is-not-mapped-to-your-front-door)을 진행합니다.

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>CNAME 레코드를 통해 사용자 지정 도메인이 CNAME 레코드에 매핑됨

Front Door의 프런트 엔드 호스트에 사용자 지정 도메인을 추가한 경우 도메인 등록 기관의 DNS 테이블에 Front Door의 기본 .azurefd.net 호스트 이름에 매핑할 CNAME 레코드를 만들었습니다. 해당 CNAME 레코드가 여전히 있고 afdverify 하위 도메인을 포함하지 않는 경우 DigiCert 인증 기관은 이 레코드를 사용하여 사용자 지정 도메인의 소유권을 자동으로 확인합니다. 

사용자 고유의 인증서를 사용 중인 경우 도메인 유효성 검사가 필요하지 않습니다.

CNAME 레코드는 다음 형식이어야 합니다. 여기서 *Name*은 사용자 지정 도메인 이름이고 *Value*는 Front Door의 기본 .azurefd.net 호스트 이름입니다.

| 속성            | Type  | 값                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

CNAME 레코드에 대한 자세한 내용은 [CNAME DNS 레코드 만들기](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain)를 참조하세요.

CNAME 레코드가 올바른 형식이면 DigiCert는 사용자 지정 도메인 이름을 자동으로 확인하고 도메인 이름에 전용 인증서를 만듭니다. DigitCert는 확인 메일을 보내지 않으며 요청을 승인할 필요가 없습니다. 인증서는 1년 동안 유효하며 만료되기 전에 자동으로 갱신됩니다. [전파 대기](#wait-for-propagation)를 진행합니다. 

일반적으로 자동 유효성 검사에 몇 분 정도 걸립니다. 1시간 내에 도메인의 유효성이 확인되지 않으면 지원 티켓을 엽니다.

>[!NOTE]
>DNS 공급자가 포함된 CAA(Certificate Authority Authorization) 레코드가 있으면 DigiCert가 유효한 CA로 포함되어야 합니다. CAA 레코드를 사용하면 도메인 소유자가 자신의 도메인에 대한 인증서를 발급할 권한이 있는 CA를 DNS 공급자로 지정할 수 있습니다. CA에서 CAA 레코드가 있는 도메인에 대한 인증서 주문을 받고 해당 CA가 인증된 발급자로 나열되지 않은 경우 해당 도메인 또는 하위 도메인에 인증서를 발급할 수 없습니다. CAA 레코드 관리에 대한 내용은 [CAA 레코드 관리](https://support.dnsimple.com/articles/manage-caa-record/)를 참조하세요. CAA 레코드 도구는 [CAA 레코드 도우미](https://sslmate.com/caa/)를 참조하세요.

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>사용자 지정 도메인이 Front Door에 매핑되지 않음

엔드포인트에 대한 CNAME 레코드 항목이 더 이상 없거나 afdverify 하위 도메인을 포함하는 경우 이 단계의 나머지 지침을 따르세요.

사용자 지정 도메인에서 HTTPS를 활성화한 후에 DigiCert CA에서 도메인의 [WHOIS](http://whois.domaintools.com/) 등록자 정보에 따라 등록자에게 연락하여 도메인 소유권에 대한 유효성을 검사합니다. 기본적으로 WHOIS에 등록된 전자 메일 주소 또는 전화 번호를 통해 연락합니다. 사용자 지정 도메인에서 HTTPS를 활성화하기 전에 도메인 유효성 검사를 완료해야 합니다. 업무일 기준 6일 이내 도메인이 승인되어야 합니다. 6 영업일 이내에 승인되지 않은 요청은 자동으로 취소됩니다. 

![WHOIS 레코드](./media/front-door-custom-domain-https/whois-record.png)

또한 DigiCert는 추가 이메일 주소로 확인 전자 메일을 보냅니다. WHOIS 등록자 정보가 프라이빗인 경우 다음 주소 중 하나에서 직접 승인할 수 있는지 확인합니다.

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

잠시 후 다음 예제와 비슷한 승인 요청 전자 메일을 받게 됩니다. 스팸 필터를 사용하는 경우 admin@digicert.com을 허용 목록에 추가입니다. 24시간 이내에 전자 메일을 받지 못하면 Microsoft 지원에 문의하세요.

승인 링크를 클릭하는 경우 온라인 승인 양식으로 이동됩니다. 양식의 지침을 따르세요. 다음과 같이 두 가지 확인 옵션이 있습니다.

- 동일한 루트 도메인의 동일한 계정을 통해 이루어진 모든 향후 주문을 승인할 수 있습니다(예: consoto.com). 이 방법은 동일한 루트 도메인에 대한 추가 사용자 지정 도메인을 추가하려는 경우에 권장됩니다.

- 이 요청에 사용된 특정 호스트 이름을 승인할 수 있습니다. 후속 요청에는 추가 승인이 필요합니다.

승인 후에 DigiCert는 사용자 지정 도메인 이름에 대한 인증서 생성을 완료합니다. 인증서는 1년 동안 유효하며 만료되기 전 자동으로 갱신됩니다.

## <a name="wait-for-propagation"></a>전파 대기

도메인 이름이 확인된 후 사용자 지정 도메인 HTTPS 기능이 활성 상태가 될 때까지는 최대 6-8시간 소요됩니다. 프로세스가 완료되면 Azure Portal에서 사용자 지정 HTTPS 상태가 **사용**으로 설정되고 사용자 지정 도메인 대화 상자의 네 가지 작업 단계가 '완료'로 표시됩니다. 사용자 지정 도메인은 이제 HTTPS를 활성화할 준비가 되었습니다.

### <a name="operation-progress"></a>작업 진행 상태

다음 표에는 HTTPS를 활성화하도록 설정했을 때 나타나는 작업 진행률을 표시합니다. HTTPS를 사용하도록 설정하면 네 가지 작업 단계가 사용자 지정 도메인 대화 상자에 나타납니다. 각 단계가 활성화되면 진행되는 동안 단계 아래에 추가 하위 단계 정보가 표시됩니다. 이러한 하위 단계가 모두 발생하지는 않습니다. 단계가 성공적으로 완료되면 옆에 녹색 확인 표시가 나타납니다. 

| 작업 단계 | 작업 하위 단계 정보 | 
| --- | --- |
| 1 요청 제출 | 요청 제출 |
| | HTTPS 요청을 제출하는 중입니다. |
| | HTTPS 요청이 제출되었습니다. |
| 2 도메인 유효성 검사 | 도메인이 Front Door의 기본 .azurefd.net 프런트 엔드 호스트에 매핑된 CNAME인 경우 도메인의 유효성이 자동으로 검사됩니다. 그렇지 않으면 도메인의 등록 레코드(WHOIS 등록자)에 나열된 이메일로 확인 요청이 발송됩니다. 최대한 신속하게 도메인을 확인하세요. |
| | 도메인 소유권을 확인했습니다. |
| | 도메인 소유권 유효성 검사 요청이 만료되었습니다(고객이 6일 이내의 응답하지 않았음). 도메인에서 HTTPS를 활성화할 수 없습니다. * |
| | 고객에 의해 도메인 소유권 유효성 검사 요청이 거부되었습니다. 도메인에서 HTTPS를 활성화할 수 없습니다. * |
| 3 인증서 프로비전 | 인증 기관이 현재 도메인에서 HTTPS를 활성화하는 데 필요한 인증서를 발급 중입니다. |
| | 인증서가 발급되었고 현재 Front Door로 배포되고 있습니다. 이 프로세스는 최대 1시간까지 걸릴 수 있습니다. |
| | 인증서가 Front Door에 성공적으로 배포되었습니다. |
| 4 완료 | 도메인에서 HTTPS를 활성화하도록 설정했습니다. |

\* 오류가 발생하지 않으면 이 메시지가 표시되지 않습니다. 

요청을 제출하기 전에 오류가 발생하는 경우 다음과 같은 오류 메시지가 표시됩니다.

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>리소스 정리 - HTTPS를 사용하지 않도록 설정

이전 단계에서는 사용자 지정 도메인에 HTTPS 프로토콜을 사용했습니다. 더 이상 HTTPS에 사용자 지정 도메인을 사용하지 않으려면 다음 단계를 수행하여 HTTPS를 사용하지 않도록 설정하면 됩니다.

### <a name="disable-the-https-feature"></a>HTTPS 기능을 사용하지 않도록 설정 

1. [Azure Portal](https://portal.azure.com)에서 **Azure Front Door** 구성으로 이동합니다.

2. 프런트 엔드 호스트 목록에서 HTTPS를 비활성화하도록 설정할 사용자 지정 도메인을 클릭합니다.

3. **사용 안 함**을 클릭하여 HTTPS를 비활성화한 다음, **저장**을 클릭합니다.

### <a name="wait-for-propagation"></a>전파 대기

사용자 지정 도메인 HTTPS 기능을 비활성화한 후 최대 6-8시간까지 걸릴 수 있습니다. 프로세스가 완료되면 Azure Portal에서 사용자 지정 HTTPS 상태가 **사용 안 함**으로 설정되고 사용자 지정 도메인 대화 상자의 세 가지 작업 단계가 '완료'로 표시됩니다. 사용자 지정 도메인은 더 이상 HTTPS를 사용할 수 없습니다.

#### <a name="operation-progress"></a>작업 진행 상태

다음 표에는 HTTPS를 비활성화하도록 설정했을 때 나타나는 작업 진행률을 표시합니다. HTTPS를 사용하지 않도록 설정하면 세 가지 작업 단계가 사용자 지정 도메인 대화 상자에 나타납니다. 각 단계가 활성화되면 단계 아래에 추가 세부 정보가 표시됩니다. 단계가 성공적으로 완료되면 옆에 녹색 확인 표시가 나타납니다. 

| 작업 진행 상태 | 작업 세부 정보 | 
| --- | --- |
| 1 요청 제출 | 요청 제출 |
| 2 인증서 프로비전 해제 | 인증서 삭제 |
| 3 완료 | 인증서 삭제됨 |

## <a name="frequently-asked-questions"></a>질문과 대답

1. *인증서 공급자는 누구이며 어떤 유형의 인증서가 사용되나요?*

    Digicert에서 제공한 전용/단일 인증서가 사용자 지정 도메인에 사용됩니다. 

2. *IP 기반 또는 SNI TLS/SSL을 사용하나요?*

    Azure Front Door는 SNI TLS/SSL을 사용합니다.

3. *DigiCert로부터 도메인 확인 메일을 받지 못한 경우 어떻게 하나요?*

    엔드포인트 호스트 이름을 직접 가리키는 사용자 지정 도메인에 대한 CNAME 항목이 있고 afdverify 하위 도메인 이름을 사용하지 않는 경우에는 도메인 확인 메일이 수신되지 않습니다. 유효성 검사가 자동으로 수행됩니다. 그렇지 않은 경우 CNAME 항목이 없고 24시간 내에 이메일을 받지 못했으면 Microsoft 지원에 문의하세요.

4. *SAN 인증서를 사용하는 것보다 전용 인증서를 사용하는 것이 더 안전한가요?*
    
    SAN 인증서는 전용 인증서와 동일한 암호화 및 보안 표준을 따릅니다. 발급된 모든 TLS/SSL 인증서는 향상된 서버 보안을 위해 SHA-256을 사용합니다.

5. *내 DNS 공급자에게 CAA(Certificate Authority Authorization) 레코드가 필요합니까?*

    아니요, CAA 레코드는 현재 필요하지 않습니다. 그러나 이 레코드가 있으면 DigiCert가 유효한 CA로 포함되어야 합니다.


## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
