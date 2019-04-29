---
title: Azure CLI를 사용하여 Linux VM 이미지 선택 | Microsoft Docs
description: Azure CLI를 사용하여 Marketplace VM 이미지의 게시자, 제품, SKU 및 버전을 확인하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 453628dca04fbc3c48564f15b6cf61802165b0cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880574"
---
# <a name="find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Azure CLI를 사용하여 Azure Marketplace에서 Linux VM 이미지 찾기

이 항목에서는 Azure CLI를 사용하여 Azure Marketplace에서 VM 이미지를 찾는 방법을 설명합니다. CLI, Resource Manager 템플릿 또는 기타 도구를 사용하여 프로그래밍 방식으로 VM을 생성할 때 이 정보를 사용하여 Marketplace 이미지를 지정합니다.

또한 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 상점, [Azure Portal](https://portal.azure.com) 또는 [Azure PowerShell](../windows/cli-ps-findimage.md)을 사용하여 사용 가능한 이미지와 제품을 찾아봅니다. 

최신 [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 Azure 계정(`az login`)에 로그인합니다.

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="list-popular-images"></a>인기 있는 이미지 나열

`--all` 옵션을 사용하지 않고 [az vm image list](/cli/azure/vm/image) 명령을 실행하여 Azure Marketplace에서 인기있는 VM 이미지의 목록을 확인합니다. 예를 들어, 다음 명령을 실행하여 테이블 형식으로 캐시된 인기있는 이미지 목록을 표시합니다.

```azurecli
az vm image list --output table
```

출력에는 이미지 URN(*Urn* 열의 값)이 포함됩니다. 또한 인기있는 Marketplace 이미지 중 하나를 사용하여 VM을 만드는 경우 *UbuntuLTS*와 같이 축소된 형식으로 *UrnAlias*를 지정할 수도 있습니다.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>특정 이미지 찾기

Marketplace에서 특정 VM 이미지를 찾으려면 `az vm image list` 명령에 `--all` 옵션을 사용합니다. 명령의 이 버전은 완료하는 데 다소 시간이 걸리며 긴 출력을 생성할 수 있으므로 일반적으로 `--publisher` 또는 다른 매개 변수를 기준으로 목록을 필터링합니다. 

예를 들어, 다음 명령은 모든 Debian 제품을 표시합니다(`--all` 스위치 없이는 공용 이미지의 로컬 캐시만을 검색함).

```azurecli
az vm image list --offer Debian --all --output table 

```

부분 출력: 

```
Offer              Publisher    Sku                  Urn                                                    Version
-----------------  -----------  -------------------  -----------------------------------------------------  --------------
Debian             credativ     7                    credativ:Debian:7:7.0.201602010                        7.0.201602010
Debian             credativ     7                    credativ:Debian:7:7.0.201603020                        7.0.201603020
Debian             credativ     7                    credativ:Debian:7:7.0.201604050                        7.0.201604050
Debian             credativ     7                    credativ:Debian:7:7.0.201604200                        7.0.201604200
Debian             credativ     7                    credativ:Debian:7:7.0.201606280                        7.0.201606280
Debian             credativ     7                    credativ:Debian:7:7.0.201609120                        7.0.201609120
Debian             credativ     7                    credativ:Debian:7:7.0.201611020                        7.0.201611020
Debian             credativ     7                    credativ:Debian:7:7.0.201701180                        7.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201602010                        8.0.201602010
Debian             credativ     8                    credativ:Debian:8:8.0.201603020                        8.0.201603020
Debian             credativ     8                    credativ:Debian:8:8.0.201604050                        8.0.201604050
Debian             credativ     8                    credativ:Debian:8:8.0.201604200                        8.0.201604200
Debian             credativ     8                    credativ:Debian:8:8.0.201606280                        8.0.201606280
Debian             credativ     8                    credativ:Debian:8:8.0.201609120                        8.0.201609120
Debian             credativ     8                    credativ:Debian:8:8.0.201611020                        8.0.201611020
Debian             credativ     8                    credativ:Debian:8:8.0.201701180                        8.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201703150                        8.0.201703150
Debian             credativ     8                    credativ:Debian:8:8.0.201704110                        8.0.201704110
Debian             credativ     8                    credativ:Debian:8:8.0.201704180                        8.0.201704180
Debian             credativ     8                    credativ:Debian:8:8.0.201706190                        8.0.201706190
Debian             credativ     8                    credativ:Debian:8:8.0.201706210                        8.0.201706210
Debian             credativ     8                    credativ:Debian:8:8.0.201708040                        8.0.201708040
Debian             credativ     8                    credativ:Debian:8:8.0.201710090                        8.0.201710090
Debian             credativ     8                    credativ:Debian:8:8.0.201712040                        8.0.201712040
Debian             credativ     8                    credativ:Debian:8:8.0.201801170                        8.0.201801170
Debian             credativ     8                    credativ:Debian:8:8.0.201803130                        8.0.201803130
Debian             credativ     8                    credativ:Debian:8:8.0.201803260                        8.0.201803260
Debian             credativ     8                    credativ:Debian:8:8.0.201804020                        8.0.201804020
Debian             credativ     8                    credativ:Debian:8:8.0.201804150                        8.0.201804150
Debian             credativ     8                    credativ:Debian:8:8.0.201805160                        8.0.201805160
Debian             credativ     8                    credativ:Debian:8:8.0.201807160                        8.0.201807160
Debian             credativ     8                    credativ:Debian:8:8.0.201901221                        8.0.201901221
...
```

`--location`, `--publisher` 및 `--sku` 옵션을 사용하여 비슷한 필터를 적용합니다. 모든 Debian 이미지를 찾는 `--offer Deb`을 검색하는 등 필터에서 부분 일치를 수행할 수 있습니다.

`--location` 옵션을 사용하여 특정 위치를 지정하지 않으면 기본 위치 값이 반환됩니다. (`az configure --defaults location=<location>`을 실행하여 다른 기본 위치를 설정합니다.)

예를 들어 다음 명령은 유럽 서부 지역의 모든 Debian 8 SKU를 나열합니다.

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

부분 출력:

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
Debian   credativ     8                  credativ:Debian:8:8.0.201710090                  8.0.201710090
Debian   credativ     8                  credativ:Debian:8:8.0.201712040                  8.0.201712040
Debian   credativ     8                  credativ:Debian:8:8.0.201801170                  8.0.201801170
Debian   credativ     8                  credativ:Debian:8:8.0.201803130                  8.0.201803130
Debian   credativ     8                  credativ:Debian:8:8.0.201803260                  8.0.201803260
Debian   credativ     8                  credativ:Debian:8:8.0.201804020                  8.0.201804020
Debian   credativ     8                  credativ:Debian:8:8.0.201804150                  8.0.201804150
Debian   credativ     8                  credativ:Debian:8:8.0.201805160                  8.0.201805160
Debian   credativ     8                  credativ:Debian:8:8.0.201807160                  8.0.201807160
Debian   credativ     8                  credativ:Debian:8:8.0.201901221                  8.0.201901221
...
```

## <a name="navigate-the-images"></a>이미지 이동
 
위치에서 이미지를 찾는 또 다른 방법은 [az vm image list-publishers](/cli/azure/vm/image), [az vm image list-offers](/cli/azure/vm/image) 및 [az vm image list-skus](/cli/azure/vm/image)의 순서로 명령을 실행하는 것입니다. 이러한 명령을 사용하여 값을 결정합니다.

1. 이미지 게시자를 나열합니다.
2. 지정된 게시자에 제안을 나열합니다.
3. 지정된 제안에 SKU를 나열합니다.

그런 다음, 선택한 SKU에 대해 배포할 버전을 선택할 수 있습니다.

예를 들어 다음 명령은 미국 서부에 있는 이미지 게시자를 나열합니다.

```azurecli
az vm image list-publishers --location westus --output table
```

부분 출력:

```
Location    Name
----------  ----------------------------------------------------
westus      128technology
westus      1e
westus      4psa
westus      5nine-software-inc
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      accessdata-group
westus      accops
westus      Acronis
westus      Acronis.Backup
westus      actian-corp
westus      actian_matrix
westus      actifio
westus      activeeon
westus      advantech-webaccess
westus      aerospike
westus      affinio
westus      aiscaler-cache-control-ddos-and-url-rewriting-
westus      akamai-technologies
westus      akumina
...
```

이 정보를 사용하여 특정 게시자의 제안을 찾을 수 있습니다. 예를 들어, 미국 서부에 있는 *Canonical* 게시자의 경우 `azure vm image list-offers`를 실행하여 제품을 찾습니다. 다음 예제와 같이 위치 및 게시자를 전달합니다.

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

출력

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
```
미국 서부 지역에서 Canonical이 Azure에 *UbuntuServer* 제품을 게시한다는 것을 확인할 수 있습니다. 하지만 SKU는 무엇입니까? 이 값을 얻으려면 `azure vm image list-skus`를 실행하고 검색한 위치, 게시자 및 제안을 설정합니다.

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

출력:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-LTS
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10
westus      18.10-DAILY
westus      19.04-DAILY
```

마지막으로 `az vm image list` 명령을 사용하여 원하는 SKU의 특정 버전을 찾습니다(예: *18.04-LTS*).

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 18.04-LTS --all --output table
```

부분 출력:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201804262  18.04.201804262
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805170  18.04.201805170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805220  18.04.201805220
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806130  18.04.201806130
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806170  18.04.201806170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201807240  18.04.201807240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808060  18.04.201808060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808080  18.04.201808080
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808140  18.04.201808140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808310  18.04.201808310
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201809110  18.04.201809110
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810030  18.04.201810030
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810240  18.04.201810240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810290  18.04.201810290
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201811010  18.04.201811010
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812031  18.04.201812031
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812040  18.04.201812040
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812060  18.04.201812060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901140  18.04.201901140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901220  18.04.201901220
...
```

이제 URN 값을 기록하여 사용할 이미지를 정밀하게 선택할 수 있습니다. [az vm create](/cli/azure/vm) 명령을 사용하여 VM을 만들 때 이 값을 `--image` 매개 변수와 함께 제공합니다. 필요에 따라 "최신"을 사용하여 URN에서 버전 번호를 바꿀 수 있습니다. 이 버전은 항상 최신 버전의 이미지입니다. 

Resource Manager 템플릿을 사용하여 VM을 배포하는 경우 `imageReference` 속성에 이미지 매개 변수를 개별적으로 설정합니다. [템플릿 참조](/azure/templates/microsoft.compute/virtualmachines)를 참조하세요.

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>플랜 속성 보기

이미지의 구매 계획 정보를 보려면 [az vm image show](/cli/azure/image) 명령을 실행합니다. 출력의 `plan` 속성이 `null`이 아닌 경우, 이미지에는 프로그램 방식으로 배포하기 전에 동의해야 하는 약관이 있습니다.

예를 들어, Canonical Ubuntu Server 18.04 LTS 이미지는 `plan` 정보가 `null`이기 때문에 추가 약관이 없습니다.

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

출력:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

RabbitMQ Certified by Bitnami 이미지에 유사한 명령을 실행하면 `name`, `product` 및 `publisher`와 같은 `plan` 속성이 표시됩니다. (일부 이미지에는 `promotion code` 속성도 있습니다.) 이 이미지를 배포하려면 다음 섹션에서 약관에 동의하고 프로그래밍 방식 배포를 사용하도록 설정합니다.

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
출력

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

### <a name="accept-the-terms"></a>약관에 동의

사용 조건을 확인하고 동의하려면 [az vm image accept-terms](/cli/azure/vm/image?) 명령을 사용합니다. 약관에 동의하면 구독에서 프로그래밍 방식 배포를 사용하도록 설정됩니다. 이미지의 구독마다 약관에 한 번만 동의하면 됩니다. 예를 들면 다음과 같습니다.

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

출력에는 사용 조건에 대한 `licenseTextLink`가 포함되며 `accepted`의 값이 `true`임을 나타냅니다.

```
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

### <a name="deploy-using-purchase-plan-parameters"></a>구매 플랜 매개 변수를 사용하여 배포

이미지에 대한 약관에 동의한 후에는 구독에 VM을 배포할 수 있습니다. `az vm create` 명령을 사용하여 이미지를 배포하려면 이미지에 대한 URN 외에 구매 플랜에 대한 매개 변수를 제공합니다. 예를 들어 RabbitMQ Certified by Bitnami 이미지가 있는 VM을 배포하려면 다음 명령을 실행합니다.

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami
```

## <a name="next-steps"></a>다음 단계
이미지 정보를 사용하여 가상 머신을 빠르게 만들려면 [Azure CLI로 Linux VM 만들기 및 관리](tutorial-manage-vm.md)를 참조하세요.
