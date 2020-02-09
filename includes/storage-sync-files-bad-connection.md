---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b2ff542d2782293e89b66e5d25cb67a9bcde6da8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773013"
---
서버에서 Azure 파일 동기화 서비스에 액세스할 수 없을 때마다 이 오류가 발생할 수 있습니다. 다음 단계를 진행하여 이 오류를 해결할 수 있습니다.

1. Windows 서비스 `FileSyncSvc.exe`가 방화벽에 의해 차단되지 않았는지 확인합니다.
2. 포트 443이 Azure 파일 동기화 서비스로 나가는 연결에 대해 열려 있는지 확인합니다. `Test-NetConnection` cmdlet을 사용하면 됩니다. 아래의 `<azure-file-sync-endpoint>` 자리 표시자에 대한 URL은 [Azure 파일 동기화 프록시 및 방화벽 설정](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) 문서에서 찾을 수 있습니다. 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. 프록시 구성이 예상대로 설정되었는지 확인합니다. `Get-StorageSyncProxyConfiguration` cmdlet으로 할 수 있습니다. Azure 파일 동기화에 대해 프록시를 구성하는 자세한 방법은 [Azure 파일 동기화 프록시 및 방화벽 설정](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall)에서 찾을 수 있습니다.

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. 테스트-StorageSyncNetworkConnectivity cmdlet을 사용 하 여 서비스 엔드포인트에 대 한 네트워크 연결을 확인 합니다. 자세히 알아보려면 [서비스 엔드포인트에 대 한 네트워크 연결 테스트](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints)를 참조 하세요.    

5. 네트워크 연결 문제와 관련하여 추가적인 도움이 필요하면 네트워크 관리자에게 문의하세요.
