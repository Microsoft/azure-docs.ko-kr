---
title: 자격 증명 관리에 ID Broker (미리 보기) 사용-Azure HDInsight
description: 도메인에 가입 된 Apache Hadoop 클러스터에 대 한 인증을 간소화 하기 위해 HDInsight ID Broker에 대해 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: 6ef76f3dafc02e89008ae164e3d868c628291766
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89075310"
---
# <a name="use-id-broker-preview-for-credential-management"></a>자격 증명 관리에 ID Broker (미리 보기) 사용

이 문서에서는 Azure HDInsight에서 ID Broker 기능을 설정 하 고 사용 하는 방법을 설명 합니다. 이 기능을 사용 하 여 Azure Multi-Factor Authentication를 통해 Apache Ambari에 로그인 하 고 Azure Active Directory Domain Services (Azure AD DS)에서 암호 해시가 없어도 필요한 Kerberos 티켓을 가져올 수 있습니다.

## <a name="overview"></a>개요

ID Broker는 다음과 같은 시나리오에서 복잡 한 인증을 단순화 합니다.

* 조직에서는 페더레이션에 의존 하 여 클라우드 리소스에 액세스 하는 사용자를 인증 합니다. 이전에는 Enterprise Security Package (ESP) 클러스터를 사용 하려면 온-프레미스 환경에서 암호 해시 동기화를 사용 하도록 설정 하 여 Azure Active Directory 해야 했습니다. 일부 조직에서는이 요구 사항이 어렵거나 바람직하지 않을 수 있습니다.

* 다른 인증 메커니즘을 사용 하는 기술을 사용 하는 솔루션을 구축 하 고 있습니다. 예를 들어 Apache Hadoop 및 Apache 레인저는 Kerberos를 사용 하지만 Azure Data Lake Storage는 OAuth를 사용 합니다.

ID Broker는 통합 인증 인프라를 제공 하 고 Azure AD DS에 암호 해시를 동기화 하는 요구 사항을 제거 합니다. ID Broker는 클러스터 게이트웨이 노드와 함께 Windows Server VM (ID 브로커 노드)에서 실행 되는 구성 요소로 구성 됩니다. 

다음 다이어그램에서는 ID Broker를 사용 하도록 설정한 후에 페더레이션된 사용자를 포함 하 여 모든 사용자에 대 한 인증 흐름을 보여 줍니다.

![ID Broker를 사용 하 여 인증 흐름](./media/identity-broker/identity-broker-architecture.png)

ID Broker를 사용 하면 암호를 제공 하지 않고 Multi-Factor Authentication를 사용 하 여 ESP 클러스터에 로그인 할 수 있습니다. Azure Portal와 같은 다른 Azure 서비스에 이미 로그인 한 경우에는 SSO (Single Sign-On) 환경을 사용 하 여 HDInsight 클러스터에 로그인 할 수 있습니다.

## <a name="enable-hdinsight-id-broker"></a>HDInsight ID Broker 사용

ID Broker를 사용 하는 ESP 클러스터를 만들려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. ESP 클러스터에 대 한 기본 만들기 단계를 따릅니다. 자세한 내용은 [ESP를 사용 하 여 HDInsight 클러스터 만들기](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)를 참조 하세요.
1. **HDINSIGHT ID Broker 사용**을 선택 합니다.

ID Broker 기능은 클러스터에 추가 VM 하나를 추가 합니다. 이 VM은 ID Broker 노드가 며 인증을 지 원하는 서버 구성 요소를 포함 합니다. ID Broker 노드는 Azure AD DS 도메인에 가입 된 도메인입니다.

![ID Broker를 사용 하도록 설정 하는 옵션](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 사용
다음 특성을 사용 하 여 라는 새 역할을 `idbrokernode` 템플릿의 계산 프로필에 추가 하면 ID broker 노드가 활성화 된 상태에서 클러스터가 생성 됩니다.

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 1,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

## <a name="tool-integration"></a>도구 통합

HDInsight [IntelliJ 플러그](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) 인이 OAuth를 지원 하도록 업데이트 되었습니다. 이 플러그 인을 사용 하 여 클러스터에 연결 하 고 작업을 제출할 수 있습니다.

[VS Code Spark & Hive 도구](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) 를 사용 하 여 노트북을 활용 하 고 작업을 제출할 수도 있습니다.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS에서 암호 해시가 없는 SSH 액세스

ID Broker를 사용 하도록 설정한 후에는 도메인 계정을 사용 하 여 SSH 시나리오에 대해 Azure AD DS에 저장 된 암호 해시가 여전히 필요 합니다. 도메인에 가입 된 VM으로 SSH 하거나 명령을 실행 하려면 암호를 `kinit` 제공 해야 합니다. 

SSH 인증을 사용 하려면 Azure AD DS에서 해시를 사용할 수 있어야 합니다. 관리 시나리오에만 SSH를 사용 하려는 경우 클라우드 전용 계정을 하나 만들어 클러스터에 대 한 SSH로 사용할 수 있습니다. 다른 사용자는 Azure AD DS에서 암호 해시를 사용할 수 없는 경우에도 Ambari 또는 HDInsight 도구 (예: IntelliJ 플러그 인)를 사용할 수 있습니다.

인증 문제를 해결 하려면이 [가이드](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues)를 참조 하세요.

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>OAuth를 사용 하 여 ID Broker 설정으로 HDInsight 게이트웨이에 연결 하는 클라이언트

ID broker 설정에서 게이트웨이에 연결 하는 사용자 지정 앱 및 클라이언트를 업데이트 하 여 필요한 OAuth 토큰을 먼저 획득할 수 있습니다. 이 [문서의](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) 단계에 따라 다음 정보를 포함 하는 토큰을 가져올 수 있습니다.

*   OAuth 리소스 uri: `https://hib.azurehdinsight.net` 
* AppId: 7865c1d2-f040-46cc-875f-831a1ef6a28a
*   권한: (이름: Cluster. ReadWrite, id: 8f89faa0-ffef-4007-974d-4989b39ad77d)

OAuth 토큰을 획득 한 후 클러스터 게이트웨이에 대 한 HTTP 요청에 대 한 인증 헤더 (예: <clustername> -int.azurehdinsight.net)에이 토큰을 사용할 수 있습니다. 예를 들어 livy API에 대 한 샘플 말아 명령은 다음과 같이 표시 될 수 있습니다.
    
```bash
curl -k -v -H "Authorization: TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By: UPN"
``` 

## <a name="next-steps"></a>다음 단계

* [Azure Active Directory Domain Services를 사용 하 여 Enterprise Security Package를 사용 하 여 HDInsight 클러스터 구성](apache-domain-joined-configure-using-azure-adds.md)
* [Azure Active Directory 사용자를 HDInsight 클러스터와 동기화](../hdinsight-sync-aad-users-to-cluster.md)
* [클러스터 성능 모니터링](../hdinsight-key-scenarios-to-monitor.md)
