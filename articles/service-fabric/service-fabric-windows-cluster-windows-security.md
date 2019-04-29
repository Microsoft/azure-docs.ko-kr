---
title: Windows 보안을 사용하여 Windows에서 실행되는 클러스터 보안 | Microsoft Docs
description: Windows 보안을 사용하여 Windows에서 실행되는 독립 실행형 클러스터에서 노드 간 및 클라이언트-노드 보안을 구성하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 394ba3b3b8189bbe96137e920745f7b8cdd1cd95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60863966"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Windows 보안을 사용하여 독립 실행형 클러스터 보호
Service Fabric 클러스터에 대한 무단 액세스를 방지하려면 클러스터를 보호해야 합니다. 클러스터에서 프로덕션 작업을 실행하는 경우 특히 보안이 중요합니다. 이 문서에서는 *ClusterConfig.JSON* 파일에서 Windows 보안을 사용하여 노드 간 및 클라이언트-노드 간 보안을 구성하는 방법을 설명합니다.  이 프로세스는 보안[Windows에서 실행되는 독립 실행형 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)의 보안 단계 구성에 해당합니다. Service Fabric에서 Windows 보안을 사용하는 방법에 대한 자세한 내용은 [클러스터 보안 시나리오](service-fabric-cluster-security.md)를 참조하세요.

> [!NOTE]
> 보안 방식을 바꾸는 동안 클라이언트 업그레이드가 수행되지 않으므로 노드 간 보안을 선택할 때는 신중해야 합니다. 보안 선택을 변경하려면 전체 클러스터를 다시 작성해야 합니다.
>
>

## <a name="configure-windows-security-using-gmsa"></a>gMSA를 사용하여 Windows 보안 구성  
샘플 *ClusterConfig.gMSA.Windows.MultiMachine.JSON* 와 함께 다운로드 하는 구성 파일을 [Microsoft.Azure.ServiceFabric.WindowsServer.\< 버전 >.zip](https://go.microsoft.com/fwlink/?LinkId=730690) 독립 실행형 클러스터 패키지를 사용 하 여 Windows 보안 구성을 위한 템플릿을 포함 [그룹 관리 서비스 계정 (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {  
        "ClustergMSAIdentity": "[gMSA Identity]",
        "ClusterSPN": "[Registered SPN for the gMSA account]",
        "ClientIdentities": [
            {
                "Identity": "domain\\username",
                "IsAdmin": true
            }
        ]
    }
}
```

| **구성 설정** | **설명** |
| --- | --- |
| ClusterCredentialType |노드 간 통신에서 Windows 보안을 사용하도록 설정하려면 *Windows*로 설정합니다.  | 
| ServerCredentialType |클라이언트-노드 간 통신에서 Windows 보안을 사용하도록 설정하려면 *Windows*로 설정합니다. |
| WindowsIdentities |클러스터와 클라이언트 ID를 포함합니다. |
| ClustergMSAIdentity |노드 간 보안을 구성합니다. 그룹 관리 서비스 계정입니다. |
| ClusterSPN |gMSA 계정에 대해 등록된 SPN|
| ClientIdentities |클라이언트-노드 보안을 구성합니다. 클라이언트 사용자 계정의 배열입니다. |
| ID |클라이언트 ID로 도메인 사용자인 domain\username을 추가합니다. |
| IsAdmin |도메인 사용자가 관리자 클라이언트 액세스 권한을 갖는 경우 true로 설정하고, 사용자 클라이언트 액세스 권한을 갖는 경우 false를 설정합니다. |

> [!NOTE]
> ClustergMSAIdentity 값 도메인 이름이 포함 될 수 없습니다 및 그룹 관리 서비스 계정 이름 이어야 합니다. I.E. "mysfgmsa" 올바른지와 "mydomain / / mysfgmsa" 또는 "mysfgmsa@mydomain" 도메인 호스트 컴퓨터에서 암시적; 올바르지 않습니다.

[노드 간 보안](service-fabric-cluster-security.md#node-to-node-security)은 서비스 패브릭이 gMSA에서 실행되어야 하는 경우 **ClustergMSAIdentity**를 설정하여 구성됩니다. 노드 간의 신뢰 관계를 구축하기 위해 서로를 인식하도록 만들어야 합니다. 이 두 가지 방법으로 수행할 수 있습니다. 그룹 관리 서비스 계정을 지정 클러스터의 모든 노드를 포함 하거나 클러스터의 모든 노드를 포함 하는 도메인 컴퓨터 그룹을 지정 합니다. 특히 노드가 10개보다 많은 대형 클러스터 또는 확장되거나 축소될 수 있는 클러스터의 경우에는 [gMSA(그룹 관리 서비스 계정)](https://technet.microsoft.com/library/hh831782.aspx) 방식을 사용하는 것이 좋습니다.  
이 접근 방법에서는 클러스터 관리자에게 멤버를 추가하고 제거하는 액세스 권한을 부여하기 위해 도메인 그룹을 만들 필요가 없습니다. 이러한 계정은 자동 암호 관리에도 유용합니다. 자세한 내용은 [그룹 관리 서비스 계정 시작](https://technet.microsoft.com/library/jj128431.aspx)을 참조하세요.  
 
[클라이언트 및 노드 간 보안](service-fabric-cluster-security.md#client-to-node-security)은 **ClientIdentities**를 사용하여 구성합니다. 클라이언트와 클러스터 간에 트러스트를 설정하기 위해 신뢰할 수 있는 클라이언트 ID를 파악하도록 클러스터를 구성해야 합니다. 이 작업은 두 가지 방법으로 수행할 수 있습니다. 연결 하거나 연결할 수 있는 도메인 노드 사용자를 지정할 수 있는 도메인 그룹 사용자를 지정 합니다. 서비스 패브릭은 서비스 패브릭 클러스터에 연결된 클라이언트에 대해 관리자 및 사용자 액세스 제어 형식을 지원합니다. 액세스 제어는 클러스터 관리자가 사용자 그룹마다 특정 형식의 클러스터 작업에 대한 액세스를 제한하는 기능을 제공하여 클러스터의 보안을 강화할 수 있습니다.  관리자는 관리 기능(읽기/쓰기 기능만 포함)에 대한 모든 권한을 가집니다. 사용자는 기본적으로 관리 기능(예: 쿼리 기능)에 대한 읽기 권한 및 애플리케이션과 서비스를 확인하는 기능만 갖습니다. 액세스 제어에 대한 자세한 내용은 [Service Fabric 클라이언트의 역할 기반 액세스 제어](service-fabric-cluster-security-roles.md)를 참조하세요.  
 
다음 예제 **security** 섹션에서는 gMSA를 사용하는 Windows 보안을 구성하고 *ServiceFabric.clusterA.contoso.com* gMSA의 머신이 클러스터의 일부이며 *CONTOSO\usera*에 관리자 클라이언트 액세스 권한이 있음을 지정합니다.  
  
```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",
        "ClusterSPN" : "http/servicefabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
}
```
  
## <a name="configure-windows-security-using-a-machine-group"></a>컴퓨터 그룹을 사용하여 Windows 보안 구성  
이 모델은 더 이상 사용되지 않습니다. 위에서 설명한 대로 gMSA를 사용하는 것이 좋습니다. 샘플 *ClusterConfig.Windows.MultiMachine.JSON* 와 함께 다운로드 하는 구성 파일을 [Microsoft.Azure.ServiceFabric.WindowsServer.\< 버전 >.zip](https://go.microsoft.com/fwlink/?LinkId=730690) 독립 실행형 클러스터 패키지는 Windows 보안 구성을 위한 템플릿을 포함 합니다.  **Properties** 섹션에서 다음과 같이 Windows 보안을 구성합니다. 

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
        "ClientIdentities": [{
            "Identity": "[domain\username]",
            "IsAdmin": true
        }]
    }
}
```

| **구성 설정** | **설명** |
| --- | --- |
| ClusterCredentialType |노드 간 통신에서 Windows 보안을 사용하도록 설정하려면 *Windows*로 설정합니다.  |
| ServerCredentialType |클라이언트-노드 간 통신에서 Windows 보안을 사용하도록 설정하려면 *Windows*로 설정합니다. |
| WindowsIdentities |클러스터와 클라이언트 ID를 포함합니다. |
| ClusterIdentity |컴퓨터 그룹 domain\machinegroup을 사용하여 노드 간 보안을 구성합니다. |
| ClientIdentities |클라이언트-노드 보안을 구성합니다. 클라이언트 사용자 계정의 배열입니다. |  
| ID |클라이언트 ID로 도메인 사용자인 domain\username을 추가합니다. |  
| IsAdmin |도메인 사용자가 관리자 클라이언트 액세스 권한을 갖는 경우 true로 설정하고, 사용자 클라이언트 액세스 권한을 갖는 경우 false를 설정합니다. |  

Active Directory 도메인 내에서 컴퓨터 그룹을 사용하려는 경우 [노드 간 보안](service-fabric-cluster-security.md#node-to-node-security)은 **ClusterIdentity** 사용을 설정하여 구성됩니다. 자세한 내용은 [Active Directory에 컴퓨터 그룹 만들기](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx)를 참조하세요.

[클라이언트-노드 간 보안](service-fabric-cluster-security.md#client-to-node-security)은 **ClientIdentities**를 사용하여 구성됩니다. 클라이언트와 클러스터 간에 트러스트를 설정하기 위해 클라이언트가 신뢰할 수 있는 클라이언트 ID를 파악하도록 클러스터를 구성해야 합니다. 다음 두 가지 방법으로 트러스트를 설정할 수 있습니다.

- 연결할 수 있는 도메인 그룹 사용자를 지정합니다.
- 연결할 수 있는 도메인 노드 사용자를 지정합니다.

서비스 패브릭은 서비스 패브릭 클러스터에 연결된 클라이언트에 대해 관리자 및 사용자 액세스 제어 형식을 지원합니다. 액세스 제어는 클러스터 관리자가 사용자 그룹마다 특정 형식의 클러스터 작업에 대한 액세스를 제한할 수 있도록 하여 클러스터의 보안을 강화할 수 있습니다.  관리자는 관리 기능(읽기/쓰기 기능만 포함)에 대한 모든 권한을 가집니다. 사용자는 기본적으로 관리 기능(예: 쿼리 기능)에 대한 읽기 권한 및 애플리케이션과 서비스를 확인하는 기능만 갖습니다.  

다음 예제 **security** 섹션에서는 Windows 보안을 구성하고 *ServiceFabric/clusterA.contoso.com*의 컴퓨터가 클러스터의 일부이며 *CONTOSO\usera*에 관리자 클라이언트 액세스 권한이 있음을 지정합니다.

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> 도메인 컨트롤러에 Service Fabric을 배포해서는 안 됩니다. 컴퓨터 그룹 또는 gMSA(그룹 관리 서비스 계정)을 사용할 때 ClusterConfig.json에는 도메인 컨트롤러의 IP 주소가 포함되지 않습니다.
>
>

## <a name="next-steps"></a>다음 단계
*ClusterConfig.JSON* 파일에서 Windows 보안을 구성한 후에 [Windows에서 실행되는 독립 실행형 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)에 나와 있는 클러스터 만들기 프로세스를 다시 시작합니다.

노드 간 보안, 클라이언트 및 노드 간 보안 및 역할 기반 액세스 제어 방법에 대한 자세한 내용은 [클러스터 보안 시나리오](service-fabric-cluster-security.md)를 참조하세요.

PowerShell 또는 FabricClient를 사용한 연결에 대한 예제는 [보안 클러스터에 연결](service-fabric-connect-to-secure-cluster.md) 을 참조하세요.
