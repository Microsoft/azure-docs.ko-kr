---
title: Azure Linux 가상 머신에 태그를 지정하는 방법 | Microsoft Docs
description: Resource Manager 배포 모델을 사용하여 만든 Azure Linux 가상 머신에 태그를 지정하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: 297d3bc201b4bc9d9db0b0bed7a364769fa72859
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115977"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Azure에서 Linux 가상 컴퓨터에 태그를 지정하는 방법
이 문서에서는 리소스 관리자 배포 모델을 통해 Azure의 Linux 가상 머신에 태그를 지정하는 다양한 방법에 대해 설명합니다. 태그는 리소스 또는 리소스 그룹에 직접 배치할 수 있는 사용자 정의 키/값 쌍입니다. Azure는 현재 리소스 및 리소스 그룹당 최대 15개의 태그를 지원합니다. 태그를 만들 때 리소스에 배치하거나 기존 리소스에 추가할 수 있습니다. 태그는 리소스 관리자 배포 모델을 통해 만든 리소스에 대해서만 지원됩니다.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Azure CLI를 사용하여 태그 지정

시작하려면 최신 [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [az login](/cli/azure/reference-index#az-login)을 사용하여 Azure 계정에 로그인해야 합니다.

다음 명령을 사용하여 태그를 비롯한 지정된 Virtual Machine의 모든 속성을 볼 수 있습니다.

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Azure CLI를 통해 새 VM 태그를 추가하려면 태그 매개 변수 **--set**과 함께 `azure vm update` 명령을 사용할 수 있습니다.

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

`azure vm update` 명령에 **--remove** 매개 변수를 사용하여 태그를 제거할 수 있습니다.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Azure CLI 및 포털을 통해 리소스에 태그를 적용했으므로 이제 사용량 세부 정보를 확인하여 청구 포털에서 태그를 살펴보겠습니다.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>다음 단계
* Azure 리소스에 태그를 지정하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요][Azure Resource Manager Overview] 및 [태그를 사용하여 Azure 리소스 구성][Using Tags to organize your Azure Resources]을 참조하세요.
* 태그로 Azure 리소스의 사용을 관리하는 방법은 [Azure 청구서 이해][Understanding your Azure Bill] 및 [Microsoft Azure 리소스 소비에 대한 정보 얻기][Gain insights into your Microsoft Azure resource consumption]를 참조하세요.

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
