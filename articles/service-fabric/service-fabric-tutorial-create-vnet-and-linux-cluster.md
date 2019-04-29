---
title: Azure에서 Linux Service Fabric 클러스터 만들기 | Microsoft Docs
description: Azure CLI를 사용하여 기존 Azure 가상 네트워크에 Linux Service Fabric 클러스터를 배포하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 00d7e510fa43865f1427092f2f20b9847f1afa9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60863787"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Azure 가상 네트워크에 Linux Service Fabric 클러스터 배포

이 문서에서는 Azure CLI 및 템플릿을 사용하여 [Azure VNET(가상 네트워크)](../virtual-network/virtual-networks-overview.md)에 Linux Service Fabric 클러스터를 배포하는 방법을 알아봅니다. 작업이 완료되면 애플리케이션을 배포할 수 있는, 클라우드에서 실행되는 클러스터가 생깁니다. PowerShell을 사용하여 Windows 클러스터를 만들려면 [Azure에서 보안 Windows 클러스터 만들기](service-fabric-tutorial-create-vnet-and-windows-cluster.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

시작하기 전에

* Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Service Fabric CLI](service-fabric-cli.md)를 설치합니다.
* [Azure CLI](/cli/azure/install-azure-cli) 설치
* 클러스터의 주요 개념을 알아보려면 [Azure 클러스터 개요](service-fabric-azure-clusters-overview.md)를 읽어보세요.
* 프로덕션 클러스터 배포를 [계획 및 준비](service-fabric-cluster-azure-deployment-preparation.md)합니다.

다음 절차에서는 7개 노드 Service Fabric 클러스터를 만듭니다. Azure에서 Service Fabric 클러스터를 실행할 때 발생하는 비용을 계산하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.

## <a name="download-and-explore-the-template"></a>템플릿 다운로드 및 탐색

다음 Resource Manager 템플릿 파일을 다운로드합니다.

* [AzureDeploy.json][template]
* [AzureDeploy.Parameters.json][parameters]

이 템플릿은 7개 가상 머신 및 3개 노드 유형의 보안 클러스터를 가상 네트워크에 배포합니다.  다른 예제 템플릿은 [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates)에 있을 수 있습니다. [AzureDeploy.json][template]은 다음을 포함한 숫자 리소스를 배포합니다.

### <a name="service-fabric-cluster"></a>Service Fabric 클러스터

**Microsoft.ServiceFabric/clusters** 리소스에서 다음과 같은 특성이 있는 Linux 클러스터가 배포됩니다.

* 3개 노드 유형
* 기본 노드 유형의 5개 노드(템플릿 매개 변수에서 구성 가능), 다른 두 노드 유형의 각 1개 노드
* OS: Ubuntu 16.04 LTS(템플릿 매개 변수에서 구성 가능)
* 보안된 인증서(템플릿 매개 변수에서 구성 가능)
* [DNS 서비스](service-fabric-dnsservice.md) 사용함
* 브론즈의 [내구성 수준](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)(템플릿 매개 변수에서 구성 가능)
* 실버의 [안정성 수준](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)(템플릿 매개 변수에서 구성 가능)
* 클라이언트 연결 엔드포인트: 19000(템플릿 매개 변수에서 구성 가능)
* HTTP 게이트웨이 엔드포인트: 19080(템플릿 매개 변수에서 구성 가능)

### <a name="azure-load-balancer"></a>Azure Load Balancer

**Microsoft.Network/loadBalancers** 리소스에서 부하 분산 장치가 구성되고 다음 포트에 대한 프로브 및 규칙이 설정됩니다.

* 클라이언트 연결 엔드포인트: 19000
* HTTP 게이트웨이 엔드포인트: 19080
* 애플리케이션 포트: 80
* 애플리케이션 포트: 443

### <a name="virtual-network-and-subnet"></a>가상 네트워크 및 서브넷

가상 네트워크 및 서브넷의 이름은 템플릿 매개 변수에서 선언됩니다.  가상 네트워크 및 서브넷의 주소 공간도 템플릿 매개 변수로 선언되고 **Microsoft.Network/virtualNetworks** 리소스에 구성됩니다.

* 가상 네트워크 주소 공간: 10.0.0.0/16
* Service Fabric 서브넷 주소 공간: 10.0.2.0/24

다른 애플리케이션 포트가 필요한 경우 트래픽을 허용하도록 Microsoft.Network/loadBalancers 리소스를 조정해야 합니다.

## <a name="set-template-parameters"></a>템플릿 매개 변수 설정

[AzureDeploy.Parameters][parameters] 매개 변수 파일은 클러스터 및 연결된 리소스를 배포하는 데 사용되는 많은 값을 선언합니다. 배포에 대해 수정이 필요할 수도 있는 매개 변수 중 일부는 다음과 같습니다.

|매개 변수|예제 값|메모|
|---|---||
|adminUserName|vmadmin| 클러스터 VM에 대한 관리자 사용자 이름입니다. |
|adminPassword|Password#1234| 클러스터 VM에 대한 관리자 암호입니다.|
|clusterName|mysfcluster123| 클러스터의 이름입니다. |
|location|southcentralus| 클러스터의 위치입니다. |
|certificateThumbprint|| <p>자체 서명된 인증서를 만들거나 인증서 파일을 제공하는 경우 값은 비워두어야 합니다.</p><p>이전에 키 자격 증명 모음에 업로드된 기존 인증서를 사용하려면 인증서 SHA1 지문 값을 입력합니다. 예를 들면 "6190390162C988701DB5676EB81083EA608DCCF3"과 같습니다. </p>|
|certificateUrlValue|| <p>자체 서명된 인증서를 만들거나 인증서 파일을 제공하는 경우 값은 비워두어야 합니다.</p><p>이전에 키 자격 증명 모음에 업로드된 기존 인증서를 사용하려면 인증서 URL을 입력합니다. 예: "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>자체 서명된 인증서를 만들거나 인증서 파일을 제공하는 경우 값은 비워두어야 합니다.</p><p>이전에 키 자격 증명 모음에 업로드된 기존 인증서를 사용하려면 원본 자격 증명 모음 값을 입력합니다. 예를 들면 “/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”와 같습니다.</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>가상 네트워크 및 클러스터 배포

다음으로 네트워크 토폴로지를 설정하고 Service Fabric 클러스터를 배포합니다. [AzureDeploy.json][template] Resource Manager 템플릿은 VNET(가상 네트워크) 및 Service Fabric에 대한 서브넷을 만듭니다. 템플릿은 활성화된 인증서 보안으로 클러스터도 배포합니다.  프로덕션 클러스터의 경우 클러스터 인증서로 CA(인증 기관)의 인증서를 사용합니다. 테스트 클러스터를 보호하는 데 자체 서명된 인증서를 사용할 수 있습니다.

이 문서의 템플릿은 인증서 지문을 사용하여 클러스터 인증서를 식별하는 클러스터를 배포합니다.  두 인증서에 동일한 지문을 사용할 수 없으므로 인증서 관리가 더 어려워집니다. 배포된 클러스터를 인증서 지문에서 인증서 일반 이름으로 전환하면 인증서 관리 방법이 훨씬 간단해집니다.  인증서 관리에 인증서 일반 이름을 사용하도록 클러스터를 업데이트하는 방법에 대해 알아보려면 [클러스터를 인증서 일반 이름 관리로 변경](service-fabric-cluster-change-cert-thumbprint-to-cn.md)을 참조하세요.

### <a name="create-a-cluster-using-an-existing-certificate"></a>기존 인증서를 사용하여 클러스터 만들기

다음 스크립트는 [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) 명령 및 템플릿을 사용하여 기존 인증서로 보호된 새 클러스터를 배포합니다. 또한 명령은 Azure에서 새 키 자격 증명 모음을 만들고 인증서를 업로드합니다.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>자체 서명된 새로운 인증서를 사용하여 클러스터 만들기

다음 스크립트는 [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) 명령 및 템플릿을 사용하여 Azure에 새 클러스터를 배포합니다. 또한 이 명령은 Azure에 새로운 키 자격 증명 모음을 만들고, 키 자격 증명 모음에 자체 서명된 인증서를 추가하고, 인증서 파일을 로컬로 다운로드합니다.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>보안 클러스터에 연결

키를 사용하여 Service Fabric CLI `sfctl cluster select` 명령을 통해 클러스터에 연결합니다.  자체 서명된 인증서에만 **--no-verify** 옵션을 사용합니다.

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

`sfctl cluster health` 명령을 사용하여 연결되어 있고 클러스터 상태가 정상인지 확인합니다.

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>리소스 정리

다음 문서로 바로 이동하지 않는 경우 요금이 발생하지 않도록 [클러스터를 삭제](service-fabric-cluster-delete.md)하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[클러스터 크기 조정](service-fabric-tutorial-scale-cluster.md) 방법에 대해 알아보세요.

이 문서의 템플릿은 인증서 지문을 사용하여 클러스터 인증서를 식별하는 클러스터를 배포합니다.  두 인증서에 동일한 지문을 사용할 수 없으므로 인증서 관리가 더 어려워집니다. 배포된 클러스터를 인증서 지문에서 인증서 일반 이름으로 전환하면 인증서 관리 방법이 훨씬 간단해집니다.  인증서 관리에 인증서 일반 이름을 사용하도록 클러스터를 업데이트하는 방법에 대해 알아보려면 [클러스터를 인증서 일반 이름 관리로 변경](service-fabric-cluster-change-cert-thumbprint-to-cn.md)을 참조하세요.

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
