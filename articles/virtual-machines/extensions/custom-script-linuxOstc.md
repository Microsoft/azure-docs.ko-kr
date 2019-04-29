---
title: Azure의 Linux VM에서 사용자 지정 스크립트 실행 | Microsoft Docs
description: 사용자 지정 스크립트 확장 v1을 사용하여 Linux VM 구성 작업 자동화
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: fe3803b7dc75ab13831a5e42d4b1a96f5aa894e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800295"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Linux 가상 머신에서 Azure 사용자 지정 스크립트 확장 버전 1 사용

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

사용자 지정 스크립트 확장 버전 1은 Azure 가상 머신에서 스크립트를 다운로드하고 실행합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성/관리 작업에 유용합니다. 스크립트를 Azure Storage 또는 기타 액세스가 가능한 인터넷 위치에서 다운로드하거나 확장 런타임을 제공할 수 있습니다.

사용자 지정 스크립트 확장은 Azure Resource Manager 템플릿과 통합됩니다. Azure CLI, PowerShell, Azure Portal 또는 Azure Virtual Machines REST API를 사용하여 실행할 수 있습니다.

이 문서에서는 Azure CLI에서 사용자 지정 스크립트 확장을 사용하는 방법 및 Azure Resource Manager 템플릿을 사용하여 확장을 실행하는 방법을 자세히 설명합니다. 또한 Linux 시스템에 대한 문제 해결 단계도 제공합니다.

두 개의 Linux 사용자 지정 스크립트 확장이 있습니다.

* 버전 1 - Microsoft.OSTCExtensions.CustomScriptForLinux

* 버전 2 - Microsoft.Azure.Extensions.CustomScript

신규 및 기존 배포가 대신 새 버전([Microsoft.Azure.Extensions.CustomScript](custom-script-linux.md))을 사용하도록 전환하세요. 새 버전은 즉시 대체되어 사용됩니다. 따라서 마이그레이션은 이름 및 버전을 변경하는 것만큼 용이합니다. 확장 구성을 변경할 필요가 없습니다.

### <a name="operating-system"></a>운영 체제

지원되는 Linux 배포:

* CentOS 6.5 이상
* Debian 8 이상
  * Debian 8.7은 최신 이미지에서 CustomScriptForLinux를 중단하는 Python2를 함께 제공하지 않습니다.
* FreeBSD
* OpenSUSE 13.1 이상
* Oracle Linux 6.4 이상
* SUSE Linux Enterprise Server 11 SP3 이상
* Ubuntu 12.04 이상

### <a name="script-location"></a>스크립트 위치

확장은 Azure Blob Storage 자격 증명을 사용하여 Azure Blob Storage에 액세스하는 데 사용할 수 있습니다. 또는 스크립트 위치가 VM에서 해당 엔드포인트(예: GitHub, 내부 파일 서버 등)로 라우팅할 수 있는 모든 위치가 될 수 있습니다.

### <a name="internet-connectivity"></a>인터넷 연결

외부 스크립트(예: GitHub 또는 Azure Storage)를 다운로드해야 하는 경우 추가 방화벽/네트워크 보안 그룹 포트를 열어야 합니다. 예를 들어 스크립트가 Azure Storage에 있으면 [Storage](../../virtual-network/security-overview.md#service-tags)에 Azure NSG 서비스 태그를 사용하여 액세스하도록 허용할 수 있습니다.

스크립트가 로컬 서버에 있으면 추가 방화벽/네트워크 보안 그룹 포트도 열어야 합니다.

### <a name="tips-and-tricks"></a>팁과 요령

* 이 확장에 대한 가장 큰 실패율은 스크립트의 구문 오류 때문이며, 오류 없이 스크립트가 실행되는지 테스트하고 실패한 위치를 쉽게 찾을 수 있도록 하는 스크립트에 추가 로깅을 배치합니다.
* idempotent(멱등원)인 스크립트를 작성합니다. 이렇게 하면 스크립트를 실수로 두 번 이상 다시 실행하더라도 시스템이 변경되지 않습니다.
* 스크립트를 실행할 때 사용자 입력이 필요하지 않도록 합니다.
* 스크립트를 실행하는 데 허용되는 시간은 90분입니다. 더 오래 걸리면 확장을 프로비전하는 데 실패합니다.
* 스크립트 내에 재부팅을 배치하지 않습니다. 그렇지 않으면 설치되는 다른 확장에 문제가 발생하고 재부팅 후 다시 시작하면 확장이 계속 실행되지 않습니다. 
* 재부팅할 스크립트가 있는 경우 애플리케이션을 설치하고 스크립트를 실행합니다. Cron 작업 또는 DSC, Chef 또는 Puppet 확장과 같은 도구를 사용하여 재부팅을 예약해야 합니다.
* 확장은 스크립트를 한 번만 실행합니다. 부팅할 때마다 스크립트를 실행하려는 경우 [cloud-init 이미지](../linux/using-cloud-init.md)를 사용하고 [부팅 단위 스크립트](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) 모듈을 사용할 수 있습니다. 또는 Systemd 서비스 단위를 만드는 스크립트를 사용할 수 있습니다.
* 스크립트가 실행될 시기를 예약하려면 Cron 작업을 만드는 확장을 사용해야 합니다.
* 스크립트를 실행하는 경우 Azure Portal 또는 CLI에서 ‘전환 중’ 확장 상태만 표시됩니다. 실행 중인 스크립트의 상태 업데이트를 더 자주 수행하려는 경우 사용자 고유의 솔루션을 만들어야 합니다.
* 사용자 지정 스크립트 확장이 고유하게 프록시 서버를 지원하지는 않지만 *Curl*과 같은 스크립트 내에서 프록시 서버를 지원하는 파일 전송 도구를 사용할 수 있습니다.
* 스크립트 또는 명령에서 사용할 수 있는 기본 디렉터리가 아닌 위치를 알고 있어야 하고, 이를 처리할 논리가 있어야 합니다.

## <a name="extension-schema"></a>확장 스키마

사용자 지정 스크립트 확장 구성은 스크립트 위치 및 실행할 명령 등을 지정합니다. 이 구성을 구성 파일에 저장하거나, 명령줄에 지정하거나, Azure Resource Manager 템플릿에 지정할 수 있습니다. 

중요한 데이터는 보호된 구성에 저장하면 암호화된 후 가상 머신 내에서만 해독됩니다. 보호된 구성은 실행 명령에 암호와 같은 기밀 정보가 포함될 때 유용합니다.

이러한 항목은 중요한 데이터로 처리하고 확장으로 보호되는 설정 구성에 지정되어야 합니다. Azure VM 확장으로 보호되는 설정 데이터는 암호화되어 대상 가상 머신에서만 해독됩니다.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>속성 값

| 이름 | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.OSTCExtensions | 문자열 |
| 형식 | CustomScriptForLinux | 문자열 |
| typeHandlerVersion | 1.5 | int |
| fileUris(예) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute(예) | python MyPythonScript.py \<my-param1\> | 문자열 |
| enableInternalDNSCheck | true | 부울 |
| storageAccountName(예) | examplestorageacct | 문자열 |
| storageAccountKey(예) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | 문자열 |

### <a name="property-value-details"></a>속성 값 세부 정보

* `fileUris`: (선택 사항 문자열 배열) 스크립트의 URI 목록
* `enableInternalDNSCheck`: (선택 사항, bool) 기본값은 True이며 DNS 확인을 해제하려면 False로 설정합니다.
* `commandToExecute`: (선택 사항, 문자열) 실행할 진입점 스크립트.
* `storageAccountName`: (선택 사항, 문자열) 저장소 계정의 이름
* `storageAccountKey`: (선택 사항, 문자열) 저장소 계정의 액세스 키

공용 또는 보호된 설정에서 다음 값을 설정할 수 있습니다. 공용 및 보호된 설정 모두에서 이러한 값을 설정하지 않아야 합니다.

* `commandToExecute`

공용 설정을 사용하면 디버깅에 유용할 수 있지만 보호된 설정을 사용하는 것이 좋습니다.

공용 설정은 스크립트를 실행할 VM에 일반 텍스트로 보내집니다.  보호된 설정은 Azure 및 VM에만 알려진 키를 사용하여 암호화됩니다. 설정은 보내진 대로 VM에 저장됩니다. 즉, 설정이 암호화된 경우 VM에서 암호화되어 저장됩니다. 암호화된 값의 암호를 해독하는 데 사용되는 인증서는 VM에 저장되고, 필요한 경우 런타임 시 설정의 암호를 해독하는 데 사용됩니다.

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 이전 섹션에서 자세히 설명되어 있는 JSON 스키마는 Azure Resource Manager 템플릿에서 사용하여 Azure Resource Manager 템플릿 배포 중 사용자 지정 스크립트 확장을 실행할 수 있습니다.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>이러한 속성 이름은 대/소문자를 구분합니다. 배포 문제를 방지하려면 다음과 같이 이름을 사용합니다.

## <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 사용자 지정 스크립트 확장을 실행하는 경우 구성 파일 또는 파일을 만듭니다. 최소한 ‘commandToExecute’가 있어야 합니다.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

필요에 따라 명령에서 설정을 JSON 형식 문자열로 지정할 수 있습니다. 이렇게 하면 실행 중에 별도 구성 파일 없이 구성을 지정할 수 있습니다.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI 예제

#### <a name="public-configuration-with-no-script-file"></a>스크립트 파일이 없는 공용 구성

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI 명령:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>공용 및 보호된 구성 파일

공용 구성 파일을 사용하여 스크립트 파일 URI를 지정합니다. 보호된 구성 파일을 사용하여 실행할 명령을 지정합니다.

공용 구성 파일:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

보호된 구성 파일:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Azure CLI 명령:

```azurecli
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>문제 해결

사용자 지정 스크립트 확장이 실행되면 스크립트 생성되거나 다음 예제와 비슷한 디렉터리에 다운로드됩니다. 또한 명령 출력은 이 디렉터리의 `stdout` 및 `stderr` 파일에 저장됩니다.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

문제를 해결하려면 먼저 Linux 에이전트 로그를 확인하고, 확장이 실행되는지 확인하고 다음을 검사합니다.

```bash
/var/log/waagent.log
```

다음과 유사한 확장 실행을 찾아야 합니다.

```text
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```

다음 몇 가지 사항을 주의해야 합니다.

1. 사용은 명령이 실행하기 시작할 때입니다.
1. 다운로드는 fileUris에 지정된 스크립트 파일이 아니라 Azure의 CustomScript 확장 패키지의 다운로드에 관련됩니다.
1. 로그 파일에 작성 한 것을 확인할 수 있습니다. `/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

다음 단계는 로그 파일 검사로 이동하는 것입니다. 다음과 같은 형식입니다.

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

개별 실행을 찾아야, 다음과 같은 모양이 됩니다.

```text
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
```

여기서는 다음을 확인할 수 있습니다.

* 이 로그를 시작하는 사용 명령
* 확장에 전달된 설정
* 파일 및 해당 결과를 다운로드하는 확장.
* 실행되는 명령 및 결과.

Azure CLI를 사용하여 사용자 지정 스크립트 확장의 실행 상태를 검색할 수도 있습니다.

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

출력은 다음 텍스트와 비슷합니다.

```azurecli
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>다음 단계

코드, 현재 문제 및 버전을 보려면 [CustomScript 확장 리포지토리](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)를 참조하세요.
