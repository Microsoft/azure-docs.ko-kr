---
title: Service Fabric Backup 및 복원 | Microsoft Docs
description: 서비스 패브릭 Backup 및 복원에 관한 개념 설명서
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: chackdan
editor: subramar,zhol
ms.assetid: 91ea6ca4-cc2a-4155-9823-dcbd0b996349
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: cd40f59cfa7846911c68206c3bc1e85a770b0fcc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723877"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Reliable Services 및 Reliable Actors 백업 및 복원
Azure 서비스 패브릭은 여러 노드에 걸쳐 상태를 복제하여 고가용성을 유지하는 고가용성 플랫폼입니다.  따라서 클러스터의 한 노드에서 오류가 발생해도 서비스를 지속적으로 사용할 수 있습니다. 많은 경우 플랫폼에서 제공하는 이러한 기본 제공 중복성으로 충분하지만 어떤 경우에는 서비스를 위해 (외부 저장소에) 데이터를 백업하는 것이 바람직합니다.

> [!NOTE]
> 데이터 손실 시나리오에서 복구할 수 있도록 데이터를 백업 및 복원(및 예상대로 작동하는지 테스트)하는 것이 중요합니다.
> 

> [!NOTE]
> Reliable Stateful 서비스 및 Reliable Actors의 데이터 백업 구성에는 [정기적인 백업 및 복원](service-fabric-backuprestoreservice-quickstart-azurecluster.md)을 사용하는 것이 좋습니다. 
> 


예를 들어 서비스에서 다음과 같은 시나리오로부터 보호하기 위해 데이터를 백업하려고 할 수 있습니다.

- Service Fabric 클러스터 전체가 영구적으로 손실되는 경우
- 서비스 파티션의 복제본 대다수가 영구적으로 손실되는 경우
- 상태가 우발적으로 삭제되거나 손상된 관리 오류. 예를 들어 충분한 권한이 있는 관리자가 실수로 서비스를 삭제한 경우에 발생합니다.
- 데이터 손상을 초래하는 서비스 내 버그. 예를 들어 신뢰할 수 있는 컬렉션에 잘못된 데이터 작성을 시작하는 서비스 코드가 업그레이드되는 경우 발생할 수 있습니다. 이런 경우 코드와 데이터 모두 이전 상태로 되돌아가야 할 수도 있습니다.
- 오프라인 데이터 처리. 데이터를 생성하는 서비스와는 별도로 비즈니스 인텔리전스를 위한 데이터를 오프라인 처리하는 것이 편리할 수 있습니다.

Backup/복원 기능을 사용하면 Reliable Services API에 구축된 서비스로 백업을 만들고 복원할 수 있습니다. 플랫폼에서 제공하는 백업 API를 사용하면 읽기 또는 쓰기 작업을 차단하지 않고 서비스 파티션 상태를 백업할 수 있습니다. 복원 API를 사용하면 선택한 백업에서 서비스 파티션의 상태를 복원할 수 있습니다.

## <a name="types-of-backup"></a>Backup 유형
백업 옵션은 두 가지입니다. 전체 및 증분.
전체 백업은 검사점 및 모든 로그 레코드처럼 복제본의 상태를 다시 만드는 데 필요한 모든 데이터를 포함하는 백업입니다.
전체 백업은 검사점 및 로그를 포함하므로 자체적으로 복원할 수 있습니다.

전체 백업에서는 검사점이 큰 경우에 문제가 발생합니다.
예를 들어 16GB의 상태가 있는 복제본은 약 16GB까지 추가되는 검사점을 포함하게 됩니다.
복구 지점 목표가 5분인 경우 복제본을 5분마다 백업해야 합니다.
백업할 때마다 50MB(`CheckpointThresholdInMB`를 사용하여 구성 가능)의 로그 외에도 16GB의 검사점을 복사해야 합니다.

![전체 Backup 예입니다.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

이 문제에 대한 해결 방법은 증분 백업입니다. 이 경우 백업에는 마지막 백업 이후에 변경된 로그 레코드만 포함됩니다.

![증분 Backup 예입니다.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

증분 백업은 마지막 백업 이후 변경 사항만 백업(검사점은 포함하지 않음)하므로 속도는 더 빠르지만 자체적으로 복원할 수 없습니다.
증분 백업을 복원하려면 전체 백업 체인이 필요합니다.
백업 체인은 전체 백업으로 시작하여 이후 수많은 지속적인 증분 백업이 이어지는 백업의 체인입니다.

## <a name="backup-reliable-services"></a>Reliable Services 백업
서비스 작성자에는 백업 수행 시기와 백업 저장 위치에 대한 모든 권한이 있습니다.

백업을 시작하려면 서비스에서 상속된 `BackupAsync` 멤버 함수를 호출해야 합니다.  
Backup은 주 복제본에서만 수행되며 상태 쓰기 권한을 부여해야 합니다.

아래와 같이 `BackupAsync`는 `BackupDescription` 개체를 가져오며, 여기서는 `Func<< BackupInfo, CancellationToken, Task<bool>>>` 콜백 함수뿐만 아니라 전체 또는 증분 백업도 지정할 수 있습니다. 이 함수는 백업 폴더를 로컬로 만들 때 호출되고 일부 외부 저장소로 이동할 준비가 되어 있습니다.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

증분 백업을 수행하도록 요구하는 요청은 `FabricMissingFullBackupException`으로 인해 실패할 수 있습니다. 이 예외는 다음 중 하나가 발생했음을 나타냅니다.

- 이는 복제본이 전체 백업을 아예 가져올 수 없거나
- 마지막 백업 이후 일부 로그 레코드가 잘렸거나
- 복제본이 `MaxAccumulatedBackupLogSizeInMB` 제한을 초과했습니다.

사용자는 `MinLogSizeInMB` 또는 `TruncationThresholdFactor`를 구성하여 증분 백업을 수행할 수 있는 가능성을 높일 수 있습니다.
이러한 값을 늘리면 복제본당 디스크 사용량이 증가합니다.
자세한 내용은 [Reliable Services 구성](service-fabric-reliable-services-configuration.md)을 참조하세요.

`BackupInfo`는 런타임에서 백업을 저장한 폴더의 위치(`BackupInfo.Directory`)를 포함하여 백업과 관련된 정보를 제공합니다. 콜백 함수는 `BackupInfo.Directory`를 외부 저장소 또는 다른 위치로 이동할 수 있습니다.  이 함수는 백업 폴더를 대상 위치로 이동할 수 있는지 여부를 표시하는 부울 값도 반환합니다.

다음 코드에서는 `BackupCallbackAsync` 메서드를 사용하여 Azure Storage에 백업을 업로드하는 방법을 보여 줍니다.

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

위의 예제에서 `ExternalBackupStore`는 Azure Blob Storage와의 인터페이스에 사용되는 샘플 클래스이고, `UploadBackupFolderAsync`는 폴더를 압축하여 Azure Blob Storage에 배치하는 메서드입니다.

다음 사항에 유의하세요.

  - 특정 시점에서 처리 중인 복제본 당 하나의 백업 작업만이 존재할 수 있습니다. 한 번에 둘 이상의 `BackupAsync` 호출은 처리 중인 백업을 하나로 제한하기 위해 `FabricBackupInProgressException`이 발생됩니다(throw).
  - 백업 진행 중에 복제본 장애 조치가 발생하면 백업이 완료되지 않을 수 있습니다. 따라서 장애 조치가 완료되면 서비스에서 필요에 따라 `BackupAsync`를 호출하여 백업을 다시 시작해야 합니다.

## <a name="restore-reliable-services"></a>Reliable Services 복원
일반적으로 복원 작업을 수행해야 하는 경우는 다음 범주 중 하나에 속합니다.

  - 서비스 파티션에서 데이터가 손실되었습니다. 예를 들어, 파티션에 대한 세 복제본 중 두 복제본(주 복제본 포함)에 대한 디스크가 손상되거나 초기화되었습니다. 새 주 복제본이 백업에서 데이터를 복원해야 할 수도 있습니다.
  - 전체 서비스가 손실되었습니다. 예를 들어, 관리자가 전체 서비스를 제거하여 서비스와 데이터를 복원해야 합니다.
  - 서비스가 손상된 애플리케이션 데이터를 복제했습니다(예: 애플리케이션 버그 때문에). 이 경우 손상 원인을 제거하기 위해 서비스를 업그레이드하거나 되돌려야 하며 손상되지 않은 데이터를 복원해야 합니다.

다양한 방법이 가능하지만, 위의 시나리오에서 `RestoreAsync`를 사용하여 복구하는 몇 가지 예제를 제공하겠습니다.

## <a name="partition-data-loss-in-reliable-services"></a>Reliable Services의 파티션 데이터 손실
이 경우 런타임에서 자동으로 데이터 손실을 검색하고 `OnDataLossAsync` API를 호출합니다.

서비스 작성자는 복구하려면 다음을 수행해야 합니다.

  - `OnDataLossAsync` 가상 기본 클래스 메서드를 재정의합니다.
  - 서비스의 백업을 포함하는 외부 위치에서 최신 백업을 찾습니다.
  - 최신 백업을 다운로드(및 압축된 경우 백업에서 백업 폴더에 압축 해제)합니다.
  - `OnDataLossAsync` 메서드에서 `RestoreContext`를 제공합니다. 제공된 `RestoreContext`에서 `RestoreAsync` API를 호출합니다.
  - 복원이 성공하면 true를 반환합니다.

다음은 `OnDataLossAsync` 메서드의 구현 예제입니다.

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreContext.RestoreAsync` 호출에 전달된 `RestoreDescription`에는 `BackupFolderPath`라고 하는 멤버가 포함되어 있습니다.
단일 전체 백업을 복원하는 경우 이 `BackupFolderPath`는 전체 백업이 포함되어 있는 폴더의 로컬 경로로 설정해야 합니다.
전체 백업과 여러 개의 증분 백업을 복원하는 경우 `BackupFolderPath`는 전체 백업뿐만 아니라 모든 증분 백업도 포함되어 있는 폴더의 로컬 경로로 설정해야 합니다.
제공된 `BackupFolderPath`에 전체 백업이 포함되지 않은 경우 `RestoreAsync` 호출에서 `FabricMissingFullBackupException`이 발생(throw)될 수 있습니다.
`BackupFolderPath`에서 증분 백업 체인이 끊겨 있으면 `ArgumentException`이 발생(throw)될 수도 있습니다.
예를 들어, 전체 백업을 포함하는 경우 두 번째 증분 백업을 제외하고, 첫 번째 증분과 세 번째 증분 백업을 포함하는 경우입니다.

> [!NOTE]
> RestorePolicy는 기본적으로 Safe로 설정됩니다.  즉, 백업 폴더에 이 복제본에 포함된 상태와 같거나 그 이전의 상태가 포함되어 있음을 검색한 경우 `RestoreAsync` API가 ArgumentException으로 인해 실패합니다.  `RestorePolicy.Force`를 사용하여 이 안전 검사를 건너뛸 수 있습니다. 이는 `RestoreDescription`의 일부로 지정됩니다.
> 

## <a name="deleted-or-lost-service"></a>서비스 삭제 또는 손상
서비스가 제거된 경우 데이터 복원에 앞서 서비스를 다시 만들어야 합니다.  데이터의 원활한 복원을 위해 파티션 구성표 등과 같은 동일한 구성의 서비스를 만드는 것이 좋습니다.  서비스가 작동되면 이 서비스의 모든 파티션에서 데이터를 복원하는 API(위의`OnDataLossAsync`)를 호출해야 합니다. 이를 수행하기 위한 한 가지 방법은 모든 파티션에서 [FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx)를 사용하는 것입니다.  

이 시점부터는 앞의 시나리오와 같은 방식으로 구현됩니다. 각 파티션이 외부 저장소로부터 최신 관련 백업을 복원해야 합니다. 한 가지 주의할 점은 런타임이 동적으로 파티션 ID를 생성하기 대문에 파티션 ID가 변경될 수 있다는 사실입니다. 따라서 서비스가 각 파티션에서 복원할 정확한 최신 백업을 식별할 수 있게 적합한 파티션 정보 및 서비스 이름을 저장해야 합니다.

> [!NOTE]
> 전체 서비스를 복원하기 위해 각 파티션에서 `FabricClient.ServiceManager.InvokeDataLossAsync`를 사용하는 것은 클러스터 상태를 손상시킬 수 있으므로 권장되지 않습니다.
> 

## <a name="replication-of-corrupt-application-data"></a>손상된 애플리케이션 데이터의 복제
새로 배포된 애플리케이션 업그레이드에 버그가 있는 경우 데이터 손상을 초래할 수 있습니다. 애플리케이션 업그레이드를 시작하여 잘못된 지역 번호로 신뢰할 수 있는 사전의 모든 전화번호 레코드를 업데이트하는 경우를 예로 들 수 있습니다.  이 경우 서비스 패브릭이 저장하는 데이터의 본질을 인지하지 못하므로 잘못된 전화 번호가 복제됩니다.

이렇게 데이터 손상을 초래할 수 있는 황당한 버그를 탐지한 후에 가장 먼저 할 일은 애플리케이션 수준에서 서비스를 고정하고, 가능하다면 버그가 없는 애플리케이션 코드의 버전으로 업그레이드하는 것입니다.  그러나 서비스 코드가 고정된 후에도 데이터 손상이 지속되어 데이터 복원이 필요할 수 있습니다.  이 경우에는 최신 백업도 손상되었을 수 있기 때문에 최신 백업을 복원하는 것으로는 부족할 수 있습니다.  따라서 데이터 손상 이전에 만든 최신 백업을 찾아야 합니다.

백업이 손상되었는지 확실하지 않은 경우 새 서비스 패브릭 클러스터를 배포하고 위의 "삭제되거나 손상된 서비스" 시나리오와 마찬가지로 영향을 받는 파티션의 백업을 복원할 수 있습니다.  각 파티션에 대해 가장 최근부터 가장 오래된 순으로 백업을 복원하기 시작합니다. 손상되지 않은 백업을 찾은 후 이 파티션에 있는 모든 백업(해당 백업보다 최신 버전)을 이동/삭제합니다. 각 파티션에 대해 이 프로세스를 반복합니다. 이제 프로덕션 클러스터의 파티션에서 `OnDataLossAsync`가 호출되면 외부 저장소에 있는 마지막 백업이 위의 프로세스로 선택된 백업이 됩니다.

이제 "삭제되거나 손실된 서비스" 섹션의 단계를 사용하여 버그가 있는 코드로 손상되기 이전의 상태로 서비스 상태를 복원할 수 있습니다.

다음 사항에 유의하세요.

  - 복원할 때 복원 중인 백업이 데이터 손실 이전의 파티션 상태보다 오래된 것일 가능성이 있습니다. 이 때문에 가능한 많은 데이터를 복구하기 위한 마지막 수단으로만 복원해야 합니다.
  - 백업 폴더 경로와 백업 폴더 내 파일 경로를 나타내는 문자열은 FabricDataRoot 경로 및 애플리케이션 형식 이름의 길이에 따라 255자보다 길 수 있습니다. 이로 인해 `Directory.Move`와 같은 일부 .NET 메서드에서 `PathTooLongException` 예외가 발생(throw)될 수 있습니다. 한 가지 해결 방법은 `CopyFile`과 같은 kernel32 API를 직접 호출하는 것입니다.

## <a name="back-up-and-restore-reliable-actors"></a>Reliable Actors 백업 및 복원


Reliable Actors 프레임워크는 Reliable Services를 기반으로 구축됩니다. 작업자를 호스트하는 ActorService는 상태 저장 신뢰할 수 있는 서비스입니다. 따라서 Reliable Services에서 사용 가능한 모든 백업 및 복원 기능이 Reliable Actors에도 제공됩니다(상태 제공자와 관련된 동작은 제외). 백업이 파티션 단위로 수행되기 때문에 파티션에서 모든 행위자에 대한 상태는 백업됩니다(또한 복원은 비슷하고 파티션 기준으로 발생함). 백업/복원을 수행하려면 서비스 소유자는 ActorService에서 파생되는 사용자 지정 행위자 서비스 클래스를 만든 다음 이전 섹션에서 설명한 것과 같이 Reliable Services와 유사한 백업/복원을 수행해야 합니다.

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo)
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

사용자 지정 행위자 서비스 클래스를 만들 경우 행위자를 등록할 때 사용자 지정 행위자 서비스도 등록해야 합니다.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Reliable Actors에 대한 기본 상태 제공자는 `KvsActorStateProvider`입니다. `KvsActorStateProvider`에 대한 증분 백업은 기본적으로 사용하지 않도록 설정됩니다. 다음 코드 조각과 같이 생성자에서 적절한 설정을 사용하여 `KvsActorStateProvider`를 만든 다음 ActorService 생성자에 전달하여 증분 백업을 사용하도록 설정할 수 있습니다.

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

증분 백업을 사용하도록 설정한 후 다음 이유 중 하나로 인한 FabricMissingFullBackupException 때문에 증분 백업이 실패할 수 있으며, 증분 백업을 수행하기 전에 전체 백업을 수행해야 합니다.

  - 복제본이 주가 된 이후로 전체 백업을 수행한 적이 없었습니다.
  - 마지막 백업이 수행된 이후로 로그 레코드의 일부가 잘렸습니다.

증분 백업을 사용하도록 설정하면 `KvsActorStateProvider`에서 순환 버퍼를 사용하여 해당 로그 레코드를 관리하지 않으며 정기적으로 자릅니다. 사용자가 45분 동안 백업을 수행하지 않을 경우 시스템이 로그 레코드를 자동으로 자릅니다. 이 간격은 `KvsActorStateProvider` 생성자에서 `logTruncationIntervalInMinutes`를 지정하여 구성할 수 있습니다(증분 백업을 사용하도록 설정하는 경우와 비슷함). 주 복제본이 모든 데이터를 전송하여 다른 복제본을 구축해야 하는 경우 로그 레코드가 잘릴 수도 있습니다.

백업 체인에서 복원 작업을 수행할 때는 Reliable Services와 마찬가지로 BackupFolderPath에 전체 백업이 포함된 하위 디렉터리 하나와 증분 백업이 포함된 다른 하위 디렉터리가 포함되어 있어야 합니다. 백업 체인 유효성 검사에 실패할 경우 복원 API에서 적절한 오류 메시지와 함께 FabricException을 throw합니다. 

> [!NOTE]
> `KvsActorStateProvider`는 현재 RestorePolicy.Safe 옵션을 무시합니다. 이 기능은 이후 릴리스에서 지원될 예정입니다.
> 

## <a name="testing-back-up-and-restore"></a>백업 및 복원 테스트
데이터가 백업되고 복원될 수 있도록 하는 것이 중요합니다. 이 작업은 특정 파티션에서 데이터 손실을 유도할 수 있는 PowerShell의 `Start-ServiceFabricPartitionDataLoss` cmdlet을 호출하여 서비스에 대한 데이터 백업 및 복원 기능이 예상대로 작동하는지 테스트함으로써 수행할 수 있습니다.  프로그래밍 방식으로 데이터 손실을 호출하고 해당 이벤트에서 복원하는 것도 가능합니다.

> [!NOTE]
> 백업의 샘플 구현을 찾고 GitHub의 웹 참조 앱에서 기능을 복원할 수 있습니다. 자세한 내용은 `Inventory.Service` 서비스를 참조하세요.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>내부 살펴보기: 백업 및 복원에 대한 자세한 내용 
다음은 백업 및 복원에 대한 자세한 내용입니다.

### <a name="backup"></a>Backup
Reliable State Manager는 읽기 및 쓰기 작업을 차단하지 않고 일관된 백업을 만드는 기능을 제공합니다. 이를 위해 검사점 및 로그 지속성 메커니즘을 활용합니다.  Reliable State Manager는 트랜잭션 로그로부터의 부담을 줄이고 복구 시간을 단축하기 위해 특정 지점에서 유사 항목(경량) 검사점을 사용합니다.  `BackupAsync`가 호출되면 신뢰할 수 있는 상태 관리자에서 모든 신뢰할 수 있는 개체에 최신 검사점 파일을 로컬 백업 폴더에 복사하도록 지시합니다.  그런 다음 Reliable State Manager가 "시작  포인터"에서부터 마지막 로그 레코드까지의 모든 로그 레코드를 백업 폴더에 복사합니다.  최신 로그 레코드까지의 모든 로그 레코드가 백업에 포함되고 신뢰할 수 있는 상태 관리자에서 미리 쓰기 로깅을 유지하므로 신뢰할 수 있는 상태 관리자는 커밋된 모든 트랜잭션(`CommitAsync`가 성공적으로 반환됨)이 백업에 포함되도록 보장합니다.

`BackupAsync`가 호출된 후에 커밋되는 모든 트랜잭션은 백업에 포함되거나 포함되지 않을 수 있습니다.  로컬 백업 폴더를 플랫폼에서 입력한 후에는(즉, 런타임에서 로컬 백업 완료됨) 서비스의 백업 콜백이 호출됩니다.  이 콜백은 백업 폴더를 Azure Storage 등의 외부 위치로 이동하는 것을 담당합니다.

### <a name="restore"></a>복원
신뢰할 수 있는 상태 관리자에서는 `RestoreAsync` API를 사용하여 백업에서 복원하는 기능을 제공합니다.  
`RestoreContext`의 `RestoreAsync` 메서드는 `OnDataLossAsync` 메서드 내에서만 호출할 수 있습니다.
`OnDataLossAsync`에서 반환한 부울 값은 서비스에서 외부 원본으로부터 상태를 복원했는지 여부를 나타냅니다.
`OnDataLossAsync`에서 true를 반환하면 Service Fabric에서 이 주 복제본의 다른 모든 복제본을 다시 빌드합니다. Service Fabric은 `OnDataLossAsync` 호출을 받는 복제본이 먼저 주 역할로 전환되지만 읽기 상태 또는 쓰기 상태가 부여되지 않도록 합니다.
즉 `OnDataLossAsync`가 성공적으로 완료될 때까지 StatefulService 구현자에 대해 `RunAsync`가 호출되지 않습니다.
그런 다음 `OnDataLossAsync`가 새 주 복제본에서 호출됩니다.
서비스가 이 API를 성공적으로 완료하고(True 또는 False 반환) 관련 재구성을 마치면 한 번에 하나씩 API가 계속 호출됩니다.

`RestoreAsync`는 먼저 호출된 주 복제본의 모든 기존 상태를 삭제합니다. 그런 다음 신뢰할 수 있는 상태 관리자가 백업 폴더에 존재하는 모든 신뢰 개체를 만듭니다. 다음으로 백업 폴더의 검사점으로부터 백업하도록 신뢰 개체에게 지시합니다. 마지막으로 Reliable State Manager가 백업 폴더의 로그 레코드에서 자체 상태를 복구하고 복구를 수행합니다. 복구 프로세스의 일환으로 백업 폴더에서 커밋된 로그 레코드가 있는 "시작점"에서 시작하는 작업이 신뢰할 수 있는 개체에 재생됩니다. 이 단계를 통해 일관된 복구 상태를 유지합니다.

## <a name="next-steps"></a>다음 단계
  - [신뢰할 수 있는 컬렉션](service-fabric-work-with-reliable-collections.md)
  - [Reliable Services 빠른 시작](service-fabric-reliable-services-quick-start.md)
  - [Reliable Services 알림](service-fabric-reliable-services-notifications.md)
  - [Reliable Services 구성](service-fabric-reliable-services-configuration.md)
  - [신뢰할 수 있는 컬렉션에 대한 개발자 참조](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Azure Service Fabric에서 정기적인 백업 및 복원](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

