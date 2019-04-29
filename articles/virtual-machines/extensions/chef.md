---
title: Azure VM에 대한 Chef 확장 | Microsoft Docs
description: Chef VM 확장을 사용하여 가상 머신에 Chef 클라이언트를 배포합니다.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: roiyz
ms.openlocfilehash: 6bd3ea4e664523fe8014be40c51d573ed5158ecf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800273"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Linux 및 Windows에 대한 Chef VM 확장

Chef Software는 실제 및 가상 서버 구성의 관리를 활성화하는 Linux 및 Windows용 DevOps 자동화 플랫폼을 제공합니다. Chef VM 확장은 가상 머신에서 Chef를 사용하도록 설정하는 확장입니다.

## <a name="prerequisites"></a>필수 조건

### <a name="operating-system"></a>운영 체제

Chef VM 확장은 Azure의 모든 [확장 지원 OS](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)에 지원됩니다.

### <a name="internet-connectivity"></a>인터넷 연결

Chef VM 확장은 CDN(콘텐츠 배달 네트워크)에서 Chef 클라이언트 페이로드를 검색하기 위해 대상 가상 머신을 인터넷에 연결해야 합니다.  

## <a name="extension-schema"></a>확장 스키마

Chef VM 확장에 대한 스키마를 보여주는 JSON은 다음과 같습니다. 확장에는 최소한 Chef 서버에 대한 최소 Chef 서버 URL, 유효성 검사 클라이언트 이름 및 유효성 검사 키가 필요합니다. 이러한 값은 [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) 또는 독립 실행형 [Chef 서버](https://downloads.chef.io/chef-server)를 설치할 때 다운로드되는 starter-kit.zip의 `knife.rb` 파일에서 찾을 수 있습니다. 유효성 검사 키가 중요한 데이터로 처리되므로 **protectedSettings** 요소에서 구성되어야 합니다. 즉, 대상 가상 머신에서만 암호가 해독됩니다.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.12",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>핵심 속성 값

| 이름 | 값/예제 | 데이터 형식
| ---- | ---- | ---- 
| apiVersion | `2017-12-01` | 문자열(날짜) |
| publisher | `Chef.Bootstrap.WindowsAzure` | 문자열 |
| 형식 | `LinuxChefClient`(Linux), `ChefClient`(Windows) | 문자열 |
| typeHandlerVersion | `1210.12` | 문자열(double) |

### <a name="settings"></a>설정

| 이름 | 값/예제 | 데이터 형식 | Required?
| ---- | ---- | ---- | ----
| settings/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | 문자열(URL) | Y |
| settings/bootstrap_options/validation_client_name | `myorg-validator` | 문자열 | Y |
| settings/runlist | `recipe[mycookbook::default]` | 문자열 | Y |

### <a name="protected-settings"></a>보호 설정

| 이름 | 예 | 데이터 형식 | Required?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | 문자열 | Y |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 템플릿은 [실행 목록](https://docs.chef.io/run_lists.html)에서 정의된 대로 하나 이상의 가상 머신을 배포하고, Chef 클라이언트를 설치하고, Chef 서버에 연결하고, 서버에서 초기 구성을 수행하는 데 사용할 수 있습니다.

Chef VM 확장을 포함하는 샘플 Resource Manager 템플릿은 [Azure 빠른 시작 갤러리](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm)에서 찾을 수 있습니다.

가상 머신 확장에 대한 JSON 구성은 가상 머신 리소스 내에 중첩되거나 루트 또는 최상위 수준의 Resource Manager JSON 템플릿에 배치될 수 있습니다. JSON 구성의 배치는 리소스 이름 및 형식 값에 영향을 줍니다. 자세한 내용은 [자식 리소스의 이름 및 형식 설정](../../azure-resource-manager/resource-manager-template-child-resource.md)을 참조하세요.

## <a name="azure-cli-deployment"></a>Azure CLI 배포

Azure CLI를 사용하여 Chef VM 확장을 기존 VM에 배포할 수 있습니다. **validation_key**를 유효성 검사 키의 콘텐츠로 바꿉니다(파일을 `.pem` 확장으로 변환).  **validation_client_name**, **chef_server_url** 및 **run_list**는 시작 키트의 `knife.rb` 파일에서 해당 값으로 바꿉니다.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.12 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>문제 해결 및 지원

확장 배포 상태에 대한 데이터는 Azure CLI를 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

확장 실행 출력은 다음 파일에 기록됩니다.

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>오류 코드 및 해당 의미

| 오류 코드 | 의미 | 가능한 작업 |
| :---: | --- | --- |
| 51 | 이 확장이 VM의 운영 체제에서 지원되지 않습니다. | |

추가 문제 해결 정보는 [Chef VM 확장 추가 정보](https://github.com/chef-partners/azure-chef-extension)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
