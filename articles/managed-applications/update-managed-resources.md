---
title: Azure 관리되는 애플리케이션에서 리소스 업데이트 | Microsoft Docs
description: Azure 관리되는 애플리케이션의 관리되는 리소스 그룹에서 리소스 작업 방법을 설명합니다.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 21f4e0aa339eb0c746f9b9b06f8aaada6c4d4b71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043459"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Azure 관리되는 애플리케이션의 관리되는 리소스 그룹에서 리소스 작업

이 문서에서는 관리되는 애플리케이션의 일부로 배포되는 리소스를 업데이트하는 방법에 대해 설명합니다. 관리되는 애플리케이션의 게시자는 관리되는 리소스 그룹의 리소스에 대한 액세스 권한이 있습니다. 이러한 리소스를 업데이트하려면 관리되는 애플리케이션과 연결된 관리되는 리소스 그룹을 찾아 해당 리소스 그룹의 리소스에 액세스해야 합니다.

이 문서에서는 [Azure Management Services를 통한 관리되는 웹 애플리케이션(IaaS)](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app) 샘플 프로젝트에서 관리되는 애플리케이션을 배포했다고 가정합니다. 이 관리되는 애플리케이션에는 **Standard_D1_v2** 가상 컴퓨터가 포함됩니다. 이 관리되는 애플리케이션을 배포하지 않은 경우에도 이 문서를 통해 관리되는 리소스 그룹 업데이트를 위한 절차에 익숙해질 수 있습니다.

다음 이미지는 배포된 관리되는 애플리케이션을 보여 줍니다.

![배포된 관리되는 애플리케이션](./media/update-managed-resources/deployed.png)

이 문서에서는 다음을 위해 Azure CLI를 사용합니다.

* 관리되는 애플리케이션 식별
* 관리되는 리소스 그룹 식별
* 관리되는 리소스 그룹의 가상 머신 리소스 식별
* VM 크기 변경(사용하지 않은 경우 더 적은 크기로, 더 큰 로드를 지원하려면 더 큰 크기로)
* 허용되는 위치를 지정하는 관리되는 리소스 그룹에 정책 할당

## <a name="get-managed-application-and-managed-resource-group"></a>관리되는 애플리케이션 및 관리되는 리소스 그룹 가져오기

리소스 그룹에 관리되는 애플리케이션을 가져오려면 다음을 사용합니다.

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

관리되는 리소스 그룹의 ID를 가져오려면 다음을 사용합니다.

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>관리되는 리소스 그룹의 VM 크기 조정

관리되는 리소스 그룹의 가상 머신을 보려면 관리되는 리소스 그룹의 이름을 제공합니다.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

VM의 크기를 업데이트하려면 다음을 사용합니다.

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

작업이 완료된 후 애플리케이션이 Standard D2 v2에서 실행 중인지 확인합니다.

![표준 D2 v2를 사용하는 관리되는 애플리케이션](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>관리되는 리소스 그룹에 정책 적용

이 범위에서 관리되는 리소스 그룹을 가져오고 정책을 할당합니다. **e56962a6-4747-49cd-b67b-bf8b01975c4c** 정책은 허용되는 위치를 지정하기 위한 기본 제공 정책입니다.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

허용되는 위치를 확인하려면 다음을 사용합니다.

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

정책 할당이 포털에 표시됩니다.

![정책 할당 보기](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>다음 단계

* 관리형 애플리케이션에 대한 소개는 [관리형 애플리케이션 개요](overview.md)를 참조하세요.
* 샘플 프로젝트는 [Azure 관리되는 애플리케이션의 샘플 프로젝트](sample-projects.md)를 참조하세요.
