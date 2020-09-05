---
title: 비 대화형 인증 .NET 응용 프로그램-Azure HDInsight
description: Azure HDInsight에서 비대화형 인증 .NET HDInsight 애플리케이션을 만드는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/23/2019
ms.openlocfilehash: a696ba4f7bab85f2353e80d028220688531b3b69
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021768"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>비대화형 인증 .NET HDInsight 애플리케이션 만들기

응용 프로그램의 고유한 id (비 대화형) 또는 응용 프로그램의 로그인 한 사용자의 id (대화형)에서 Microsoft .NET Azure HDInsight 응용 프로그램을 실행 합니다. 이 문서에서는 비대화형 인증 .NET 애플리케이션을 만들어 Azure에 연결하고 HDInsight를 관리하는 방법을 보여줍니다. 대화형 애플리케이션의 샘플은 [Azure HDInsight에 연결](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight)을 참조하세요.

비대화형.NET 애플리케이션에서 다음 항목이 필요합니다.

* Azure 구독 테넌트 ID(*디렉터리 ID*라고도 함) - [테넌트 ID 가져오기](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)를 참조하세요.
* Azure AD(Azure Active Directory) 애플리케이션 클라이언트 ID - [Azure Active Directory 응용 프로그램 만들기](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 및 [응용 프로그램 ID 가져오기](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)를 참조 하세요.
* Azure AD 애플리케이션 비밀 키 - [응용 프로그램 인증 키 가져오기](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

HDInsight 클러스터. [시작 자습서](hadoop/apache-hadoop-linux-tutorial-get-started.md) 참조

## <a name="assign-a-role-to-the-azure-ad-application"></a>Azure AD 애플리케이션에 역할 할당

Azure AD 애플리케이션에 [역할](../role-based-access-control/built-in-roles.md)을 할당하여 작업을 수행할 권한을 부여합니다. 구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어 리소스 그룹에 대 한 읽기 권한자 역할에 응용 프로그램을 추가 하면 응용 프로그램이 리소스 그룹 및 해당 리소스 그룹의 모든 리소스를 읽을 수 있습니다. 이 문서에서는 리소스 그룹 수준에서 범위를 설정 합니다. 자세한 내용은 [역할 할당을 사용하여 Azure 구독 리소스에 대한 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.

**Azure AD 애플리케이션에 소유자 역할을 추가하려면**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 이 문서의 뒷부분에서 Hive 쿼리를 실행할 HDInsight 클러스터가 있는 리소스 그룹으로 이동 합니다. 많은 수의 리소스 그룹이 있는 경우 필터를 사용하여 원하는 리소스 그룹을 찾을 수 있습니다.
1. 리소스 그룹 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.
1. **역할 할당** 탭을 선택하여 현재의 역할 할당을 봅니다.
1. 페이지 위쪽에서 **+ 추가**를 선택 합니다.
1. 지침에 따라 Azure AD 애플리케이션에 소유자 역할을 추가합니다. 역할이 성공적으로 추가되면 애플리케이션이 소유자 역할 아래에 나열됩니다.

## <a name="develop-an-hdinsight-client-application"></a>HDInsight 클라이언트 애플리케이션 개발

1. C# 콘솔 애플리케이션을 만듭니다.
2. 다음 [NuGet](https://www.nuget.org/) 패키지를 추가 합니다.

    * `Install-Package Microsoft.Azure.Common.Authentication -Pre`
    * `Install-Package Microsoft.Azure.Management.HDInsight -Pre`
    * `Install-Package Microsoft.Azure.Management.Resources -Pre`

3. 다음 코드를 실행하세요.

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```

## <a name="next-steps"></a>다음 단계

* [Azure Portal에서 Azure Active Directory 애플리케이션 및 서비스 주체를 만듭니다](../active-directory/develop/howto-create-service-principal-portal.md).
* [Azure Resource Manager를 사용하여 서비스 주체를 인증](../active-directory/develop/howto-authenticate-service-principal-powershell.md)하는 방법을 알아봅니다.
* Azure [RBAC (역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md)에 대해 알아봅니다.
