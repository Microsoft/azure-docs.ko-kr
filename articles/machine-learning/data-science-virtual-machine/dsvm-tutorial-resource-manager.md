---
title: '빠른 시작: Data Science VM 만들기 - Resource Manager 템플릿'
titleSuffix: Azure Data Science Virtual Machine
description: 이 빠른 시작에서는 Azure Resource Manager 템플릿을 사용하여 Data Science Virtual Machine을 빠르게 배포합니다.
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 06/10/2020
ms.service: machine-learning
ms.subservice: data-science-vm
ms.topic: quickstart
ms.openlocfilehash: 9b89c0a4135bf595991439dd47e57a870ea2b0d1
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855070"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Ubuntu Data Science Virtual Machine 만들기

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Ubuntu 18.04 Data Science Virtual Machine을 만드는 방법을 보여줍니다. Data Science Virtual Machine은 데이터 과학과 기계 학습 프레임워크 및 도구 제품군이 미리 로드된 클라우드 기반 가상 머신입니다. GPU 기반 컴퓨팅 리소스에 배포되는 경우 모든 도구와 라이브러리는 GPU를 사용하도록 구성됩니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-ubuntu-DSVM-GPU-or-CPU%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/services/machine-learning/)을 만듭니다.

* **로컬 환경**에서 이 문서의 CLI 명령을 사용하려면 [Azure CLI](/cli/azure/install-azure-cli)가 필요합니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json":::

다음 리소스는 템플릿에 정의되어 있습니다.

* [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): 클라우드 기반 가상 머신을 만듭니다. 이 템플릿에서 가상 머신은 Ubuntu 18.04를 실행 하는 Data Science Virtual Machine으로 구성됩니다.

## <a name="deploy-the-template"></a>템플릿 배포

Azure CLI에서 템플릿을 사용하려면 로그인하고 구독을 선택합니다([Azure CLI를 사용하여 로그인](/cli/azure/authenticate-azure-cli) 참조). 다음을 실행합니다.

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey &&
echo "Press [ENTER] to continue ..." &&
read
```

위의 명령을 실행하는 경우 다음을 입력합니다.

1. DSVM 및 연결된 리소스를 포함하도록 만들려는 리소스 그룹의 이름
1. 배포하려는 Azure 위치입니다.
1. 사용할 인증 유형(`password` 또는 `sshPublicKey` 문자열 입력)입니다.
1. 관리자 계정의 로그인 이름(이 값은 `admin`이 될 수 없음)입니다.
1. 계정에 대한 암호 또는 SSH 공개 키의 값입니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Data Science Virtual Machine을 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동
1. 로그인합니다.
1. 방금 만든 리소스 그룹을 선택합니다.

리소스 그룹의 정보가 표시됩니다.

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="DSVM이 포함된 기본 리소스 그룹의 스크린샷":::

Virtual Machine 리소스를 클릭하여 해당 정보 페이지로 이동합니다. 여기에서 연결 세부 정보를 포함하여 VM에 대한 정보를 찾을 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 가상 머신을 사용하지 않으려면 삭제합니다. DSVM이 다른 리소스(예: 스토리지 계정)와 연결되어 있으므로 생성된 전체 리소스 그룹을 삭제하는 것이 좋습니다. **삭제** 단추를 클릭하고 확인하여 포털을 통해 리소스 그룹을 삭제할 수 있습니다. 또는 CLI에서 리소스 그룹을 삭제할 수 있습니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿에서 Data Science Virtual Machine을 만듭니다.

> [!div class="nextstepaction"]
> [샘플 프로그램 및 ML 연습](dsvm-samples-and-walkthroughs.md)
