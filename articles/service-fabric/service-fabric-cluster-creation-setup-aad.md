---
title: 클라이언트 인증에 대한 Azure Active Directory 설정
description: Service Fabric 클러스터에 대한 클라이언트를 인증하려면 Azure AD(Azure Active Directory)를 설정하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 6/28/2019
ms.openlocfilehash: 2a6ffdb1c1fdc447545477286a6d131be2449cdb
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843823"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>클라이언트 인증에 대한 Azure Active Directory 설정

Azure에서 실행 중인 클라이언트의 경우 관리 엔드포인트에 대한 액세스를 보호하려면 Azure AD(Azure Active Directory)가 좋습니다. 이 문서에서는 Service Fabric 클러스터에 대해 클라이언트를 인증 하기 위해 Azure AD를 설정 하는 방법을 설명 합니다.

이 문서에서 "응용 프로그램" 이라는 용어는 Service Fabric 응용 프로그램이 아닌 [Azure Active Directory 응용 프로그램](../active-directory/develop/developer-glossary.md#client-application)을 참조 하는 데 사용 됩니다. 필요한 경우 구분 됩니다. 조직(테넌트)에서는 Azure AD를 사용하여 애플리케이션에 대한 사용자 액세스를 관리할 수 있습니다.

Service Fabric 클러스터는 웹 기반 [Service Fabric Explorer][service-fabric-visualizing-your-cluster] 및 [Visual Studio][service-fabric-manage-application-in-visual-studio]를 포함하여 관리 기능에 대한 몇 가지 진입점을 제공합니다. 결과적으로 클러스터에 대 한 액세스를 제어 하는 두 개의 Azure AD 응용 프로그램 (웹 응용 프로그램과 네이티브 응용 프로그램)을 만듭니다. 응용 프로그램을 만든 후에는 읽기 전용 및 관리자 역할에 사용자를 할당 합니다.

> [!NOTE]
> Linux에서 클러스터를 만들기 전에 다음 단계를 완료 해야 합니다. Windows에는 [기존 클러스터에 대해 AZURE AD 인증을 구성](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md)하는 옵션도 있습니다.

> [!NOTE]
> Azure Portal에서 Linux AAD 지원 클러스터의 응용 프로그램 및 노드를 볼 수 없는 [알려진 문제](https://github.com/microsoft/service-fabric/issues/399) 입니다.



## <a name="prerequisites"></a>필수 조건
이 문서에서는 이미 테넌트를 만들었다고 가정합니다. 그렇지 않은 경우 [Azure Active Directory 테넌트를 가져오는 방법][active-directory-howto-tenant]을 참조하세요.

Service Fabric 클러스터로 Azure AD를 구성하는 데 포함되는 일부 단계를 단순화하기 위해 Windows PowerShell 스크립트 집합을 만들었습니다.

1. 컴퓨터에 [리포지토리를 복제](https://github.com/Azure-Samples/service-fabric-aad-helpers) 합니다.
2. 스크립트에 대 한 [모든 필수 구성 요소가 설치 되어 있는지 확인](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) 합니다.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure AD 애플리케이션 만들기 및 역할에 사용자 할당

스크립트를 사용 하 여 클러스터에 대 한 액세스를 제어 하는 두 개의 Azure AD 응용 프로그램 (웹 응용 프로그램과 네이티브 응용 프로그램)을 만듭니다. 클러스터를 나타내는 응용 프로그램을 만든 후 Service Fabric: 읽기 전용 및 관리자에 [의해 지원 되는 역할](service-fabric-cluster-security-roles.md)에 대 한 사용자를 만듭니다.

`SetupApplications.ps1`을 실행하고 테넌트 ID, 클러스터 이름 및 웹 애플리케이션 회신 URL을 매개 변수로 제공합니다.  또한 사용자의 사용자 이름과 암호를 지정합니다. 예:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> 국가별 클라우드(예: Azure Government, Azure 중국, Azure 독일)의 경우 `-Location` 매개 변수도 지정해야 합니다.

PowerShell 명령 `Get-AzureSubscription`을 실행하여 *TenantId*를 찾을 수 있습니다. 이 명령을 실행하면 모든 구독에 대한 TenantId가 표시됩니다.

*ClusterName*은 스크립트로 만든 Azure AD 애플리케이션의 접두사로 사용됩니다. 실제 클러스터 이름과 정확히 일치할 필요는 없습니다. 단순히 Azure AD 아티팩트를, 함께 사용할 Service Fabric 패브릭 클러스터에 쉽게 매핑하기 위한 것입니다.

*WebApplicationReplyUrl*은 Azure AD에서 로그인을 마친 후에 사용자에게 반환하는 기본 엔드포인트입니다. 이 엔드포인트을 클러스터에 대 한 Service Fabric Explorer 엔드포인트으로 설정 합니다. 기존 클러스터를 나타내는 Azure AD 응용 프로그램을 만드는 경우이 URL이 기존 클러스터의 엔드포인트과 일치 하는지 확인 합니다. 새 클러스터에 대 한 응용 프로그램을 만드는 경우 클러스터에 포함 되는 엔드포인트을 계획 하 고 기존 클러스터의 엔드포인트을 사용 하지 않도록 해야 합니다. 기본적으로 Service Fabric Explorer 엔드포인트은 다음과 같습니다.

https://&lt;cluster_domain&gt;:19080/Explorer

Azure AD 테넌트에 대한 관리자 권한이 있는 계정으로 로그인하라는 메시지가 표시됩니다. 로그인한 후에는 스크립트가 Service Fabric 클러스터를 나타내는 웹 및 네이티브 애플리케이션을 만듭니다. [Azure Portal][azure-portal]에서 테넌트의 애플리케이션을 살펴보면 다음 두 개의 새 항목이 표시되어 있습니다.

   * *ClusterName*\_클러스터
   * *ClusterName*\_클라이언트

이 스크립트는 AAD를 사용 하는 [클러스터를 만들](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access)때 Azure Resource Manager 템플릿에 필요한 JSON을 인쇄 하므로 PowerShell 창을 계속 열어 두는 것이 좋습니다.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Azure Active Directory 설정 관련 문제 해결 도움말
Azure AD를 설정하고 사용하는 작업은 매우 어려울 수 있습니다. 문제를 디버깅하기 위해 수행할 수 있는 작업에 대한 일부 포인터는 다음과 같습니다.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer에 인증서를 선택하라는 메시지가 표시
#### <a name="problem"></a>문제
Service Fabric Explorer에서 Azure AD에 로그인한 후 브라우저가 홈 페이지로 돌아가지만 인증서를 선택하라는 메시지가 표시됩니다.

![SFX 인증서 대화 상자][sfx-select-certificate-dialog]

#### <a name="reason"></a>이유
Azure AD 클러스터 애플리케이션에서 사용자에게 역할이 할당되지 않았습니다. 이 때문에 Service Fabric 클러스터에서 Azure AD 인증이 실패합니다. Service Fabric Explorer는 인증서 인증으로 대체됩니다.

#### <a name="solution"></a>솔루션
Azure AD 설정 지침을 따르고 사용자 역할을 할당합니다. 또한 `SetupApplications.ps1`에서처럼 “앱에 액세스하려면 사용자 할당 필요”를 살펴보는 것이 좋습니다.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>“지정된 자격 증명이 올바르지 않다”는 오류가 표시되면서 PowerShell 연결 실패
#### <a name="problem"></a>문제
PowerShell을 사용하여 “AzureActiveDirectory”보안 모드로 클러스터에 연결할 때, Azure AD 로그인 후 연결이 실패하고 “지정된 자격 증명이 올바르지 않다"는 오류가 표시됨

#### <a name="solution"></a>솔루션
이 솔루션은 이전과 동일합니다.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>로그인할 때 Service Fabric Explorer가 실패를 반환함: "AADSTS50011"
#### <a name="problem"></a>문제
Service Fabric Explorer에서 Azure AD에 로그인할 때 페이지가 "AADSTS50011: 회신 주소 &lt;url&gt;이 애플리케이션 &lt;guid&gt;에 대해 구성된 회신 주소와 일치하지 않습니다."라는 실패를 반환합니다.

![SFX 회신 주소가 일치하지 않습니다.][sfx-reply-address-not-match]

#### <a name="reason"></a>이유
Service Fabric Explorer를 나타내는 클러스터(웹) 애플리케이션이 Azure AD에 대해 인증을 시도하며, 해당 요청의 일부로 리디렉션 반환 URL을 제공합니다. 그렇지만 Azure AD 애플리케이션 **REPLY URL** 목록에 표시되지 않습니다.

#### <a name="solution"></a>솔루션
Azure AD 페이지에서 **앱 등록**를 선택 하 고 클러스터 응용 프로그램을 선택한 다음 **회신 url**을 선택 합니다. **회신 url** 창에서 목록에 Service Fabric Explorer URL을 추가 하거나 목록의 항목 중 하나를 바꿉니다. 변경 내용을 저장합니다.

![웹 응용 프로그램 회신 URL][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>PowerShell을 통해 Azure AD 인증을 사용 하 여 클러스터에 연결 하면 "AADSTS50011"에 로그인 할 때 오류가 발생 합니다.
#### <a name="problem"></a>문제
PowerShell을 통해 Azure AD를 사용 하 여 Service Fabric 클러스터에 연결 하려고 할 때 로그인 페이지에서 오류를 반환 합니다. "AADSTS50011: 요청에 지정 된 회신 url이 응용 프로그램에 대해 구성 된 회신 url과 일치 하지 않습니다. &lt;guid&gt;."

#### <a name="reason"></a>이유
위의 문제와 마찬가지로 PowerShell은 azure ad에 대해 인증을 시도 하며,이는 Azure ad 응용 프로그램 **회신 url** 목록에 나열 되지 않은 리디렉션 URL을 제공 합니다.  

#### <a name="solution"></a>솔루션
이전 문제와 동일한 프로세스를 사용 하지만 URL은 명령줄 인증에 대 한 특별 한 리디렉션과 `urn:ietf:wg:oauth:2.0:oob`로 설정 되어야 합니다.

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>PowerShell 통해 Azure AD 인증을 사용하여 클러스터 연결
Service Fabric 클러스터에 연결하려면 다음 PowerShell 명령 예제를 사용합니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

자세한 내용은 [Connect-ServiceFabricCluster cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster)를 참조하세요.

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>여러 클러스터에서 동일한 Azure AD테넌트를 다시 사용할 수 있나요?
예. 그렇지만 Service Fabric Explorer의 URL을 클러스터(웹) 애플리케이션에 추가해야 합니다. 그러지 않으면 Service Fabric Explorer가 작동하지 않습니다.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Azure AD가 사용되도록 설정된 경우에도 서버 인증서가 계속 필요한 이유는 무엇인가요?
FabricClient와 FabricGateway는 상호 인증을 수행합니다. Azure AD를 인증 하는 동안 Azure AD 통합은 서버에 클라이언트 id를 제공 하 고 서버 인증서는 클라이언트가 서버의 id를 확인 하는 데 사용 됩니다. 인증서 Service Fabric에 대 한 자세한 내용은 [x.509 certificate and Service Fabric][x509-certificates-and-service-fabric]를 참조 하십시오.

## <a name="next-steps"></a>다음 단계
Azure Active Directory 애플리케이션 및 사용자에 대한 역할을 설정한 후 [클러스터를 구성 및 배포합니다](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
