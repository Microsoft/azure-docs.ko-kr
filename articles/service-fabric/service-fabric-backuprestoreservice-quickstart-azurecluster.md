---
title: Azure Service Fabric에서 정기적인 백업 및 복원 | Microsoft Docs
description: Service Fabric의 주기적 백업 및 복원 기능을 사용하여 애플리케이션 데이터의 주기적인 데이터 백업을 사용하도록 설정합니다.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAA58600-897E-4CEE-9D1C-93FACF98AD1C
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: hrushib
ms.openlocfilehash: 4d4bc69f00f86bc81c353ef0cc40f37f000ba6c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472204"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric"></a>Azure Service Fabric에서 정기적인 백업 및 복원 
> [!div class="op_single_selector"]
> * [Azure의 클러스터](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [독립 실행형 클러스터](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric은 안정성이 뛰어나고 분산된 마이크로 서비스 기반 클라우드 애플리케이션을 손쉽게 개발 및 관리할 수 있도록 해주는 분산된 시스템 플랫폼입니다. 상태 비저장 및 상태 저장 마이크로 서비스를 모두 실행할 수 있습니다. 상태 저장 서비스는 요청 및 응답 또는 완전한 트랜잭션을 넘어서서 변경 가능하고 신뢰할 수 있는 상태를 유지할 수 있습니다. 상태 저장 서비스가 오랜 시간 동안 중단되거나 재해로 인해 정보가 손실되는 경우 백업된 후에 서비스를 계속 제공하기 위해 상태의 최신 백업으로 복원해야 할 수 있습니다.

Service Fabric은 여러 노드에 걸쳐 상태를 복제하여 서비스의 가용성을 높입니다. 클러스터의 한 노드에서 오류가 발생해도 서비스를 지속적으로 사용할 수 있습니다. 그러나 어떤 경우에는 서비스 데이터가 광범위한 오류에 대해 신뢰할 수 있는 것이 여전히 바람직합니다.
 
예를 들어 서비스에서 다음과 같은 시나리오로부터 보호하기 위해 해당 데이터를 백업하려고 할 수 있습니다.
- Service Fabric 클러스터 전체가 영구적으로 손실되는 경우
- 서비스 파티션의 복제본 대다수가 영구적으로 손실되는 경우
- 상태가 우발적으로 삭제되거나 손상된 관리 오류. 예를 들어 충분한 권한이 있는 관리자가 실수로 서비스를 삭제합니다.
- 데이터 손상을 초래하는 서비스 내 버그. 예를 들어 신뢰할 수 있는 컬렉션에 잘못된 데이터 작성을 시작하는 서비스 코드가 업그레이드되는 경우 발생할 수 있습니다. 이런 경우 코드와 데이터 모두 이전 상태로 되돌아가야 할 수도 있습니다.
- 오프라인 데이터 처리. 데이터를 생성하는 서비스와는 별도로 비즈니스 인텔리전스를 위한 데이터를 오프라인 처리하는 것이 편리할 수 있습니다.

Service Fabric은 특정 시점 [백업 및 복원](service-fabric-reliable-services-backup-restore.md)을 수행하는 기본 제공 API를 제공합니다. 애플리케이션 개발자는 이러한 API를 사용하여 서비스 상태를 정기적으로 백업할 수 있습니다. 또한 서비스 관리자가 애플리케이션을 업그레이드하기 전과 같이 특정 시간에 서비스 외부에서 백업을 트리거하려는 경우 개발자는 서비스에서 API로 백업(및 복원)을 공개해야 합니다. 백업을 유지 관리하는 것은 이 밖에도 추가 비용이 듭니다. 예를 들어 30분마다 5번의 증분 백업을 수행하고 전체 백업을 수행하려고 할 수 있습니다. 전체 백업 후에는 이전 증분 백업을 삭제할 수 있습니다. 이 방법은 추가 코드를 필요로 하므로 애플리케이션 개발 중에 추가 비용이 발생합니다.

Service Fabric의 서비스 백업 및 복원에서는 상태 저장 서비스에 저장된 정보의 손쉬운 자동화 백업을 사용하도록 설정합니다. 애플리케이션 데이터를 정기적으로 백업하는 것은 데이터 손실 및 서비스 비가용성을 방지하는 데 필수적입니다. Service Fabric은 백업 및 복원 서비스(선택 사항)를 제공하므로 추가 코드를 작성할 필요 없이 상태 저장 Reliable Services(Actor Services 포함)의 정기적인 백업을 구성할 수 있습니다. 또한 이전에 수행한 백업 복원을 수월하게 수행할 수 있습니다. 


Service Fabric에서는 정기적 백업 및 복원 기능과 관련된 다음 기능을 가능하게 해주는 API 집합을 제공합니다.

- (외부) 저장소 위치에 백업을 업로드할 수 있도록 지원하여 Reliable Stateful 서비스 및 Reliable Actors의 정기적 백업을 예약합니다. 지원되는 저장소 위치
    - Azure Storage
    - 파일 공유(온-프레미스)
- 백업 열거
- 파티션의 임시 백업 트리거
- 이전 백업을 사용하여 파티션 복원
- 일시적으로 백업 일시 중단
- 백업의 보존 관리(예정)

## <a name="prerequisites"></a>필수 조건
* Fabric 버전 6.2 이상을 포함하는 Service Fabric 클러스터. 클러스터는 Windows 서버에 설치해야 합니다. Azure 리소스 템플릿을 사용하여 Service Fabric 클러스터를 만드는 단계는 이 [문서](service-fabric-cluster-creation-via-arm.md)를 참조하세요.
* 백업을 저장하기 위해 저장소에 연결하는 데 필요한 비밀 암호화를 위한 X.509 인증서. X.509 인증서를 가져오거나 만드는 방법에 대해 알아보려면 [문서](service-fabric-cluster-creation-via-arm.md)를 참조하세요.
* Service Fabric SDK 버전 3.0 이상을 사용하여 빌드된 Service Fabric Reliable Stateful 애플리케이션. .NET Core 2.0을 대상으로 응용 프로그램에 대 한 응용 프로그램 사용 하 여 빌드되어야 Service Fabric SDK 버전 3.1 이상.
* 애플리케이션 백업을 저장하기 위해 Azure Storage 계정을 만듭니다.

## <a name="enabling-backup-and-restore-service"></a>Backup 및 Restore 서비스 사용
먼저 클러스터에서 _Backup 및 Restore 서비스_를 사용하도록 설정해야 합니다. 배포하려는 클러스터에 대한 템플릿을 가져옵니다. [샘플 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)을 사용하거나 Resource Manager 템플릿을 만들 수 있습니다. 다음 단계에 따라 _Backup 및 Restore 서비스_를 사용하도록 설정합니다.

1. 먼저 다음 코드 조각과 같이 `Microsoft.ServiceFabric/clusters` 리소스에 대해 `apiversion`이 **`2018-02-01`** 로 설정되었는지 확인하고 이렇게 설정되어 있지 않으면 업데이트합니다.

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 이제 다음 코드 조각과 같이 다음 `addonFeatures` 섹션을 `properties` 섹션 아래에 추가하여 _Backup 및 Restore 서비스_를 사용하도록 설정합니다. 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. 자격 증명의 암호화를 위해 X.509 인증서를 구성합니다. 저장소에 연결하기 위해 제공된 자격 증명을 저장하기 전에 암호화되도록 하는 것이 중요합니다. 다음 코드 조각과 같이 다음 `BackupRestoreService` 섹션을 `fabricSettings` 섹션 아래에 추가하여 암호화 인증서를 구성합니다. 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. 위와 같은 변경 내용으로 클러스터 템플릿을 업데이트한 후 변경 내용을 적용하고 배포/업그레이드가 완료되도록 합니다. 완료된 후에는 클러스터에서 _Backup 및 Restore 서비스_ 실행이 시작됩니다. 이 서비스의 URI는 `fabric:/System/BackupRestoreService`이고 서비스는 Service Fabric Explorer의 시스템 서비스 섹션 아래에 있을 수 있습니다. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Reliable Stateful 서비스 및 Reliable Actors에 대해 정기적 백업 사용
Reliable Stateful 서비스 및 Reliable Actors에 대한 정기적 백업을 사용하도록 설정하는 단계를 살펴보겠습니다. 이러한 단계에서는 다음을 가정합니다.
- 클러스터는 _Backup 및 Restore 서비스_와 함께 X.509 보안을 사용하여 설치됩니다.
- Reliable Stateful 서비스가 클러스터에 배포됩니다. 이 빠른 시작 가이드의 목적에 맞게 애플리케이션 URI는 `fabric:/SampleApp`이며 이 애플리케이션에 속한 Reliable Stateful 서비스의 URI는 `fabric:/SampleApp/MyStatefulService`입니다. 이 서비스는 단일 파티션과 함께 배포되고 파티션 ID는 `974bd92a-b395-4631-8a7f-53bd4ae9cf22`입니다.
- 관리자 역할이 있는 클라이언트 인증서는 아래 스크립트가 호출되는 컴퓨터의 _CurrentUser_ 인증서 저장소 위치의 _내_(_개인용_) 저장소 이름에 설치됩니다. 이 예에서는 `1b7ebe2174649c45474a4819dafae956712c31d3`를 인증서 지문으로 사용합니다. 클라이언트 인증서에 대한 자세한 내용은 [Service Fabric 클라이언트의 역할 기반 액세스 제어](service-fabric-cluster-security-roles.md)를 참조하세요.

### <a name="create-backup-policy"></a>백업 정책 만들기

첫 번째 단계는 백업 일정, 백업 데이터의 대상 스토리지, 정책 이름, 전체 백업을 트리거하기 전에 허용할 최대 증분 백업 및 백업 스토리지를 위한 보존 정책을 설명하는 백업 정책을 만드는 것입니다. 

백업 스토리지의 경우 위에서 만든 Azure Storage 계정을 사용합니다. `backup-container` 컨테이너는 백업을 저장하기 위해 구성됩니다. 백업 업로드 중이 이 이름의 컨테이너가 만들어집니다(아직 없는 경우). Azure Storage 계정에 유효한 연결 문자열로 `ConnectionString`을 채우고 `account-name`을 스토리지 계정 이름으로 바꾸며 `account-key`를 스토리지 계정 키로 바꿉니다.

필요한 REST API를 호출하여 새 정책을 만들려면 다음 PowerShell 스크립트를 실행합니다. `account-name`을 저장소 계정의 이름으로 바꾸고 `account-key`를 저장소 계정 키로 바꿉니다.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

> [!IMPORTANT]
> 런타임의 문제로 인해 보존 정책의 보존 기간을 24일 이내로 구성해야 합니다. 그렇지 않으면 백업 복원 서비스가 복제본 장애 조치(Failover) 후 쿼럼 손실 상태가 될 수 있습니다.

### <a name="enable-periodic-backup"></a>정기적 백업 사용
애플리케이션의 데이터 보호 요구 사항을 충족하도록 백업 정책을 정의한 후 백업 정책을 애플리케이션과 연결해야 합니다. 요구 사항에 따라 백업 정책을 애플리케이션, 서비스 또는 파티션과 연결할 수 있습니다.

위의 단계에서 만든 이름이 `BackupPolicy1`인 백업 정책을 `SampleApp` 애플리케이션과 연결하기 위해 필요한 REST API를 호출하기 위해 다음 PowerShell 스크립트를 실행합니다.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>정기적 백업이 작동하는지 확인

애플리케이션 수준에서 백업을 사용하도록 설정하면 애플리케이션의 Reliable Stateful 서비스 및 Reliable Actors에 속한 모든 파티션이 관련 백업 정책에 따라 정기적으로 백업되기 시작합니다. 

![파티션 BackedUp 상태 이벤트][0]

### <a name="list-backups"></a>백업 목록

애플리케이션의 Reliable Stateful 서비스 및 Reliable Actors에 속한 모든 파티션과 관련된 백업은 _GetBackups_ API를 사용하여 열거할 수 있습니다. 백업은 애플리케이션, 서비스 또는 파티션에 대해 열거할 수 있습니다.

HTTP API를 호출하는 다음 PowerShell 스크립트를 실행하여 `SampleApp` 애플리케이션 내의 모든 파티션에 대해 생성된 백업을 열거합니다.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
위의 실행에 대한 샘플 출력:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 

BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 

BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            : 
```

## <a name="limitation-caveats"></a>제한/주의 사항
- PowerShell cmdlet에 기본 제공 Service Fabric이 없습니다.
- Linux에서 Service Fabric 클러스터에 대한 지원이 없습니다.

## <a name="known-issues"></a>알려진 문제
- 보존 기간을 24일 이내로 구성했는지 확인합니다. 

## <a name="next-steps"></a>다음 단계
- [정기 백업 구성 이해](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [백업 복원 REST API 참조](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png

