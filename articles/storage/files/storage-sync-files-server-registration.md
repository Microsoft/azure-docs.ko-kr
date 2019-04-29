---
title: Azure 파일 동기화에 등록된 서버 관리 | Microsoft Docs
description: Azure 파일 동기화 저장소 동기화 서비스에서 Windows Server를 등록 및 등록 취소하는 방법을 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9f1195927acee143a34ec6c74f3ad301194fbc84
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759482"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Azure 파일 동기화에 등록된 서버 관리
Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 희생하지 않고 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. 이 작업은 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환하여 수행합니다. Windows Server에서 사용할 수 있는 아무 프로토콜이나 사용하여 데이터를 로컬로(SMB, NFS 및 FTPS 포함) 액세스할 수 있으며 세계 전역에 걸쳐 필요한 만큼 캐시를 보유할 수 있습니다.

다음 문서에서는 저장소 동기화 서비스를 사용하여 서버를 등록하고 관리하는 방법을 보여 줍니다. 종단 간 Azure 파일 동기화를 배포하는 방법에 대한 자세한 내용은 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="registerunregister-a-server-with-storage-sync-service"></a>저장소 동기화 서비스로 서버 등록/등록 취소
Azure 파일 동기화를 사용하여 서버를 등록하면 Windows Server와 Azure 간에 트러스트 관계가 설정됩니다. 그런 다음 이 관계를 사용하여 Azure 파일 공유(*클라우드 엔드포인트*라고도 함)와 동기화해야 하는 특정 폴더를 나타내는 *서버 엔드포인트*를 서버에 만들 수 있습니다. 

### <a name="prerequisites"></a>필수 조건
저장소 동기화 서비스를 사용하여 서버를 등록하려면 먼저 필요한 필수 구성 요소를 갖춘 서버를 준비해야 합니다.

* 서버에서 지원되는 Windows Server 버전을 실행해야 합니다. 자세한 내용은 [Azure 파일 동기화 시스템 요구 사항 및 상호 운용성](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability)을 참조하세요.
* 저장소 동기화 서비스가 배포되었는지 확인합니다. 저장소 동기화 서비스를 배포하는 방법에 대한 자세한 내용은 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.
* 서버가 인터넷에 연결되어 있고 Azure가 액세스 가능한지 확인합니다.
* 서버 관리자 UI를 사용하여 관리자에 대한 IE 고급 보안 구성을 사용하지 않도록 설정합니다.
    
    ![IE 고급 보안 구성이 강조 표시된 서버 관리자 UI](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Azure PowerShell 모듈이 서버에 설치되어 있는지 확인합니다. 서버가 장애 조치(failover) 클러스터의 멤버인 경우 클러스터의 모든 노드에 Az 모듈이 필요합니다. Az 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-Az-ps)을 참조하세요.

    > [!Note]  
    > 최신 버전의 Az PowerShell 모듈을 사용하여 서버를 등록/등록 취소하는 것이 좋습니다. Az 패키지가 이전에 이 서버에 설치되었고 이 서버의 PowerShell 버전이 5.* 이상인 경우 `Update-Module` cmdlet을 사용하여 이 패키지를 업데이트할 수 있습니다. 
* 사용자 환경에서 네트워크 프록시 서버를 사용하는 경우 활용할 동기화 에이전트에 대한 서버에서 프록시 설정을 구성합니다.
    1. 프록시 IP 주소 및 포트 번호 결정
    2. 다음 두 파일을 편집합니다.
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. 위의 두 파일에서 /System.ServiceModel 아래에 이 섹션 아래에 있는 그림 1의 줄을 추가하여 127.0.0.1:8888을 올바른 IP 주소(127.0.0.1 대체)와 올바른 포트 번호(8888 대체)로 변경합니다.
    4. 다음과 같이 명령줄을 통해 WinHTTP 프록시 설정을 지정합니다.
        * 프록시 표시: netsh winhttp show proxy
        * 프록시 설정: netsh winhttp set proxy 127.0.0.1:8888
        * 프록시 다시 설정: netsh winhttp reset proxy
        * 에이전트 설치 후 설정되는 경우 동기화 에이전트 다시 시작: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>저장소 동기화 서비스에 서버 등록
Azure 파일 동기화의 *동기화 그룹*에서 서버를 *서버 엔드포인트*로 사용하려면 먼저 해당 서버를 *저장소 동기화 서비스*에 등록해야 합니다. 서버는 한 번에 하나의 저장소 동기화 서비스에만 등록할 수 있습니다.

#### <a name="install-the-azure-file-sync-agent"></a>Azure 파일 동기화 에이전트 설치
1. [Azure 파일 동기화 에이전트를 다운로드](https://go.microsoft.com/fwlink/?linkid=858257)합니다.
2. Azure 파일 동기화 에이전트 설치 관리자를 시작합니다.
    
    ![Azure 파일 동기화 에이전트 설치 관리자의 첫 번째 창](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Microsoft 업데이트를 사용하여 Azure 파일 동기화 에이전트에 대한 업데이트를 사용하도록 설정해야 합니다. 서버 패키지에 대한 중요한 보안 수정과 향상된 기능은 Microsoft 업데이트를 통해 제공됩니다.

    ![Azure 파일 동기화 에이전트 설치 관리자의 [Microsoft 업데이트] 창에서 Microsoft 업데이트가 사용하도록 설정되어 있는지 확인합니다.](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. 이전에 서버가 등록되지 않은 경우에는 설치를 완료한 후 바로 서버 등록 UI가 표시됩니다.

> [!Important]  
> 서버가 장애 조치(failover) 클러스터의 멤버인 경우 Azure 파일 동기화 에이전트를 클러스터의 모든 노드에 설치해야 합니다.

#### <a name="register-the-server-using-the-server-registration-ui"></a>서버 등록 UI를 사용하여 서버 등록
> [!Important]  
> CSP(클라우드 솔루션 공급자) 구독은 서버 등록 UI를 사용할 수 없습니다. 대신 PowerShell을 사용합니다(이 섹션 아래).

1. Azure 파일 동기화 에이전트 설치를 완료한 후 바로 서버 등록 UI가 시작되지 않은 경우에는 `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`를 실행하여 수동으로 해당 UI를 시작할 수 있습니다.
2. *로그인*을 클릭하여 Azure 구독에 액세스합니다. 

    ![서버 등록 UI 대화 상자 열기](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. 대화 상자에서 올바른 구독, 리소스 그룹 및 저장소 동기화 서비스를 선택합니다.

    ![저장소 동기화 서비스 정보](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. 미리 보기에서는 프로세스를 완료하려면 한 번 더 로그인해야 합니다. 

    ![로그인 대화 상자](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> 서버가 장애 조치(failover) 클러스터의 멤버인 경우 각 서버는 서버 등록을 실행해야 합니다. Azure Portal에서 등록된 서버로 표시되면 Azure 파일 동기화에서 자동으로 각 노드를 동일한 장애 조치 클러스터의 멤버로 인식하여 적절하게 그룹화합니다.

#### <a name="register-the-server-with-powershell"></a>PowerShell로 서버 등록
PowerShell을 통해 서버 등록을 수행할 수도 있습니다. 다음은 CSP(클라우드 솔루션 공급자) 구독에 지원되는 유일한 서버 등록 방법입니다.

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>저장소 동기화 서비스에서 서버 등록 취소
저장소 동기화 서비스에서 서버 등록을 취소하려면 여러 단계를 수행해야 합니다. 서버를 제대로 등록 취소하는 방법을 살펴보겠습니다.

> [!Warning]  
> Microsoft 엔지니어가 명시적으로 지시하기 전에 서버 등록을 취소하거나, 등록하거나, 서버 엔드포인트를 제거하고 다시 만들어서 동기화, 클라우드 계층화 또는 Azure 파일 동기화의 다른 부분에서 발생한 문제를 해결하려고 하지 않습니다. 서버 등록을 취소하고 서버 엔트포인트를 제거하는 것은 파괴적 작업입니다. 서버 엔드포인트를 사용하여 볼륨에 계층화된 파일은 등록된 서버 및 서버 엔드포인트를 다시 만든 후에 Azure 파일 공유의 해당 위치에 "다시 연결"되지 않습니다. 그러면 동기화 오류가 발생합니다. 또한 서버 엔드포인트 네임스페이스 외부에 있는 계층화된 파일은 영구적으로 손실될 수 있습니다. 클라우드 계층화를 한 번도 사용하지 않아도 계층화된 파일이 서버 엔드포인트에 있을 수 있습니다.

#### <a name="optional-recall-all-tiered-data"></a>(선택 사항) 모든 계층화된 데이터 기억
현재 계층화된 파일을 Azure 파일 동기화(예: 테스트, 환경이 아닌 프로덕션임)를 제거한 후에 사용할 수 있도록 하려면 서버 엔드포인트를 포함하는 각 볼륨에서 모든 파일을 회수합니다. 모든 서버 엔드포인트에 대해 계층화된 클라우드를 사용하지 않도록 설정한 다음, 다음과 같은 PowerShell cmdlet을 실행합니다.

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> 계층화된 모든 데이터를 회수할 만큼 충분한 사용 가능한 공간이 서버 엔드포인트를 호스팅하는 로컬 볼륨에 없으면 `Invoke-StorageSyncFileRecall` cmdlet이 실패합니다.  

#### <a name="remove-the-server-from-all-sync-groups"></a>모든 동기화 그룹에서 서버 제거
저장소 동기화 서비스에서 서버의 등록을 취소하기 전에 먼저 해당 서버에 대한 서버 엔드포인트를 모두 제거해야 합니다. 이 작업은 Azure Portal을 통해 수행할 수 있습니다.

1. 서버가 등록되어 있는 저장소 동기화 서비스로 이동합니다.
2. 저장소 동기화 서비스의 각 동기화 그룹에서 이 서버에 대한 서버 엔드포인트를 모두 제거합니다. 이렇게 하려면 동기화 그룹 창에서 관련 서버 엔드포인트를 마우스 오른쪽 단추로 클릭합니다.

    ![동기화 그룹에서 서버 엔드포인트 제거](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

간단한 PowerShell 스크립트를 사용하여 이 작업을 수행할 수도 있습니다.

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Connect-AzAccount
Login-AzStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

#### <a name="unregister-the-server"></a>서버 등록 취소
이제 모든 데이터가 회수되고 서버가 모든 동기화 그룹에서 제거되었으므로 서버의 등록을 취소할 수 있습니다. 

1. Azure Portal에서 저장소 동기화 서비스의 *등록된 서버* 섹션으로 이동합니다.
2. 등록 취소할 서버를 마우스 오른쪽 단추로 클릭하고 “서버 등록 취소”를 클릭합니다.

    ![서버 등록 취소](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Azure 파일 동기화가 데이터 센터의 좋은 이웃이 되도록 보장 
Azure 파일 동기화는 거의 데이터 센터에서 실행되는 유일한 서비스가 아니기 때문에 Azure 파일 동기화의 네트워크 및 저장소 사용량을 제한하는 것이 좋습니다.

> [!Important]  
> 한도를 너무 낮게 설정하면 Azure 파일 동기화 동기화 및 회수 성능에 영향을 줍니다.

### <a name="set-azure-file-sync-network-limits"></a>Azure 파일 동기화 네트워크 제한 설정
`StorageSyncNetworkLimit`cmdlets을 사용하여 Azure 파일 동기화의 네트워크 사용률을 조절할수 있습니다.

> [!Note]  
> 계층화된 파일에 액세스하거나 Invoke-StorageSyncFileRecall cmdlet을 사용하는 경우 네트워크 제한이 적용되지 않습니다.

예를 들어 Azure 파일 동기화에서 작업 주간 동안 오전 9시에서 오후 5시(17:00) 사이에 10Mbps를 초과하여 사용하지 않도록 새 스로틀 제한을 만들 수 있습니다. 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

다음 cmdlet을 사용하여 제한을 확인할 수 있습니다.

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

네트워크 제한을 제거하려면 `Remove-StorageSyncNetworkLimit`를 사용합니다. 예를 들어 다음 명령은 모든 네트워크 제한을 제거합니다.

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Windows Server 저장소 QoS 사용 
Azure 파일 동기화가 Windows Server 가상화 호스트에서 실행되는 가상 머신에 호스팅되는 경우 저장소 QoS(저장소 서비스 품질)를 사용하여 저장소 IO 사용을 제어할 수 있습니다. 스토리지 QoS 정책은 최대(또는 위에서 StorageSyncNetwork 제한이 적용되는 방식과 같은 제한) 또는 최소(또는 예약) 중 하나로 설정할 수 있습니다. 최대 대신 최소를 설정하면 다른 워크로드에서 사용하지 않는 경우 Azure 파일 동기화에서 사용 가능한 저장소 대역폭을 사용하도록 버스트할 수 있습니다. 자세한 내용은 [저장소 서비스 품질](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview)을 참조하세요.

## <a name="see-also"></a>참고 항목
- [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
- [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
- [Azure 파일 동기화 모니터링](storage-sync-files-monitoring.md)
- [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)
