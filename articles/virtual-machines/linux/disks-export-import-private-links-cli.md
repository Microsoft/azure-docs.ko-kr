---
title: Azure CLI - Private Links를 사용하여 관리 디스크에 대한 가져오기/내보내기 액세스 제한
description: Azure CLI를 사용하여 관리 디스크에 대한 Private Links를 사용하도록 설정합니다. 가상 네트워크 내에서만 디스크를 안전하게 내보내고 가져올 수 있습니다.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 2c9c63956144c6438dc0900fa9fdd06ce7d30f60
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322061"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links"></a>Azure CLI - Private Links를 사용하여 관리 디스크에 대한 가져오기/내보내기 액세스 제한

관리 디스크를 사용하는 Private Links에 대한 지원은 현재 미리 보기로 제공됩니다. [프라이빗 엔드포인트](../../private-link/private-endpoint-overview.md)를 사용하여 관리 디스크의 내보내기 및 가져오기를 제한하고 Azure 가상 네트워크의 클라이언트에서 [Private Link](../../private-link/private-link-overview.md)를 통해 안전하게 데이터에 액세스할 수 있습니다. 프라이빗 엔드포인트는 관리 디스크 서비스에 대해 가상 네트워크 주소 공간의 IP 주소를 사용합니다. 가상 네트워크의 클라이언트와 관리 디스크 간의 네트워크 트래픽은 가상 네트워크와 Microsoft 백본 네트워크의 프라이빗 링크만 통과하여 공용 인터넷으로부터의 노출을 제거합니다.

Private Links를 사용하여 관리 디스크를 내보내거나 가져오려면 먼저 디스크 액세스 리소스를 만들고, 프라이빗 엔드포인트를 만들어 동일한 구독의 가상 네트워크에 연결합니다. 그런 다음, 디스크 또는 스냅샷을 디스크 액세스 인스턴스와 연결합니다. 마지막으로 디스크 또는 스냅샷의 NetworkAccessPolicy 속성을 `AllowPrivate`으로 설정합니다. 그러면 가상 네트워크에 대한 액세스가 제한됩니다. 

NetworkAccessPolicy 속성을 `DenyAll`로 설정하여 다른 사용자가 디스크 또는 스냅샷의 데이터를 내보내지 못하도록 할 수 있습니다. NetworkAccessPolicy 속성의 기본값은 `AllowAll`입니다.

## <a name="limitations"></a>제한 사항

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]

## <a name="regional-availability"></a>국가별 가용성

[!INCLUDE [virtual-machines-disks-private-links-regions](../../../includes/virtual-machines-disks-private-links-regions.md)]

## <a name="log-in-into-your-subscription-and-set-your-variables"></a>구독에 로그인하고 변수 설정

```azurecli-interactive
subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Azure CLI를 사용하여 디스크 액세스 만들기
```azurecli-interactive
az deployment group create -g $resourceGroupName \
--template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/privatelinks/CreateDiskAccess.json" \
--parameters "region=$region" "diskAccessName=$diskAccessName"

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Virtual Network 만들기

프라이빗 엔드포인트에는 NSG(네트워크 보안 그룹)와 같은 네트워크 정책이 지원되지 않습니다. 지정된 서브넷에 프라이빗 엔드포인트를 배포하려면 해당 서브넷에 명시적 비활성화 설정이 필요합니다. 

```azurecli-interactive
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>서브넷 프라이빗 엔드포인트 정책 사용 안 함

Azure는 리소스를 가상 네트워크 내의 서브넷에 배포하므로 프라이빗 엔드포인트 네트워크 정책을 사용하지 않도록 서브넷을 업데이트해야 합니다. 

```azurecli-interactive
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>디스크 액세스 개체용 프라이빗 엔드포인트 만들기

```azurecli-interactive
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>프라이빗 DNS 영역 구성

Storage Blob 도메인에 대한 프라이빗 DNS 영역을 만들고, Virtual Network와의 연결 링크를 만들고, DNS 영역 그룹을 만들어 프라이빗 엔드포인트를 프라이빗 DNS 영역과 연결합니다. 

```azurecli-interactive
az network private-dns zone create --resource-group $resourceGroupName \
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```

## <a name="create-a-disk-protected-with-private-links"></a>Private Links로 보호되는 디스크 만들기
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
diskName=yourDiskName
diskSkuName=Standard_LRS
diskSizeGB=128

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az deployment group create -g $resourceGroupName \
   --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/privatelinks/CreateDisksWithExportViaPrivateLink.json" \
   --parameters "diskName=$diskName" \
   "diskSkuName=$diskSkuName" \
   "diskSizeGB=$diskSizeGB" \
   "diskAccessId=$diskAccessId" \
   "region=$region" \
   "networkAccessPolicy=AllowPrivate"
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Private Links로 보호되는 디스크의 스냅샷 만들기
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
sourceDiskName=yourSourceDiskForSnapshot
snapshotNameSecuredWithPL=yourSnapshotName

diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az deployment group create -g $resourceGroupName \
   --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/privatelinks/CreateSnapshotWithExportViaPrivateLink.json" \
   --parameters "snapshotName=$snapshotNameSecuredWithPL" \
   "sourceResourceId=$diskId" \
   "diskAccessId=$diskAccessId" \
   "region=$region" \
   "networkAccessPolicy=AllowPrivate" 
```

## <a name="next-steps"></a>다음 단계

- [Private Links에 대한 FAQ](../faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [CLI를 사용하여 관리 스냅샷을 다른 지역의 스토리지 계정에 VHD로 내보내기/복사](../scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md)
