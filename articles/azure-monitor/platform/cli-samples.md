---
title: Azure Monitor CLI 빠른 시작 샘플
description: Azure Monitor 기능에 대한 샘플 CLI 명령입니다. Azure Monitor는 Cloud Services, Virtual Machines 및 Web Apps의 크기를 자동으로 조정하고, 구성된 원격 분석 데이터 값을 기반으로 경고 알림을 보내거나 웹 URL을 호출할 수 있는 Microsoft Azure 서비스입니다.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: robb
ms.component: ''
ms.openlocfilehash: fa3293346fee6f6666db01dab5587dd760df84b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740886"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Azure Monitor CLI 빠른 시작 샘플
이 문서에서는 Azure Monitor 기능에 액세스하는 데 유용한 샘플 CLI(명령줄 인터페이스) 명령을 보여 줍니다. Azure Monitor를 통해 Cloud Services, Virtual Machines 및 Web Apps의 크기를 자동으로 조정하고, 구성된 원격 분석 데이터의 값을 기반으로 경고 알림을 보내거나 웹 URL을 호출할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

아직 Azure CLI를 설치하지 않은 경우 [Azure CLI 설치](/cli/azure/install-azure-cli) 지침을 따릅니다. 또한 [Azure Cloud Shell](/azure/cloud-shell)을 사용하여 브라우저에서 CLI를 대화형 환경으로 실행할 수 있습니다. [Azure Monitor CLI 참조](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)에서 사용 가능한 모든 명령의 전체 참조를 확인하세요. 

## <a name="log-in-to-azure"></a>Azure에 로그인
첫 번째 단계에서는 Azure 계정에 로그인합니다.

```azurecli
az login
```

이 명령을 실행한 후 화면의 지시에 따라 로그인해야 합니다. 모든 명령은 기본 구독의 컨텍스트에서 작동합니다.

현재 구독에 대한 세부 정보를 나열하려면 다음 명령을 사용합니다.

```azurecli
az account show
```

작업 중인 컨텍스트를 다른 구독으로 변경하려면 다음 명령을 사용합니다.

```azurecli
az account set -s <Subscription ID or name>
```

지원되는 모든 Azure Monitor 명령 목록을 보려면 다음을 수행합니다.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>구독에 대한 활동 로그 보기

활동 로그 이벤트 목록을 보려면 다음을 수행합니다.

```azurecli
az monitor activity-log list
```

사용 가능한 모든 옵션을 보려면 다음을 수행합니다.

```azurecli
az monitor activity-log list -h
```

다음은 resourceGroup별 로그를 나열하는 예제입니다.

```azurecli
az monitor activity-log list --resource-group <group name>
```

호출자별 로그를 나열하는 예제

```azurecli
az monitor activity-log list --caller myname@company.com
```

날짜 범위 내 리소스 유형에 대한 호출자별 로그를 나열하는 예제

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>경고 작업 
> [!NOTE]
> 이번에 CLI에서 경고(클래식)만이 지원됩니다. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>리소스 그룹의 경고(클래식) 규칙 가져오기

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>메트릭 경고(클래식) 규칙 만들기

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>경고(클래식) 규칙 삭제

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>로그 프로필

이 섹션의 정보를 사용하여 로그 프로필 작업을 수행할 수 있습니다.

### <a name="get-a-log-profile"></a>로그 프로필 가져오기

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>보존하는 로그 프로필 추가

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>보존하는 로그 프로필 및 이벤트 허브 추가

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>로그 프로필 제거

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>진단

이 섹션의 정보를 사용하여 진단 설정 작업을 수행할 수 있습니다.

### <a name="get-a-diagnostic-setting"></a>진단 설정 가져오기

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>진단 로그 설정 만들기 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>진단 설정 삭제

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>자동 크기 조정

이 섹션의 정보를 사용하여 자동 크기 조정 설정 작업을 수행할 수 있습니다. 이러한 예제를 수정해야 합니다.

### <a name="get-autoscale-settings-for-a-resource-group"></a>리소스 그룹에 대한 자동 크기 조정 설정 가져오기

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>리소스 그룹의 이름으로 자동 크기 조정 설정 가져오기

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>자동 크기 조정 설정 지정

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
