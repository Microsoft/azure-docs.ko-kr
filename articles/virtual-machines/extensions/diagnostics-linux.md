---
title: Azure Compute - Linux 진단 확장 | Microsoft Docs
description: Azure에서 실행 중인 Linux VM에서 메트릭 및 로그 이벤트를 수집하도록 Azure LAD(Linux 진단 확장)를 구성하는 방법입니다.
services: virtual-machines-linux
author: abhijeetgaiha
manager: sankalpsoni
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: agaiha
ms.openlocfilehash: af5d4e21bb5b41df4bcb88dc2f9eb7901fcaa597
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129214"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Linux 진단 확장을 사용하여 메트릭 및 로그 모니터링

이 문서에서는 3.0 이상 버전의 Linux 진단 확장에 대해 설명합니다.

> [!IMPORTANT]
> 2.3 이하 버전에 대한 내용은 [이 문서](../linux/classic/diagnostic-extension-v2.md)를 참조하세요.

## <a name="introduction"></a>소개

Linux 진단 확장을 통해 사용자는 Microsoft Azure에서 실행하는 Linux VM의 상태를 모니터링할 수 있습니다. 제공되는 기능은 다음과 같습니다.

* VM에서 시스템 성능 메트릭을 수집하여 지정된 저장소 계정의 특정 테이블에 저장합니다.
* syslog에서 로그 이벤트를 검색하여 지정된 저장소 계정의 특정 테이블에 저장합니다.
* 사용자가 수집 및 업로드된 데이터 메트릭을 사용자 지정할 수 있도록 설정합니다.
* 사용자가 수집 및 업로드된 이벤트의 심각도 수준과 syslog 기능을 사용자 지정할 수 있도록 설정합니다.
* 사용자가 지정된 저장소 테이블에 지정된 로그 파일을 업로드할 수 있도록 설정합니다.
* 지정된 저장소 계정의 JSON 형식 Blob 및 임의 EventHub 엔드포인트로 메트릭과 로그 이벤트 전송을 지원합니다.

이 확장은 Azure 배포 모델 모두에서 작동합니다.

## <a name="installing-the-extension-in-your-vm"></a>VM에 확장 설치

Azure PowerShell cmdlet, Azure CLI 스크립트, ARM 템플릿 또는 Azure Portal을 통해 이 확장을 사용하도록 설정할 수 있습니다. 자세한 내용은 [확장 기능](features-linux.md)을 참조하세요.

이러한 설치 지침 및 [다운로드 가능한 샘플 구성](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json)은 다음을 수행할 수 있도록 LAD 3.0을 구성합니다.

* LAD 2.3에서 제공하는 것과 동일한 메트릭을 캡처 및 저장합니다.
* LAD 3.0의 새로운 기능인 유용한 파일 시스템 메트릭 집합을 캡처합니다.
* LAD 2.3에서 사용하도록 설정된 기본 syslog 컬렉션을 캡처합니다.
* VM 메트릭에 대한 차트 및 경고에 대해 Azure Portal 환경을 사용하도록 설정합니다.

다운로드 가능한 구성은 예로 든 것일 뿐입니다. 사용자 요구 사항에 맞게 수정합니다.

### <a name="prerequisites"></a>필수 조건

* **Azure Linux 에이전트 버전 2.2.0 이상**. 대부분의 Azure VM Linux 갤러리 이미지에는 2.2.7 이후 버전이 포함되어 있습니다. VM에 설치된 버전을 확인하려면 `/usr/sbin/waagent -version`을 실행합니다. VM이 게스트 에이전트의 이전 버전을 실행 중인 경우 [이 지침](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent)에 따라 업데이트합니다.
* **Azure CLI**. 머신에 [Azure CLI 환경을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다.
* wget 명령. 아직 없는 경우 `sudo apt-get install wget`을 실행합니다.
* 데이터를 저장할 기존 Azure 구독 및 기존 저장소 계정
* 지원되는 Linux 배포 목록은 https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions에 있습니다.

### <a name="sample-installation"></a>샘플 설치

처음 세 줄에 올바른 매개 변수를 입력한 다음 루트로 이 스크립트를 실행합니다.

```bash
# Set your Azure VM diagnostic parameters correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

샘플 구성의 URL과 해당 내용은 변경될 수 있습니다. 포털 설정 JSON 파일의 복사본을 다운로드하고 요구 사항에 맞게 사용자 지정합니다. 템플릿 또는 자동화를 생성할 때마다 해당 URL을 다운로드하는 대신 고유한 복사본을 사용해야 합니다.

### <a name="updating-the-extension-settings"></a>확장 설정 업데이트

보호 또는 공용 설정을 변경한 후 동일한 명령을 실행하여 VM에 배포합니다. 설정에서 변경된 사항이 있으면 업데이트된 설정이 확장에 전송됩니다. LAD가 구성을 다시 로드하고 자체를 다시 시작합니다.

### <a name="migration-from-previous-versions-of-the-extension"></a>이전 확장 버전에서 마이그레이션

최신 확장 버전은 **3.0**입니다. **모든 이전 버전(2.x)은 사용되지 않으며 2018년 7월 31일부터는 게시되지 않을 수 있습니다**.

> [!IMPORTANT]
> 이 확장에서는 확장 구성에 대해 새로운 변경 사항을 도입했습니다. 이러한 변경 사항은 확장의 보안을 향상시키기 위한 것이며 이에 따라 이전 2.x 버전과는 호환되지 않습니다. 또한 이 확장의 확장 게시자는 2.x 버전의 게시자와 다릅니다.
>
> 확장을 2.x에서 이 새 버전으로 마이그레이션하려면 이전 게시자 이름 아래의 확장을 제거한 다음 버전 3의 확장을 설치해야 합니다.

권장 사항:

* 자동 부 버전 업그레이드를 사용하도록 설정하여 확장을 설치합니다.
  * 클래식 배포 모델 VM에서는 Azure XPLAT CLI 또는 Powershell을 통해 확장을 설치하는 경우 버전으로 '3.*'를 지정합니다.
  * Azure Resource Manager 배포 모델 VM에서는 VM 배포 템플릿에 '"autoUpgradeMinorVersion": true'를 포함합니다.
* LAD 3.0에 대해 새 저장소 계정 또는 다른 저장소 계정을 사용합니다. LAD 2.3과 LAD 3.0 간에 다음과 같은 몇 가지 사소한 비호환성 문제가 있어 계정 공유에 문제가 발생합니다.
  * LAD 3.0은 syslog 이벤트를 이름이 다른 테이블에 저장합니다.
  * `builtin` 메트릭에 대한 counterSpecifier 문자열이 LAD 3.0에서 다릅니다.

## <a name="protected-settings"></a>보호 설정

이 구성 정보 집합에는 공용 보기로부터 보호해야 하는 중요한 정보(예: 저장소 자격 증명)가 포함되어 있습니다. 이러한 설정은 확장에 의해 전송되어 암호화된 형태로 저장됩니다.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

이름 | 값
---- | -----
storageAccountName | 확장에 의해 데이터가 기록될 저장소 계정의 이름입니다.
storageAccountEndPoint | (선택 사항) 저장소 계정이 있는 클라우드를 식별하는 엔드포인트입니다. 이 설정이 없는 경우 LAD는 Azure 공용 클라우드, `https://core.windows.net`으로 기본 설정됩니다. Azure Germany, Azure Government 또는 Azure China에서 저장소 계정을 사용하려면 이 값을 적절하게 설정합니다.
storageAccountSasToken | Blob service 및 Table service(`ss='bt'`)용으로, 컨테이너 및 개체(`srt='co'`)에 적용할 수 있고, 추가, 생성, 나열, 업데이트 및 쓰기 권한(`sp='acluw'`)을 부여하는 [계정 SAS 토큰](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/)입니다. 앞에 물음표(?)를 포함하지 *마세요*.
mdsdHttpProxy | (선택 사항) 지정된 저장소 계정 및 엔드포인트에 연결할 확장을 사용하도록 설정하는 데 필요한 HTTP 프록시 정보입니다.
sinksConfig | (선택 사항) 메트릭 및 이벤트를 전달할 수 있는 대체 대상의 세부 정보입니다. 확장에서 지원되는 각 데이터 싱크의 특정 세부 정보는 다음에 나오는 섹션에 설명되어 있습니다.


> [!NOTE]
> Azure 배포 템플릿을 사용하여 확장을 배포할 때는 저장소 계정 및 SAS 토큰을 미리 만든 후 템플릿에 전달해야 합니다. 단일 템플릿에서 VM, 저장소 계정 배포와 확장 구성을 함께 수행할 수 없습니다. 템플릿 내에서 SAS 토큰을 만드는 방식은 현재 지원되지 않습니다.

Azure Portal을 통해 필요한 SAS 토큰을 쉽게 생성할 수 있습니다.

1. 확장에서 쓰게 할 범용 저장소 계정을 선택합니다.
1. 왼쪽 메뉴의 설정 부분에서 "공유 액세스 서명"을 선택합니다.
1. 앞에서 설명한 대로 적절한 섹션을 만듭니다.
1. "SAS 생성" 단추를 클릭합니다.

![이미지](./media/diagnostics-linux/make_sas.png)

생성된 SAS를 storageAccountSasToken 필드에 복사하고 앞의 물음표("?")를 제거합니다.

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

이 선택적 섹션은 확장에서 수집한 정보를 보낼 추가 대상을 정의합니다. "sink" 배열에 각 추가 데이터 싱크에 대한 개체를 포함합니다. "type" 특성은 개체의 다른 특성을 결정합니다.

요소 | 값
------- | -----
이름 | 확장 구성의 다른 위치에서 이 싱크를 참조하는 데 사용되는 문자열입니다.
형식 | 정의 중인 싱크 유형입니다. 이 유형의 인스턴스에서 다른 값(있는 경우)을 결정합니다.

Linux 진단 확장 3.0 버전에서는 두 개의 싱크 유형 EventHub 및 JsonBlob을 지원합니다.

#### <a name="the-eventhub-sink"></a>EventHub 싱크

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

"sasURL" 항목에는 SAS 토큰을 포함하여 데이터를 게시해야 하는 Event Hub에 대한 전체 URL이 포함됩니다. LAD는 클레임 보내기를 사용하도록 설정하는 SAS 명명 정책이 필요합니다. 예제:

* 호출된 Event Hubs 네임스페이스 만들기`contosohub`
* `syslogmsgs`라는 네임스페이스에 Event Hub 만들기
* Event Hub에 클레임 보내기를 사용하도록 설정하는 `writer`라는 공유 액세스 정책 만들기

2018년 1월 1일 UTC 자정까지 SAS 제품을 만든 경우 sasURL 값은 다음과 같을 수 있습니다.

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Event Hubs에 대한 SAS 토큰을 생성하는 방법에 대한 자세한 내용은 [이 웹 페이지](../../event-hubs/event-hubs-authentication-and-security-model-overview.md)를 참조하세요.

#### <a name="the-jsonblob-sink"></a>JsonBlob 싱크

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

JsonBlob 싱크로 전달되는 데이터는 Azure Storage의 Blob에 저장됩니다. 각 LAD 인스턴스는 매시간 각 싱크 이름에 대한 Blob을 만듭니다. 각 Blob에는 항상 구문상 유효한 JSON 배열의 개체가 포함되어 있습니다. 새 항목이 배열에 원자 단위로 추가됩니다. Blob은 싱크와 동일한 이름을 가진 컨테이너에 저장됩니다. Blob 컨테이너 이름에 대한 Azure Storage 규칙이 JsonBlob 싱크의 이름에 적용되며 3-63자의 소문자 영숫자 ASCII 문자 또는 대시로 구성됩니다.

## <a name="public-settings"></a>공용 설정

이 구조는 확장에서 수집한 정보를 제어하는 다양한 설정 블록을 포함합니다. 각 설정은 선택 사항입니다. `ladCfg`를 지정할 경우 `StorageAccount`도 지정해야 합니다.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

요소 | 값
------- | -----
StorageAccount | 확장에 의해 데이터가 기록될 저장소 계정의 이름입니다. [보호 설정](#protected-settings)에서 지정된 이름과 동일해야 합니다.
mdsdHttpProxy | (선택 사항) [보호 설정](#protected-settings)에서와 동일해야 합니다. 공용 값은 개인 값(설정된 경우)으로 재정의됩니다. [보호 설정](#protected-settings)에서 비밀(예: 암호)을 포함하는 프록시 설정을 배치합니다.

남아 있는 요소는 다음 섹션에 자세히 설명되어 있습니다.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

이 선택적 구조는 Azure Metrics 서비스 및 기타 데이터 싱크에 전달할 메트릭과 로그의 수집을 제어합니다. `performanceCounters`나 `syslogEvents` 또는 둘 다 지정해야 합니다. `metrics` 구조를 지정해야 합니다.

요소 | 값
------- | -----
eventVolume | (선택 사항) 저장소 테이블 내에서 만든 파티션의 수를 제어합니다. `"Large"`, `"Medium"` 또는 `"Small"` 중 하나여야 합니다. 지정하지 않으면 기본값 `"Medium"`입니다.
sampleRateInSeconds | (선택 사항) 원시(집계되지 않은) 메트릭 컬렉션 간의 기본 간격입니다. 지원되는 가장 작은 샘플 속도는 15초입니다. 지정하지 않으면 기본값 `15`입니다.

#### <a name="metrics"></a>메트릭

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

요소 | 값
------- | -----
ResourceId | VM 또는 VM이 속한 가상 머신 확장 집합의 Azure Resource Manager 리소스 ID입니다. JsonBlob 싱크가 구성에 사용되는 경우 이 설정도 지정해야 합니다.
scheduledTransferPeriod | 집계 메트릭이 계산되어 Azure Metrics로 전송되는 빈도이며 IS 8601 시간 간격으로 표시됩니다. 최소 전송 기간은 60초, 즉 PT1M입니다. 하나 이상의 scheduledTransferPeriod를 지정해야 합니다.

performanceCounters 섹션에 지정된 메트릭 샘플은 15초마다 또는 카운터에 명시적으로 정의된 샘플 속도로 수집됩니다. 예제와 같이 여러 scheduledTransferPeriod 빈도가 나타날 경우 각 집계는 독립적으로 계산됩니다.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

이 선택적 섹션은 메트릭 컬렉션을 제어합니다. 원시 샘플은 각 [scheduledTransferPeriod](#metrics)별로 집계되어 다음 값을 생성합니다.

* 평균
* minimum
* maximum
* 마지막으로 수집된 값
* 집계를 계산하는 데 사용되는 원시 샘플 수

요소 | 값
------- | -----
sinks | (선택 사항) LAD가 집계된 메트릭 결과를 보내는 쉼표로 구분된 싱크 이름 목록입니다. 모든 집계된 메트릭은 나열된 각 싱크에 게시됩니다. [sinksConfig](#sinksconfig)를 참조하세요. 예: `"EHsink1, myjsonsink"`.
형식 | 메트릭의 실제 공급자를 식별합니다.
class | "counter"와 함께 공급자의 네임스페이스 내에서 특정 메트릭을 식별합니다.
counter | "class"와 함께 공급자의 네임스페이스 내에서 특정 메트릭을 식별합니다.
counterSpecifier | Azure Metrics 네임스페이스 내에서 특정 메트릭을 식별합니다.
condition | (선택 사항) 메트릭이 적용되는 개체의 특정 인스턴스를 선택하거나 해당 개체의 모든 인스턴스에서 집계를 선택합니다. 자세한 내용은 `builtin` 메트릭 정의를 참조하세요.
sampleRate | 이 메트릭의 원시 샘플이 수집되는 속도를 설정하는 IS 8601 간격입니다. 설정하지 않은 경우 컬렉션 간격은 [sampleRateInSeconds](#ladcfg) 값으로 설정됩니다. 지원되는 최소 샘플 속도는 15초(PT15S)입니다.
단위 | 문자열 "Count", "Bytes", "Seconds", "Percent", "CountPerSecond", "BytesPerSecond", "Millisecond" 중 하나여야 합니다. 메트릭에 대한 단위를 정의합니다. 수집된 데이터의 소비자는 수집된 데이터 값이 이 단위와 일치할 것으로 예상합니다. LAD는 이 필드를 무시합니다.
displayName | Azure Metrics에서 이 데이터에 연결되는 레이블(연결된 로캘 설정에서 지정된 언어)입니다. LAD는 이 필드를 무시합니다.

counterSpecifier는 임의의 식별자입니다. Azure Portal 차트 및 경고 기능과 같이 메트릭의 소비자는 counterSpecifier를 메트릭 또는 메트릭 인스턴스를 식별하는 "키"로 사용합니다. `builtin` 메트릭의 경우 `/builtin/`으로 시작하는 counterSpecifier 값을 사용하는 것이 좋습니다. 특정 메트릭 인스턴스를 수집하는 경우 counterSpecifier 값에 인스턴스 식별자를 연결하는 것이 좋습니다. 일부 사례:

* `/builtin/Processor/PercentIdleTime` - 모든 vCPU의 평균 유휴 시간
* `/builtin/Disk/FreeSpace(/mnt)` - /mnt 파일 시스템의 사용 가능한 공간
* `/builtin/Disk/FreeSpace` - 탑재된 모든 파일 시스템의 평균 사용 가능한 공간

LAD나 Azure Portal에서는 counterSpecifier 값이 패턴에 일치할 것으로 예상하지 않습니다. 일관된 방법으로 counterSpecifier 값을 생성해야 합니다.

`performanceCounters`를 지정할 경우 LAD는 항상 Azure Storage의 테이블에 데이터를 작성합니다. JSON Blob 및/또는 Event Hubs에 동일한 데이터를 작성할 수 있지만 테이블에 데이터를 저장하지 않도록 할 수는 없습니다. 동일한 스토리지 계정 이름 및 엔드포인트를 사용하도록 구성된 진단 확장의 모든 인스턴스는 해당 메트릭과 로그를 동일한 테이블에 추가합니다. 너무 많은 VM이 동일한 테이블 파티션에 작성할 경우 Azure는 해당 파티션에 쓰기를 제한할 수 있습니다. eventVolume 설정은 항목이 1(Small), 10(Medium) 또는 100(Large)개의 서로 다른 파티션에 분산되도록 합니다. 일반적으로 트래픽이 제한되지 않도록 하는 데 "Medium"이면 충분합니다. Azure Portal의 Azure Metrics 기능은 이 테이블의 데이터를 사용하여 그래프를 생성하거나 경고를 트리거합니다. 테이블 이름은 다음 문자열이 연결된 것입니다.

* `WADMetrics`
* 테이블에 저장된 집계 값에 대한 "scheduledTransferPeriod"
* `P10DV2S`
* 10일마다 변경되는 "YYYYMMDD" 형식의 날짜

예에는 `WADMetricsPT1HP10DV2S20170410` 및 `WADMetricsPT1MP10DV2S20170609`가 포함됩니다.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

이 선택적 섹션은 syslog의 로그 이벤트 컬렉션을 제어합니다. 섹션을 생략할 경우 syslog 이벤트는 전혀 캡처되지 않습니다.

syslogEventConfiguration 컬렉션에는 각각의 특정 syslog 기능별로 하나의 항목이 있습니다. minSeverity가 특정 기능에 대해 "NONE"이거나 해당 기능이 요소에 전혀 표시되지 않을 경우 해당 기능의 이벤트는 캡처되지 않습니다.

요소 | 값
------- | -----
sinks | 개별 로그 이벤트가 게시되는 쉼표로 구분된 싱크 이름 목록입니다. syslogEventConfiguration의 제한 사항과 일치하는 모든 로그 이벤트가 나열된 각 싱크에 게시됩니다. 예제: "EHforsyslog"
facilityName | syslog 기능 이름입니다(예: "LOG\_USER" 또는 "LOG\_LOCAL0"). 전체 목록은 [syslog 기본 페이지](http://man7.org/linux/man-pages/man3/syslog.3.html)의 "facility" 섹션을 참조하세요.
minSeverity | syslog 심각도 수준입니다(예: "LOG\_ERR" 또는 "LOG\_INFO"). 전체 목록은 [syslog 기본 페이지](http://man7.org/linux/man-pages/man3/syslog.3.html)의 "level" 섹션을 참조하세요. 확장은 지정된 수준 이상으로 전송되는 이벤트를 캡처합니다.

`syslogEvents`를 지정할 경우 LAD는 항상 Azure Storage의 테이블에 데이터를 작성합니다. JSON Blob 및/또는 Event Hubs에 동일한 데이터를 작성할 수 있지만 테이블에 데이터를 저장하지 않도록 할 수는 없습니다. 이 테이블에 대한 분할 동작은 `performanceCounters`의 경우와 동일합니다. 테이블 이름은 다음 문자열이 연결된 것입니다.

* `LinuxSyslog`
* 10일마다 변경되는 "YYYYMMDD" 형식의 날짜

예에는 `LinuxSyslog20170410` 및 `LinuxSyslog20170609`가 포함됩니다.

### <a name="perfcfg"></a>perfCfg

이 선택적 섹션은 임의의 [OMI](https://github.com/Microsoft/omi) 쿼리 실행을 제어합니다.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

요소 | 값
------- | -----
namespace | (선택 사항) 실행해야 할 쿼리 내의 OMI 네임스페이스입니다. 지정되지 않은 경우 기본값은 [System Center 플랫폼 간 공급자](https://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation)가 구현한 "root/scx"입니다.
쿼리 | 실행될 OMI 쿼리입니다.
테이블 | (선택 사항) 지정된 스토리지 계정의 Azure Storage 테이블입니다([보호 설정](#protected-settings) 참조).
frequency | (선택 사항) 쿼리 실행 간격(초)입니다. 기본값은 300(5분)이고 최소값은 15초입니다.
sinks | (선택 사항) 원시 샘플 메트릭 결과가 게시되어야 하는 쉼표로 구분된 추가 싱크 이름 목록입니다. 이러한 원시 샘플의 집계는 확장 또는 Azure Metrics에서 계산되지 않습니다.

"table"이나 "sinks" 또는 둘 다 지정해야 합니다.

### <a name="filelogs"></a>fileLogs

로그 파일의 캡처를 제어합니다. LAD는 파일에 작성된 새 텍스트 줄을 캡처하여 테이블 행 및/또는 지정된 싱크(JsonBlob 또는 EventHub)에 기록합니다.

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

요소 | 값
------- | -----
file | 확인 및 캡처할 로그 파일의 전체 경로 이름입니다. 경로 이름은 단일 파일의 이름을 지정해야 합니다. 디렉터리 이름을 지정하거나 와일드카드를 포함할 수 없습니다.
테이블 | (선택 사항) 보호되는 구성에서 지정된 대로, 파일의 “끝"에서 새 줄이 작성되고 지정된 스토리지 계정의 Azure Storage 테이블입니다.
sinks | (선택 사항) 로그 줄이 전송되는 쉼표로 구분된 추가 싱크 이름 목록입니다.

"table"이나 "sinks" 또는 둘 다 지정해야 합니다.

## <a name="metrics-supported-by-the-builtin-provider"></a>기본 제공 공급자가 지원하는 메트릭

기본 제공 메트릭 공급자는 광범위한 사용자에게 가장 흥미로운 메트릭 소스입니다. 이러한 메트릭은 다음과 같은 다섯 가지 광범위한 클래스에 속합니다.

* 프로세서
* 메모리
* 네트워크
* 파일 시스템
* 디스크

### <a name="builtin-metrics-for-the-processor-class"></a>프로세서 클래스의 기본 제공 메트릭

메트릭의 프로세서 클래스는 VM의 프로세서 사용량에 대한 정보를 제공합니다. 백분율을 집계하는 경우 결과는 모든 CPU에서의 평균입니다. 2 vCPU VM에서 하나의 vCPU가 100% 사용 중이고 다른 vCPU가 100% 유휴 상태인 경우 보고되는 PercentIdleTime은 50입니다. 같은 기간 동안 각 vCPU가 50% 사용 중인 경우에도 보고되는 결과는 50입니다. 하나의 vCPU는 100% 사용 중이고 다른 vCPU는 유휴 상태인 4 vCPU VM에서 보고되는 PercentIdleTime은 75입니다.

counter | 의미
------- | -------
PercentIdleTime | 집계 기간 중 프로세서가 커널 유휴 루프를 실행한 시간의 백분율
PercentProcessorTime | 비 유휴 스레드를 실행하는 시간의 백분율
PercentIOWaitTime | IO 작업이 완료될 때까지 기다리는 시간의 백분율
PercentInterruptTime | 하드웨어/소프트웨어 인터럽트 및 DPC(지연된 프로시저 호출)를 실행하는 시간의 백분율
PercentUserTime | 집계 기간 중 비 유휴 시간 동안 일반 우선 순위에서 사용자에게 더 소요된 시간의 백분율
PercentNiceTime | 비 유휴 시간 동안 낮은(좋은) 우선 순위에서 소요된 시간의 백분율
PercentPrivilegedTime | 비 유휴 시간 동안 특권(커널) 모드에서 소요된 시간의 백분율

처음 4개 카운터의 합계가 100%가 되어야 합니다. 마지막 3개 카운터의 합계도 100%이고 PercentProcessorTime, PercentIOWaitTime 및 PercentInterruptTime의 합계도 100%가 되어야 합니다.

모든 프로세서에 대해 집계된 단일 메트릭을 얻으려면 `"condition": "IsAggregate=TRUE"`를 설정합니다. 특정 프로세서(예: 4 vCPU VM의 두 번째 논리 프로세서)에 대한 메트릭을 얻으려면 `"condition": "Name=\\"1\\""`를 설정합니다. 논리 프로세서 수는 `[0..n-1]` 범위에 있습니다.

### <a name="builtin-metrics-for-the-memory-class"></a>메모리 클래스의 기본 제공 메트릭

메트릭의 메모리 클래스는 메모리 사용률, 페이징 및 스와핑에 대한 정보를 제공합니다.

counter | 의미
------- | -------
AvailableMemory | 사용 가능한 실제 메모리(MiB)
PercentAvailableMemory | 총 메모리 중 사용 가능한 실제 메모리의 백분율
UsedMemory | 사용 중인 실제 메모리(MiB)
PercentUsedMemory | 총 메모리 중 사용 중인 실제 메모리의 백분율
PagesPerSec | 총 페이징(읽기/쓰기)
PagesReadPerSec | 백업 저장소(스왑 파일, 프로그램 파일, 매핑된 파일 등)에서 읽은 페이지
PagesWrittenPerSec | 백업 저장소(스왑 파일, 매핑된 파일 등)에 기록된 페이지
AvailableSwap | 사용하지 않는 스왑 공간(MiB)
PercentAvailableSwap | 총 스왑 중 사용하지 않은 스왑 공간의 백분율
UsedSwap | 사용 중인 스왑 공간(MiB)
PercentUsedSwap | 총 스왑 중 사용 중인 스왑 공간의 백분율

이 메트릭 클래스에는 인스턴스가 하나만 있습니다. "condition" 특성에는 유용한 설정이 없으며 생략해야 합니다.

### <a name="builtin-metrics-for-the-network-class"></a>네트워크 클래스의 기본 제공 메트릭

메트릭의 네트워크 클래스는 부팅 이후 개별 네트워크 인터페이스에서 네트워크 작업에 대한 정보를 제공합니다. LAD는 대역폭 메트릭을 노출하지 않으며 이는 호스트 메트릭에서 검색할 수 있습니다.

counter | 의미
------- | -------
BytesTransmitted | 부팅 이후 보낸 총 바이트
BytesReceived | 부팅 이후 받은 총 바이트
BytesTotal | 부팅 이후 보내거나 받은 총 바이트
PacketsTransmitted | 부팅 이후 보낸 총 패킷
PacketsReceived | 부팅 이후 받은 총 패킷
TotalRxErrors | 부팅 이후 수신 오류 수
TotalTxErrors | 부팅 이후 전송 오류 수
TotalCollisions | 부팅 이후 네트워크 포트에서 보고되는 충돌 수

 이 클래스가 인스턴스화되더라도 LAD는 모든 네트워크 디바이스에서 집계되는 네트워크 메트릭을 캡처하지 않습니다. 특정 인터페이스(예: eth0)에 대한 메트릭을 가져오려면 `"condition": "InstanceID=\\"eth0\\""`를 설정합니다.

### <a name="builtin-metrics-for-the-filesystem-class"></a>파일 시스템 클래스의 기본 제공 메트릭

메트릭의 파일 시스템 클래스는 파일 시스템 사용량에 대한 정보를 제공합니다. 절대값과 백분율 값은 루트가 아닌 일반 사용자에게 표시되는 것으로 보고됩니다.

counter | 의미
------- | -------
FreeSpace | 사용 가능한 디스크 공간(바이트)
UsedSpace | 사용된 디스크 공간(바이트)
PercentFreeSpace | 사용 가능한 공간의 백분율
PercentUsedSpace | 사용된 공간의 백분율
PercentFreeInodes | 사용하지 않은 inode의 백분율
PercentUsedInodes | 모든 파일 시스템에서 합한 할당된(사용 중인) inode의 백분율
BytesReadPerSecond | 초당 읽은 바이트
BytesWrittenPerSecond | 초당 쓴 바이트
초당 바이트 수 | 초당 읽거나 쓴 바이트
ReadsPerSecond | 초당 읽기 작업
WritesPerSecond | 초당 쓰기 작업
TransfersPerSecond | 초당 읽기 또는 쓰기 작업

`"condition": "IsAggregate=True"`로 설정하면 모든 파일 시스템에서 집계된 값을 얻을 수 있습니다. `"condition": 'Name="/mnt"'`로 설정하면 특정 탑재된 파일 시스템(예: "/mnt")에 대한 값을 얻을 수 있습니다.

### <a name="builtin-metrics-for-the-disk-class"></a>디스크 클래스의 기본 제공 메트릭

메트릭의 디스크 클래스는 디스크 디바이스 사용량에 대한 정보를 제공합니다. 이러한 통계는 전체 드라이브에 적용됩니다. 디바이스에 여러 파일 시스템이 있는 경우 해당 디바이스의 카운터가 전체 시스템에 걸쳐 효과적으로 집계됩니다.

counter | 의미
------- | -------
ReadsPerSecond | 초당 읽기 작업
WritesPerSecond | 초당 쓰기 작업
TransfersPerSecond | 초당 총 작업
AverageReadTime | 읽기 작업당 평균 시간(초)
AverageWriteTime | 쓰기 작업당 평균 시간(초)
AverageTransferTime | 작업당 평균 시간(초)
AverageDiskQueueLength | 대기 중인 디스크 작업의 평균 수
ReadBytesPerSecond | 초당 읽은 바이트 수
WriteBytesPerSecond | 초당 쓴 바이트 수
초당 바이트 수 | 초당 읽거나 쓴 바이트 수

`"condition": "IsAggregate=True"`로 설정하면 모든 디스크에서 집계된 값을 얻을 수 있습니다. 특정 디바이스(예: /dev/sdf1)에 대한 정보를 얻으려면 `"condition": "Name=\\"/dev/sdf1\\""`를 설정합니다.

## <a name="installing-and-configuring-lad-30-via-cli"></a>CLI를 통해 LAD 3.0 설치 및 구성

PrivateConfig.json 파일에 보호 설정이 있고 PublicConfig.json에 공용 구성 정보가 있다고 가정하고 다음 명령을 실행합니다.

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

명령은 Azure CLI의 Azure 리소스 관리 모드(arm)를 사용한다고 가정합니다. 클래식 배포 모델(ASM) VM에 대한 LAD를 구성하려면 "asm" 모드(`azure config mode asm`)로 전환하고 명령에서 리소스 그룹 이름을 생략합니다. 자세한 내용은 [플랫폼 간 CLI 설명서](https://docs.microsoft.com/azure/xplat-cli-connect)를 참조하세요.

## <a name="an-example-lad-30-configuration"></a>LAD 3.0 구성 예제

이전 정의를 기반으로 몇 가지 설명이 포함된 샘플 LAD 3.0 확장 구성이 나와 있습니다. 이 샘플을 사례에 적용하려면 사용자 고유의 저장소 계정 이름, 계정 SAS 토큰 및 EventHubs SAS 토큰을 사용해야 합니다.

### <a name="privateconfigjson"></a>PrivateConfig.json

개인 설정은 다음을 구성합니다.

* 저장소 계정
* 일치하는 계정 SAS 토큰
* 여러 싱크(SAS 토큰이 있는 EventHubs 또는 JsonBlob)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="publicconfigjson"></a>PublicConfig.json

공용 설정으로 LAD는 다음을 수행합니다.

* 프로세서 시간 비율 및 사용된 디스크 공간 메트릭을 `WADMetrics*` 테이블에 업로드
* syslog 기능 "사용자" 및 심각도 "정보"의 메시지를 `LinuxSyslog*` 테이블에 업로드
* 원시 OMI 쿼리 결과(PercentProcessorTime 및 PercentIdleTime)를 `LinuxCPU`라는 테이블에 업로드
* `/var/log/myladtestlog` 파일에서 추가된 줄을 `MyLadTestLog` 테이블에 업로드

각각의 경우 데이터는 다음으로 업로드됩니다.

* Azure Blob Storage(JsonBlob 싱크에 컨테이너 이름이 정의된 경우)
* EventHubs 엔드포인트(EventHubs 싱크에 지정된 경우)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "sampleRateInSeconds": 15,
  "ladCfg": {
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

구성에서 `resourceId`는 VM 또는 가상 머신 확장 집합의 resourceId와 일치해야 합니다.

* Azure 플랫폼 메트릭 차트 및 경고에서는 작업 중인 VM의 resourceId를 알고 있습니다. 조회 키 resourceId를 사용하여 VM에 대한 데이터를 찾을 수 있을 것으로 예상합니다.
* Azure 자동 크기 조정을 사용하는 경우 자동 크기 조정 구성에서 resourceId는 LAD에서 사용하는 resourceId와 일치해야 합니다.
* resourceId는 LAD에서 작성한 JsonBlob의 이름으로 만들어집니다.

## <a name="view-your-data"></a>데이터 보기

Azure Portal을 사용하여 성능 데이터를 보거나 경고를 설정합니다.

![이미지](./media/diagnostics-linux/graph_metrics.png)

`performanceCounters` 데이터는 항상 Azure Storage 테이블에 저장됩니다. Azure Storage API는 다양한 언어 및 플랫폼에 사용할 수 있습니다.

JsonBlob 싱크로 전송된 데이터는 [보호 설정](#protected-settings)에서 명명된 저장소 계정의 Blob에 저장됩니다. Azure Blob Storage API를 사용하여 Blob 데이터를 사용할 수 있습니다.

또한 다음 UI 도구를 사용하여 Azure Storage의 데이터에 액세스할 수 있습니다.

* Visual Studio 서버 탐색기.
* [Microsoft Azure Storage 탐색기](https://azurestorageexplorer.codeplex.com/ "Azure Storage 탐색기").

Microsoft Azure Storage 탐색기의 이 스냅숏 세션은 테스트 VM에서 올바르게 구성된 LAD 3.0 확장에서 생성된 Azure Storage 테이블 및 컨테이너를 보여 줍니다. 이미지가 [샘플 LAD 3.0 구성](#an-example-lad-30-configuration)과 정확히 일치하지는 않습니다.

![이미지](./media/diagnostics-linux/stg_explorer.png)

EventHubs 엔드포인트에 게시된 메시지를 사용하는 방법에 대해 알아보려면 관련 [EventHubs 설명서](../../event-hubs/event-hubs-what-is-event-hubs.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md)에 수집하는 메트릭에 대한 메트릭 경고를 만듭니다.
* 메트릭에 대한 [모니터링 차트](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)를 만듭니다.
* 메트릭을 사용해 [가상 머신 확장 집합 만들기](../linux/tutorial-create-vmss.md)를 수행하여 자동 크기 조정을 제어하는 방법에 대해 알아봅니다.
