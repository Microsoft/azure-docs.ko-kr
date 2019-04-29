---
title: Azure의 Linux VM에서 사용자 지정 스크립트 실행 | Microsoft Docs
description: 사용자 지정 스크립트 확장 v2를 사용하여 Linux VM 구성 작업 자동화
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: roiyz
ms.openlocfilehash: 19637a1fe49550d0ed7aea7e3a596f1f77f5984b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60869880"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Linux 가상 머신에서 Azure 사용자 지정 스크립트 확장 버전 2 사용
사용자 지정 스크립트 확장 버전 2는 Azure 가상 머신에서 스크립트를 다운로드하고 실행합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성/관리 작업에 유용합니다. 스크립트를 Azure Storage 또는 기타 액세스가 가능한 인터넷 위치에서 다운로드하거나 확장 런타임을 제공할 수 있습니다. 

사용자 지정 스크립트 확장은 Azure Resource Manager 템플릿과 통합됩니다. Azure CLI, PowerShell 또는 Azure Virtual Machines REST API를 사용하여 실행할 수도 있습니다.

이 문서에서는 Azure CLI에서 사용자 지정 스크립트 확장을 사용하는 방법 및 Azure Resource Manager 템플릿을 사용하여 확장을 실행하는 방법을 자세히 설명합니다. 또한 Linux 시스템에 대한 문제 해결 단계도 제공합니다.


두 개의 Linux 사용자 지정 스크립트 확장이 있습니다.
* 버전 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* 버전 2 - Microsoft.Azure.Extensions.CustomScript

새 버전 2를 대신 사용하도록 신규 및 기존 배포를 전환하세요. 새 버전은 즉시 대체되어 사용됩니다. 따라서 마이그레이션은 이름 및 버전을 변경하는 것만큼 용이합니다. 확장 구성을 변경할 필요가 없습니다.


### <a name="operating-system"></a>운영 체제

Linux용 사용자 지정 스크립트 확장은 지원되는 확장 OS의 확장에서 실행됩니다. 자세한 내용은 이 [문서](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems)를 참조하세요.

### <a name="script-location"></a>스크립트 위치

확장은 Azure Blob Storage 자격 증명을 사용하여 Azure Blob Storage에 액세스하는 데 사용할 수 있습니다. 또는 스크립트 위치가 VM에서 해당 엔드포인트(예: GitHub, 내부 파일 서버 등)로 라우팅할 수 있는 모든 위치가 될 수 있습니다.

### <a name="internet-connectivity"></a>인터넷 연결
외부 스크립트(예: GitHub 또는 Azure Storage)를 다운로드해야 하는 경우 추가 방화벽/네트워크 보안 그룹 포트를 열어야 합니다. 예를 들어 스크립트가 Azure Storage에 있으면 [Storage](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)에 Azure NSG 서비스 태그를 사용하여 액세스하도록 허용할 수 있습니다.

스크립트가 로컬 서버에 있으면 추가 방화벽/네트워크 보안 그룹 포트도 열어야 합니다.

### <a name="tips-and-tricks"></a>팁과 요령
* 이 확장에 대한 가장 큰 실패율은 스크립트의 구문 오류 때문이며, 오류 없이 스크립트가 실행되는지 테스트하고 실패한 위치를 쉽게 찾을 수 있도록 하는 스크립트에 추가 로깅을 배치합니다.
* idempotent(멱등원)인 스크립트를 작성합니다. 이렇게 하면 스크립트를 실수로 두 번 이상 다시 실행하더라도 시스템이 변경되지 않습니다.
* 스크립트를 실행할 때 사용자 입력이 필요하지 않도록 합니다.
* 스크립트를 실행하는 데 허용되는 시간은 90분입니다. 더 오래 걸리면 확장을 프로비전하는 데 실패합니다.
* 스크립트 내에 재부팅을 배치하지 않습니다. 그렇지 않으면 설치되는 다른 확장에 문제가 발생하고 재부팅 후 다시 시작하면 확장이 계속 실행되지 않습니다. 
* 재부팅할 스크립트가 있는 경우 애플리케이션을 설치하고 스크립트를 실행합니다. Cron 작업 또는 DSC, Chef 또는 Puppet 확장과 같은 도구를 사용하여 재부팅을 예약해야 합니다.
* 확장은 스크립트를 한 번만 실행합니다. 부팅할 때마다 스크립트를 실행하려는 경우 [cloud-init 이미지](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)를 사용하고 [부팅 단위 스크립트](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) 모듈을 사용할 수 있습니다. 또는 Systemd 서비스 단위를 만드는 스크립트를 사용할 수 있습니다.
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
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."]  
    }
  }
}
```

### <a name="property-values"></a>속성 값

| 이름 | 값/예제 | 데이터 형식 | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute.Extensions | 문자열 |
| 형식 | CustomScript | 문자열 |
| typeHandlerVersion | 2.0 | int |
| fileUris(예) | https://github.com/MyProject/Archive/MyPythonScript.py | array |
| commandToExecute(예) | python MyPythonScript.py <my-param1> | 문자열 |
| script | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | 문자열 |
| skipDos2Unix(예) | false | 부울 |
| timestamp(예) | 123456789 | 32비트 정수 |
| storageAccountName(예) | examplestorageacct | 문자열 |
| storageAccountKey(예) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | 문자열 |

### <a name="property-value-details"></a>속성 값 세부 정보
* `skipDos2Unix`: (옵션, 부울) 스크립트 기반 파일 URL 또는 스크립트의 dos2unix 변환을 건너뜁니다.
* `timestamp`: (옵션, 32비트 정수) 이 필드는 이 필드의 값을 변경하여 스크립트의 다시 실행을 트리거하는 데만 사용합니다.  모든 정수 값을 사용할 수 있습니다. 단, 이전 값과 달라야 합니다.
  * `commandToExecute`: (스크립트를 설정하지 않은 경우 **필수**, 문자열) 실행할 진입점 스크립트입니다. 명령에 암호와 같은 기밀 정보가 포함되는 경우 이 필드를 대신 사용합니다.
* `script`: (commandToExecute를 설정하지 않은 경우 **필수**, 문자열) /bin/sh로 실행된 base64 인코딩(및 필요에 따라 gzip 압축) 스크립트입니다.
* `fileUris`: (옵션, 문자열 배열) 다운로드할 파일에 대한 URL입니다.
* `storageAccountName`: (옵션, 문자열) 저장소 계정에 대한 이름입니다. 저장소 자격 증명을 지정하는 경우 모든 `fileUris`는 Azure Blob에 대한 URL이어야 합니다.
* `storageAccountKey`: (옵션, 문자열) 저장소 계정의 액세스 키입니다.


다음 값은 공용 또는 보호된 설정 중 하나에서 설정할 수 있습니다. 확장은 공용 및 보호된 설정 모두에 아래 값이 설정된 모든 구성을 거부합니다.
* `commandToExecute`
* `script`
* `fileUris`

공용 설정을 사용하면 디버깅에 유용할 수 있지만 보호된 설정을 사용하는 것이 좋습니다.

공용 설정은 스크립트를 실행할 VM에 일반 텍스트로 보내집니다.  보호된 설정은 Azure 및 VM에만 알려진 키를 사용하여 암호화됩니다. 설정은 보내진 대로 VM에 저장됩니다. 즉, 설정이 암호화된 경우 VM에서 암호화되어 저장됩니다. 암호화된 값의 암호를 해독하는 데 사용되는 인증서는 VM에 저장되고, 필요한 경우 런타임 시 설정의 암호를 해독하는 데 사용됩니다.

#### <a name="property-skipdos2unix"></a>속성: skipDos2Unix

기본값은 false이며, dos2unix 변환이 실행**되었음**을 의미합니다.

이전 버전의 CustomScript인 Microsoft.OSTCExtensions.CustomScriptForLinux는 `\r\n`을 `\n`으로 변환하여 DOS 파일을 UNIX 파일로 자동으로 변환합니다. 이 변환은 여전히 존재하며 기본적으로 설정되어 있습니다. 이 변환은 fileUris에서 다운로드된 모든 파일 또는 다음 조건 중 하나를 기반으로 한 스크립트 설정에 적용됩니다.

* 확장이 `.sh`, `.txt`, `.py` 또는 `.pl` 중 하나인 경우 변환됩니다. /bin/sh를 사용하여 실행되는 스크립트로 간주되며 VM에서 script.sh로 저장되므로 스크립트 설정은 항상 이 조건에 일치합니다.
* 파일이 `#!`로 시작되는 경우.

dos2unix 변환은 skipDos2Unix를 true로 설정하여 건너뛸 수 있습니다.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>속성: 스크립트

CustomScript는 사용자 정의 스크립트의 실행을 지원합니다. 스크립트 설정은 commandToExecute 및 fileUris를 단일 설정으로 결합합니다. Azure Storage 또는 GitHub gist에서 다운로드하기 위해 파일을 설치하는 대신 간단히 스크립트를 설정으로 인코딩할 수 있습니다. 스크립트를 commandToExecute 및 fileUris를 대신하여 사용할 수 있습니다.

스크립트는 **반드시** base64 인코딩해야 합니다.  스크립트는 **선택적으로** gzip 압축할 수 있습니다. 공용 또는 보호 설정에서 스크립트 설정을 사용할 수 있습니다. 스크립트 매개 변수의 최대 데이터 크기는 256KB입니다. 스크립트가 이 크기를 초과하는 경우에는 실행되지 않습니다.

예를 들어, /script.sh/ 파일에 저장된 다음 스크립트를 고려해 보겠습니다.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

올바른 CustomScript 스크립트 설정은 다음 명령의 출력을 사용하여 구성됩니다.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

스크립트는 나중에 크기를 줄이기 위해(대부분의 경우) 선택적으로 gzip 압축할 수 있습니다. (CustomScript는 gzip 압축의 사용을 자동 검색합니다.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript는 다음 알고리즘을 사용하여 스크립트를 실행합니다.

 1. 스크립트 값의 길이가 256KB를 초과하지 않도록 어설션
 1. 스크립트의 값을 Base64 디코드
 1. Base64 디코딩된 값의 gzip 압축 _시도_
 1. 디스크에 디코딩된(및 선택적으로 압축된) 값을 기록(/var/lib/waagent/custom-script/#/script.sh)
 1. _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh를 사용하여 스크립트 실행


## <a name="template-deployment"></a>템플릿 배포
Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 이전 섹션에서 자세히 설명되어 있는 JSON 스키마는 Azure Resource Manager 템플릿에서 사용하여 Azure Resource Manager 템플릿 배포 중 사용자 지정 스크립트 확장을 실행할 수 있습니다. 사용자 지정 스크립트 확장이 포함된 샘플 템플릿은 여기 [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)에서 확인할 수 있습니다.


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
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>이러한 속성 이름은 대/소문자를 구분합니다. 배포 문제를 방지하려면 다음과 같이 이름을 사용합니다.

## <a name="azure-cli"></a>Azure CLI
Azure CLI를 사용하여 사용자 지정 스크립트 확장을 실행하는 경우 구성 파일 또는 파일을 만듭니다. 최소한 ‘commandToExecute’가 있어야 합니다.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

필요에 따라 명령에서 설정을 JSON 형식 문자열로 지정할 수 있습니다. 이렇게 하면 실행 중에 별도 구성 파일 없이 구성을 지정할 수 있습니다.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI 예제

#### <a name="public-configuration-with-script-file"></a>스크립트 파일이 있는 공용 구성

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Azure CLI 명령:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

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
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
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
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>문제 해결
사용자 지정 스크립트 확장이 실행되면 스크립트 생성되거나 다음 예제와 비슷한 디렉터리에 다운로드됩니다. 또한 명령 출력은 이 디렉터리의 `stdout` 및 `stderr` 파일에 저장됩니다.

```bash
/var/lib/waagent/custom-script/download/0/
```

문제를 해결하려면 먼저 Linux 에이전트 로그를 확인하고, 확장이 실행되는지 확인하고 다음을 검사합니다.

```bash
/var/log/waagent.log 
```

다음과 유사한 확장 실행을 찾아야 합니다.
```text
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```
다음 몇 가지 사항을 주의해야 합니다.
1. 사용은 명령이 실행하기 시작할 때입니다.
2. 다운로드는 fileUris에 지정된 스크립트 파일이 아니라 Azure의 CustomScript 확장 패키지의 다운로드에 관련됩니다.


Azure 스크립트 확장은 여기에서 찾을 수 있는 로그를 생성합니다.

```bash
/var/log/azure/custom-script/handler.log
```

개별 실행을 찾아야, 다음과 같은 모양이 됩니다.
```text
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
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
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>다음 단계
코드, 현재 문제 및 버전을 보려면 [custom-script-extension-linux 리포지토리](https://github.com/Azure/custom-script-extension-linux)를 참조하세요.

