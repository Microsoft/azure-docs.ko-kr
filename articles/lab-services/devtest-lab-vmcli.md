---
title: Azure CLI를 사용하여 DevTest Labs에서 가상 머신 만들기 및 관리 | Microsoft Docs
description: Azure DevTest Labs를 사용하여 Azure CLI에서 가상 머신을 만들고 관리하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 48a30ef86cdb10b540ffe1231294542ccff87255
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622323"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Azure CLI를 사용하여 DevTest Labs에서 가상 머신 만들기 및 관리
이 빠른 시작 만들기, 시작, 연결, 업데이트 및 랩에서 개발 컴퓨터를 정리를 통해 안내 합니다. 

시작하기 전에

* 랩을 만들지 않은 경우 지침은 [여기](devtest-lab-create-lab.md)에서 찾을 수 있습니다.

* [Azure CLI를 설치합니다](/cli/azure/install-azure-cli). 시작하려면 az login을 실행하여 Azure와 연결합니다. 

## <a name="create-and-verify-the-virtual-machine"></a>가상 머신 만들기 및 확인 
DevTest Labs의 관련된 명령을 실행 하기 전에 적절 한 Azure 컨텍스트를 사용 하 여 설정 된 `az account set` 명령:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

가상 머신을 만들려면 명령입니다. `az lab vm create`합니다. 랩, 랩 이름 및 가상 머신 이름에 대 한 리소스 그룹은 모든 필수입니다. 인수의 나머지 가상 컴퓨터의 유형에 따라 변경 됩니다.

다음 명령은 Azure Market Place에서 Windows 기반 이미지를 만듭니다. 이미지의 이름을 표시 되는 Azure portal을 사용 하 여 가상 머신을 만들 때와 같습니다. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3’ --admin-username 'AdminUser' --admin-password 'Password1!'
```

다음 명령은 랩에서 사용할 수 있는 사용자 지정 이미지를 기반으로 가상 컴퓨터를 만듭니다.

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

합니다 **이미지 형식** 인수에서 변경 되었습니다 **갤러리** 하 **사용자 지정**합니다. Azure portal에서 가상 컴퓨터를 만드는 경우 표시 되는 내용 일치 하는 이미지의 이름을 합니다.

다음 명령은 VM을 만듭니다 사용 하 여 marketplace 이미지에서 ssh 인증:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

설정 하 여 수식에 따라 가상 컴퓨터를 만들 수도 있습니다는 **이미지 형식** 매개 변수를 **수식**합니다. 가상 머신에 대 한 특정 가상 네트워크를 선택 해야 할 경우 사용 합니다 **-vnet-name** 및 **서브넷** 매개 변수입니다. 자세한 내용은 [az lab vm 만들기](/cli/azure/lab/vm#az-lab-vm-create)합니다.

## <a name="verify-that-the-vm-is-available"></a>VM을 사용할 수 있는지 확인합니다.
사용 하 여는 `az lab vm show` 명령을 시작 하 고 연결 하려면 먼저 VM를 사용할 수 있는지 확인 합니다. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>가상 머신 시작 및 연결
다음 예제 명령은 VM을 시작합니다.

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

VM에 연결 합니다. [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) 나 [원격 데스크톱](../virtual-machines/windows/connect-logon.md)합니다.
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>가상 머신 업데이트
다음 샘플 명령은 VM에 아티팩트를 적용 됩니다.

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

랩에서 사용할 수 있는 아티팩트를 나열합니다.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>가상 머신 중지 및 삭제    
다음 샘플 명령은 VM을 중지합니다.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

VM을 삭제합니다.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>다음 단계
다음 콘텐츠를 참조 하세요. [Azure DevTest Labs에 대 한 azure CLI 설명서](/cli/azure/lab?view=azure-cli-latest)합니다. 