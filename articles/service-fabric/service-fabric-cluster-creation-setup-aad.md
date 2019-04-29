---
title: Service Fabric 클라이언트 인증에 대한 Azure Active Directory 설정 | Microsoft Docs
description: Service Fabric 클러스터에 대한 클라이언트를 인증하려면 Azure AD(Azure Active Directory)를 설정하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/15/2019
ms.author: aljo
ms.openlocfilehash: c02e38880fdf8e8f1a2229f009b343d6431af853
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125139"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>클라이언트 인증에 대한 Azure Active Directory 설정

Azure에서 실행 중인 클라이언트의 경우 관리 엔드포인트에 대한 액세스를 보호하려면 Azure AD(Azure Active Directory)가 좋습니다.  이 문서에서는 Service Fabric 클러스터에 대한 클라이언트를 인증하려면 [클러스터를 만들기](service-fabric-cluster-creation-via-arm.md) 전에 수행해야 하는 Azure AD를 설정하는 방법에 대해 설명합니다.  조직(테넌트)에서는 Azure AD를 사용하여 애플리케이션에 대한 사용자 액세스를 관리할 수 있습니다. 애플리케이션은 웹 기반 로그인 UI를 갖는 항목과 네이티브 클라이언트 환경을 갖는 항목으로 나뉩니다. 

Service Fabric 클러스터는 웹 기반 [Service Fabric Explorer][service-fabric-visualizing-your-cluster] 및 [Visual Studio][service-fabric-manage-application-in-visual-studio]를 포함하여 관리 기능에 대한 여러 진입점을 제공합니다. 결과적으로 두 개의 Azure AD 애플리케이션(웹 애플리케이션과 네이티브 애플리케이션)을 만들어 클러스터에 대한 액세스를 제어합니다.  애플리케이션을 만든 후 읽기 전용 및 관리자 역할에 사용자를 할당합니다.

> [!NOTE]
> 클러스터를 만들기 전에 다음 단계를 완료해야 합니다. 스크립트는 클러스터 이름과 끈점을 예상하므로 이 값을 계획해야 하며, 이 값은 이미 만든 값이 아닙니다.

## <a name="prerequisites"></a>필수 조건
이 문서에서는 이미 테넌트를 만들었다고 가정합니다. 그러지 않은 경우 [Azure Active Directory 테넌트를 얻는 방법][active-directory-howto-tenant]을 참조하세요.

Service Fabric 클러스터로 Azure AD를 구성하는 데 포함되는 일부 단계를 단순화하기 위해 Windows PowerShell 스크립트 집합을 만들었습니다.

1. 컴퓨터에 [스크립트를 다운로드](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool)합니다.
2. zip 파일을 마우스 오른쪽 단추로 클릭하고 **속성**, **차단 해제** 확인란을 차례로 선택한 다음 **적용**을 클릭합니다.
3. zip 파일의 압축을 풉니다.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure AD 애플리케이션 만들기 및 역할에 사용자 할당
두 개의 Azure AD 애플리케이션(웹 애플리케이션 및 네이티브 애플리케이션)을 만들어 클러스터에 대한 액세스를 제어합니다. 클러스터를 나타내는 애플리케이션이 만들어지면 사용자를 [Service Fabric에서 지원하는 역할](service-fabric-cluster-security-roles.md)(읽기 전용 및 관리자)에 할당합니다.

`SetupApplications.ps1`을 실행하고 테넌트 ID, 클러스터 이름 및 웹 애플리케이션 회신 URL을 매개 변수로 제공합니다.  또한 사용자의 사용자 이름과 암호를 지정합니다.  예를 들면 다음과 같습니다.

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> 국가별 클라우드(예: Azure Government, Azure 중국, Azure 독일)의 경우 `-Location` 매개 변수도 지정해야 합니다.

PowerShell 명령 `Get-AzureSubscription`을 실행하여 *TenantId*를 찾을 수 있습니다. 이 명령을 실행하면 모든 구독에 대한 TenantId가 표시됩니다.

*ClusterName*은 스크립트로 만든 Azure AD 애플리케이션의 접두사로 사용됩니다. 실제 클러스터 이름과 정확히 일치할 필요는 없습니다. 단순히 Azure AD 아티팩트를, 함께 사용할 Service Fabric 패브릭 클러스터에 쉽게 매핑하기 위한 것입니다.

*WebApplicationReplyUrl*은 Azure AD에서 로그인을 마친 후에 사용자에게 반환하는 기본 엔드포인트입니다. 이 엔드포인트를 기본적으로 다음과 같은 클러스터에 대한 Service Fabric Explorer 엔드포인트로 설정합니다.

https://&lt;cluster_domain&gt;:19080/Explorer

Azure AD 테넌트에 대한 관리자 권한이 있는 계정으로 로그인하라는 메시지가 표시됩니다. 로그인한 후에는 스크립트가 Service Fabric 클러스터를 나타내는 웹 및 네이티브 애플리케이션을 만듭니다. [Azure Portal][azure-portal]에서 테넌트의 애플리케이션을 보면 두 개의 새 항목이 표시됩니다.

   * *ClusterName*\_클러스터
   * *ClusterName*\_클라이언트

이 스크립트는 [클러스터를 만들](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access) 때 Azure Resource Manager 템플릿에 필요한 JSON을 출력하므로 PowerShell 창을 계속 열어 두는 것이 좋습니다.

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

#### <a name="solution"></a>해결 방법
Azure AD 설정 지침을 따르고 사용자 역할을 할당합니다. 또한 `SetupApplications.ps1`에서처럼 “앱에 액세스하려면 사용자 할당 필요”를 살펴보는 것이 좋습니다.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>"지정한 자격 증명이 잘못되었습니다"라는 오류로 인해 PowerShell 연결이 실패함
#### <a name="problem"></a>문제
"AzureActiveDirectory" 보안 모드를 사용하여 PowerShell을 통해 클러스터에 연결할 때 Azure AD에 성공적으로 로그인하면 "지정한 자격 증명이 잘못되었습니다"라는 오류로 인해 연결이 실패합니다.

#### <a name="solution"></a>해결 방법
이 솔루션은 이전과 동일합니다.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>로그인하면 Service Fabric Explorer에서 "AADSTS50011" 오류를 반환함
#### <a name="problem"></a>문제
Service Fabric Explorer에서 Azure AD에 로그인하려고 하면 "AADSTS50011: &lt;url&gt; 회신 주소가 애플리케이션에 대해 구성된 &lt;guid&gt; 회신 주소와 일치하지 않습니다"라는 오류가 페이지에 반환됩니다.

![SFX 회신 주소가 일치하지 않습니다.][sfx-reply-address-not-match]

#### <a name="reason"></a>이유
Service Fabric Explorer를 나타내는 클러스터(웹) 애플리케이션이 Azure AD에 대해 인증을 시도하며, 해당 요청의 일부로 리디렉션 반환 URL을 제공합니다. 그렇지만 Azure AD 애플리케이션 **REPLY URL** 목록에 표시되지 않습니다.

#### <a name="solution"></a>해결 방법
AAD 페이지에서 “앱 등록”을 선택하고, 클러스터 애플리케이션을 선택한 다음, **회신 URL** 단추를 선택합니다. “회신 URL” 페이지에서 Service Fabric Explorer URL을 목록에 추가하거나 목록의 항목 중 하나를 대체합니다. 마친 후 변경 사항을 저장합니다.

![웹 애플리케이션 회신 URL][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>PowerShell 통해 Azure AD 인증을 사용하여 클러스터 연결
Service Fabric 클러스터에 연결하려면 다음 PowerShell 명령 예제를 사용합니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

자세한 내용은 [Connect-ServiceFabricCluster cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster)를 참조하세요.

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>여러 클러스터에서 동일한 Azure AD테넌트를 다시 사용할 수 있나요?
예. 그렇지만 Service Fabric Explorer의 URL을 클러스터(웹) 애플리케이션에 추가해야 합니다. 그러지 않으면 Service Fabric Explorer가 작동하지 않습니다.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Azure AD가 사용되도록 설정된 경우에도 서버 인증서가 계속 필요한 이유는 무엇인가요?
FabricClient와 FabricGateway는 상호 인증을 수행합니다. Azure AD 인증 중에는 Azure AD 통합은 서버에 클라이언트 ID를 제공하고 서버 인증서가 서버 ID를 확인하는 데 사용됩니다. Service Fabric 인증서에 대한 자세한 내용은 [X.509 인증서 및 Service Fabric][x509-certificates-and-service-fabric]을 참조하세요.

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
