---
title: 서비스 끝점 정책 구성-Azure HDInsight
description: Azure HDInsight를 사용 하 여 가상 네트워크에 대 한 서비스 끝점 정책을 구성 하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: f2752e5ab2bf7c2926ec9e2c0e4929eab91ed377
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88530973"
---
# <a name="configure-virtual-network-service-endpoint-policies-for-azure-hdinsight"></a>Azure HDInsight에 대 한 가상 네트워크 서비스 끝점 정책 구성

이 문서에서는 Azure HDInsight를 사용 하 여 가상 네트워크에서 서비스 끝점 정책을 구현 하는 방법에 대 한 정보를 제공 합니다.

## <a name="background"></a>배경

Azure HDInsight를 사용 하면 자체 가상 네트워크에서 클러스터를 만들 수 있습니다. 가상 네트워크에서 다른 Azure 서비스 (예: 저장소 계정)로 나가는 트래픽을 허용 해야 하는 경우 [서비스 끝점 정책을](../virtual-network/virtual-network-service-endpoint-policies-overview.md)만들 수 있습니다. 그러나 Azure Portal를 통해 만들어진 서비스 끝점 정책은 단일 계정, 구독의 모든 계정 또는 리소스 그룹의 모든 계정에 대 한 정책만 만들 수 있습니다.

그러나 관리 되는 서비스인 Azure HDInsight는 각 지역의 특정 저장소 계정에 있는 각 클러스터의 데이터와 로그 파일을 수집 합니다. 이 데이터가 가상 네트워크에서 HDInsight에 도달 하려면 Azure HDInsight에서 관리 하는 특정 데이터 컬렉션 지점으로 나가는 트래픽을 허용 하는 서비스 끝점 정책을 만들어야 합니다.

## <a name="service-endpoint-policies-for-hdinsight"></a>HDInsight에 대 한 서비스 끝점 정책

이러한 서비스 끝점 정책은 다음 기능을 지원 합니다.

- 클러스터 만들기, 작업 실행 및 크기 조정과 같은 플랫폼 작업에서 로그 및 원격 분석을 수집 합니다.
- 클러스터에 소프트웨어 및 라이브러리를 프로 비전 하기 위해 새로 만든 클러스터 노드에 Vhd (가상 하드 디스크)를 연결 합니다.

이 데이터 흐름을 사용 하도록 서비스 끝점 정책을 만들지 않은 경우 클러스터 만들기가 실패 하 고 Azure HDInsight에서 클러스터에 대 한 지원을 제공할 수 없게 됩니다.

## <a name="create-service-endpoint-policies-for-hdinsight"></a>HDInsight에 대 한 서비스 끝점 정책 만들기

새 클러스터를 만들기 전에 올바른 서비스 끝점 정책이 가상 네트워크에 연결 되어 있는지 확인 하십시오. 그렇지 않으면 클러스터 만들기가 실패 하거나 오류가 발생할 수 있습니다.

다음 프로세스를 사용 하 여 필요한 서비스 끝점 정책을 만듭니다.

1. HDInsight 클러스터를 만들 지역을 결정 합니다.
1. HDInsight 관리 저장소 계정에 대 한 모든 리소스 그룹을 제공 하는 [서비스 끝점 정책 리소스 목록](https://github.com/Azure-Samples/hdinsight-enterprise-security/blob/main/hdinsight-service-endpoint-policy-resources.json)에서 해당 지역을 조회 합니다.
1. 해당 지역의 리소스 그룹 목록을 선택 합니다. 에 대 한 리소스의 예는 `Canada Central` 다음과 같습니다.

    ```json
    "Canada Central":[
        "/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
        "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
        "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG"
    ],
    ```

1. Azure CLI 또는 Azure PowerShell으로 작성 된 설치 스크립트에 해당 리소스 그룹 목록을 삽입 합니다.

    ```azurecli
    $subscriptionId = "<subscription id>"
    $rgName="<resource group name> "
    $location="<location name>"
    $vnetName="<vnet name>"
    $subnetName="<subnet name>"
    $sepName="<service endpoint policy name>"
    $sepDefName="<service endpoint policy definition name>"
    
    # Set to the right subscription ID
    az account set --subscription $subscriptionId
    
    # setup service endpoint on the virtual network subnet
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoints Microsoft.Storage
    
    # Create Service Endpoint Policy
    az network service-endpoint policy create -g $rgName  -n $sepName -l $location
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",`
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",`
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Assign service resources to the SEP policy.
    az network service-endpoint policy-definition create -g $rgName --policy-name $sepName -n $sepDefName --service "Microsoft.Storage" --service-resources $resources
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    az network vnet subnet update -g $rgName --vnet-name $vnetName -n $subnetName --service-endpoint-policy $sepName
    ```

    PowerShell을 사용 하 여 서비스 끝점 정책을 설정 하려면 다음 코드 조각을 사용 합니다.
    
    ```powershell
    #Script to assign SEP 
    $subscriptionId = "<subscription id>"
    $rgName = "<resource group name>"
    $vnetName = "<vnet name>"
    $subnetName = "<subnet Name"
    $location = "Canada Central"
    
    # Connect to your Azure Account
    Connect-AzAccount
    
    # Select the Subscription that you want to use
    Select-AzSubscription -SubscriptionId $subscriptionId
    
    # Retrieve VNet Config
    $vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
    
    # Retrieve Subnet Config
    $subnet = Get-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet
    
    # Insert the list of HDInsight owned resources for the region your clusters will be created in.
    [String[]]$resources = @("/subscriptions/235d341f-7fb9-435c-9bdc-034b7306c9b4/resourceGroups/Default-Storage-WestUS",
    "/subscriptions/da0c4c68-9283-4f88-9c35-18f7bd72fbdd/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/6a853a41-3423-4167-8d9c-bcf37dc72818/resourceGroups/GenevaWarmPathManageRG",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/Default-Storage-CanadaCentral",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/cancstorage",
    "/subscriptions/c8845df8-14d1-4a46-b6dd-e0c44ae400b0/resourceGroups/GenevaWarmPathManageRG")
    
    #Declare service endpoint policy definition
    $sepDef = New-AzServiceEndpointPolicyDefinition -Name "SEPHDICanadaCentral" -Description "Service Endpoint Policy Definition" -Service "Microsoft.Storage" -ServiceResource $resources
    
    # Service Endpoint Policy
    $sep= New-AzServiceEndpointPolicy -ResourceGroupName $rgName -Name "SEPHDICanadaCentral" -Location $location -ServiceEndpointPolicyDefinition $sepDef
    
    # Associate a subnet to the service endpoint policy just created. If there is a delay in updating it to subnet, you can use the Azure portal to associate the policy with the subnet.
    Set-AzVirtualNetworkSubnetConfig -Name $subnetName -VirtualNetwork $vnet -AddressPrefix $subnet.AddressPrefix -ServiceEndpointPolicy $sep
    ```

## <a name="next-steps"></a>다음 단계

* [Azure HDInsight 가상 네트워크 아키텍처](hdinsight-virtual-network-architecture.md)
* [네트워크 가상 어플라이언스 구성](./network-virtual-appliance.md)
