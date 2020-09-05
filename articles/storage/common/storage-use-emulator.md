---
title: Azure Storage 에뮬레이터를 사용 하 여 개발 및 테스트
description: Azure Storage 에뮬레이터는 Azure Storage 응용 프로그램을 개발 하 고 테스트 하기 위한 무료 로컬 개발 환경을 제공 합니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: 959a58a38861075c6509fe57136d8991eeb98ce6
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588197"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Azure Storage 에뮬레이터를 사용 하 여 개발 및 테스트

Microsoft Azure Storage 에뮬레이터은 로컬 개발 목적으로 Azure Blob, 큐 및 테이블 서비스를 에뮬레이트하는 도구입니다. Azure 구독을 만들거나 비용을 발생 시 키 지 않고 저장소 서비스에 대해 로컬로 응용 프로그램을 테스트할 수 있습니다. 에뮬레이터에서 응용 프로그램이 작동 하는 방식에 만족 하는 경우 클라우드에서 Azure storage 계정을 사용 하도록 전환 합니다.

> [!IMPORTANT]
> Azure Storage 에뮬레이터가 더 이상 적극적으로 개발 되 고 있지 않습니다. [**Azurite**](storage-use-azurite.md) 는 향후 저장소 에뮬레이터 플랫폼입니다. Azurite는 Azure Storage 에뮬레이터를 대체 합니다. Azurite는 최신 버전의 Azure Storage Api를 지원 하도록 계속 업데이트 됩니다. 자세한 내용은 [**Azurite 에뮬레이터를 사용 하 여 로컬 Azure Storage 개발**](storage-use-azurite.md)을 참조 하세요.

## <a name="get-the-storage-emulator"></a>저장소 에뮬레이터 가져오기

저장소 에뮬레이터는 [MICROSOFT AZURE SDK](https://azure.microsoft.com/downloads/)의 일부로 제공 됩니다. [독립 실행형 설치 관리자](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (직접 다운로드)를 사용 하 여 저장소 에뮬레이터를 설치할 수도 있습니다. 저장소 에뮬레이터를 설치 하려면 컴퓨터에 대 한 관리자 권한이 있어야 합니다.

저장소 에뮬레이터는 현재 Windows 에서만 실행 됩니다. Linux 용 저장소 에뮬레이터가 필요한 경우 한 가지 옵션은 커뮤니티에서 유지 관리 되는 오픈 소스 저장소 에뮬레이터 [Azurite](https://github.com/azure/azurite)입니다.

> [!NOTE]
> 한 버전의 저장소 에뮬레이터에서 만든 데이터는 다른 버전을 사용 하는 경우에는 액세스할 수 없습니다. 장기적으로 데이터를 유지 해야 하는 경우 저장소 에뮬레이터가 아닌 Azure 저장소 계정에 해당 데이터를 저장 하는 것이 좋습니다.
> 
> 저장소 에뮬레이터는 OData 라이브러리의 특정 버전에 따라 달라 집니다. 저장소 에뮬레이터에서 사용 하는 OData Dll을 다른 버전으로 바꾸는 것은 지원 되지 않으며 예기치 않은 동작이 발생할 수 있습니다. 그러나 스토리지 서비스가 지원하는 어떤 버전의 OData도 에뮬레이터에 요청을 보내는 데 사용할 수 있습니다.

## <a name="how-the-storage-emulator-works"></a>스토리지 에뮬레이터의 작동 원리

저장소 에뮬레이터는 로컬 Microsoft SQL Server 2012 Express LocalDB 인스턴스를 사용 하 여 Azure Storage 서비스를 에뮬레이트합니다. LocalDB 인스턴스 대신 SQL Server의 로컬 인스턴스에 액세스 하도록 저장소 에뮬레이터를 구성 하도록 선택할 수 있습니다. 자세한 내용은이 문서 뒷부분의 [저장소 에뮬레이터 시작 및 초기화](#start-and-initialize-the-storage-emulator) 섹션을 참조 하세요.

저장소 에뮬레이터는 Windows 인증을 사용 하 여 SQL Server 또는 LocalDB에 연결 합니다.

저장소 에뮬레이터와 Azure storage 서비스 간에는 몇 가지 기능의 차이점이 있습니다. 이러한 차이점에 대 한 자세한 내용은이 문서 뒷부분의 [저장소 에뮬레이터와 Azure Storage의 차이점](#differences-between-the-storage-emulator-and-azure-storage) 섹션을 참조 하세요.

## <a name="start-and-initialize-the-storage-emulator"></a>저장소 에뮬레이터 시작 및 초기화

Azure Storage 에뮬레이터를 시작 하려면:

1. **시작** 단추를 선택하거나 **Windows** 키를 누릅니다.
2. `Azure Storage Emulator`를 입력하여 시작합니다.
3. 표시된 애플리케이션 목록에서 에뮬레이터를 선택합니다.

저장소 에뮬레이터가 시작 되 면 명령 프롬프트 창이 표시 됩니다. 이 콘솔 창을 사용 하 여 저장소 에뮬레이터를 시작 하 고 중지할 수 있습니다. 또한 명령 프롬프트에서 데이터를 지우고, 상태를 가져오고, 에뮬레이터를 초기화할 수 있습니다. 자세한 내용은이 문서의 뒷부분에 나오는 [저장소 에뮬레이터 명령줄 도구 참조](#storage-emulator-command-line-tool-reference) 섹션을 참조 하세요.

> [!NOTE]
> Azurite와 같은 다른 저장소 에뮬레이터가 시스템에서 실행 되는 경우 Azure Storage 에뮬레이터가 제대로 시작 되지 않을 수 있습니다.

에뮬레이터를 실행 하는 경우 Windows 작업 표시줄 알림 영역에 아이콘이 표시 됩니다.

저장소 에뮬레이터 명령 프롬프트 창을 닫으면 저장소 에뮬레이터가 계속 실행 됩니다. 저장소 에뮬레이터 콘솔 창을 다시 표시 하려면 저장소 에뮬레이터를 시작 하는 것 처럼 앞의 단계를 수행 합니다.

저장소 에뮬레이터를 처음 실행 하면 로컬 저장소 환경이 초기화 됩니다. 초기화 프로세스에서는 LocalDB에 데이터베이스가 작성되며 각 로컬 스토리지 서비스용으로 HTTP 포트가 예약됩니다.

저장소 에뮬레이터는 기본적으로에 설치 됩니다 `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator` .

> [!TIP]
> [Microsoft Azure Storage 탐색기](https://storageexplorer.com) 를 사용 하 여 로컬 저장소 에뮬레이터 리소스 작업을 수행할 수 있습니다. 저장소 에뮬레이터를 설치 하 고 시작한 후에 Storage 탐색기 리소스 트리의 "로컬 & 연결 됨"에서 "(에뮬레이터-기본 포트) (키)"를 찾습니다.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>다른 SQL 데이터베이스를 사용 하도록 저장소 에뮬레이터 초기화

저장소 에뮬레이터 명령줄 도구를 사용 하 여 기본 LocalDB 인스턴스가 아닌 SQL database 인스턴스를 가리키도록 저장소 에뮬레이터를 초기화할 수 있습니다.

1. [저장소 에뮬레이터 시작 및 초기화](#start-and-initialize-the-storage-emulator) 섹션에 설명 된 대로 저장소 에뮬레이터 콘솔 창을 엽니다.
1. 콘솔 창에 다음 명령을 입력합니다. 여기서 `<SQLServerInstance>`은 SQL Server 인스턴스의 이름입니다. LocalDB를 사용하려면 SQL Server 인스턴스로 `(localdb)\MSSQLLocalDb`을 지정합니다.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   기본 SQL Server 인스턴스를 사용하도록 에뮬레이터에 지시하는 다음 명령을 사용할 수도 있습니다.

   `AzureStorageEmulator.exe init /server .`

   또는 데이터베이스를 기본 LocalDB 인스턴스로 초기화 하는 다음 명령을 사용할 수 있습니다.

   `AzureStorageEmulator.exe init /forceCreate`

이러한 명령에 대 한 자세한 내용은 [저장소 에뮬레이터 명령줄 도구 참조](#storage-emulator-command-line-tool-reference)를 참조 하세요.

> [!TIP]
> LocalDB 설치를 포함하여 SQL Server 인스턴스를 관리하는 데 SSMS([Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms))를 사용할 수 있습니다. SMSS **서버에 연결** 대화 상자에서 **서버 이름:** 필드에 `(localdb)\MSSQLLocalDb`을 지정하여 LocalDB 인스턴스에 연결합니다.

## <a name="authenticating-requests-against-the-storage-emulator"></a>저장소 에뮬레이터에 대 한 요청 인증

저장소 에뮬레이터를 설치 하 고 시작한 후에는 코드를 테스트할 수 있습니다. 익명 요청이 아니면 저장소 에뮬레이터에 대해 수행 하는 모든 요청에 권한이 부여 되어야 합니다. 공유 키 인증 또는 SAS (공유 액세스 서명)를 사용 하 여 저장소 에뮬레이터에 대 한 요청을 인증할 수 있습니다.

### <a name="authorize-with-shared-key-credentials"></a>공유 키 자격 증명 인증

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

연결 문자열에 대한 자세한 내용은 [Azure Storage 연결 문자열 구성](../storage-configure-connection-string.md)을 참조하세요.

### <a name="authorize-with-a-shared-access-signature"></a>공유 액세스 서명을 사용하여 인증

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Xamarin 라이브러리와 같은 일부 Azure Storage 클라이언트 라이브러리는 공유 액세스 서명 (SAS) 토큰 인증만 지원합니다. [Storage 탐색기](https://storageexplorer.com/) 또는 공유 키 인증을 지 원하는 다른 응용 프로그램을 사용 하 여 SAS 토큰을 만들 수 있습니다.

또한 Azure PowerShell을 사용하여 SAS 토큰을 생성할 수 있습니다. 다음 예제에서는 blob 컨테이너에 대한 모든 권한을 가진 SAS 토큰을 생성합니다.

1. Azure PowerShell이 아직 없는 경우 설치합니다(최신 버전의 Azure PowerShell cmdlet 사용 권장). 설치 지침은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/install-Az-ps)을 참조하세요.
2. Azure PowerShell을 열고 다음 명령을 실행하여 `CONTAINER_NAME`을 선택한 이름으로 바꿉니다.

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

새 컨테이너에 대해 결과적으로 생성된 공유 액세스 서명 URI는 다음과 유사해야 합니다.

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

이 예제를 사용하여 만든 공유 액세스 서명은 하루 동안 유효 합니다. 서명은 컨테이너 내에서 blob에 대해 모든 권한을 부여합니다(읽기, 쓰기, 삭제, 나열).

공유 액세스 서명에 대 한 자세한 내용은 [SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여](storage-sas-overview.md)를 참조 하세요.

## <a name="addressing-resources-in-the-storage-emulator"></a>저장소 에뮬레이터에서 리소스의 주소 지정

저장소 에뮬레이터의 서비스 끝점은 Azure storage 계정에 대 한 끝점과 다릅니다. 로컬 컴퓨터는 도메인 이름 확인을 수행 하지 않으므로 저장소 에뮬레이터 끝점이 로컬 주소가 되어야 합니다.

Azure Storage 계정에 리소스 주소를 지정할 때는 다음 체계를 사용합니다. 계정 이름은 URI 호스트 이름의 일부이고 주소를 지정하는 리소스는 URI 경로의 일부입니다.

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

예를 들어, 다음 URI는 Azure Storage 계정의 blob에 대한 올바른 주소입니다.

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

로컬 컴퓨터가 도메인 이름 확인을 수행 하지 않기 때문에 계정 이름은 호스트 이름이 아닌 URI 경로의 일부입니다. 저장소 에뮬레이터에서 리소스에 대해 다음 URI 형식을 사용 합니다.

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

예를 들어 다음 주소는 저장소 에뮬레이터에서 blob에 액세스 하는 데 사용 될 수 있습니다.

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

저장소 에뮬레이터의 서비스 끝점은 다음과 같습니다.

* Blob service: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* 큐 서비스: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Table service: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>RA-GRS를 사용하여 보조 계정 주소 지정

버전 3.1부터 저장소 에뮬레이터는 읽기 액세스 지역 중복 복제 (RA-GRS)를 지원 합니다. 계정 이름에-secondary를 추가 하 여 보조 위치에 액세스할 수 있습니다. 예를 들어 다음 주소는 저장소 에뮬레이터에서 읽기 전용 보조 데이터베이스를 사용 하 여 blob에 액세스 하는 데 사용할 수 있습니다.

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> 저장소 에뮬레이터를 사용 하 여 보조 데이터베이스에 프로그래밍 방식으로 액세스 하려면 .NET 버전 3.2 이상에 대 한 저장소 클라이언트 라이브러리를 사용 합니다. 자세한 내용은 [.NET용 Microsoft Azure Storage 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/dn261237.aspx)를 참조하세요.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>저장소 에뮬레이터 명령줄 도구 참조

버전 3.0부터 스토리지 에뮬레이터를 시작할 때 콘솔 창이 표시됩니다. 콘솔 창에서 명령줄을 사용 하 여 에뮬레이터를 시작 및 중지 합니다. 또한 명령줄에서 상태를 쿼리하고 다른 작업을 수행할 수 있습니다.

> [!NOTE]
> Microsoft Azure 컴퓨팅 에뮬레이터가 설치되어 있으면 스토리지 에뮬레이터를 시작할 때 시스템 트레이 아이콘이 표시됩니다. 해당 아이콘을 마우스 오른쪽 단추로 클릭하면 표시되는 메뉴에서 그래픽 방식으로 스토리지 에뮬레이터를 시작하고 중지할 수 있습니다.
>
>

### <a name="command-line-syntax"></a>명령줄 구문

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>옵션

옵션 목록을 보려면 명령 프롬프트에 `/help` 을(를) 입력합니다.

| 옵션 | Description | 명령 | 인수 |
| --- | --- | --- | --- |
| **시작** |저장소 에뮬레이터를 시작 합니다. |`AzureStorageEmulator.exe start [-inprocess]` |*-다시 처리*: 새 프로세스를 만드는 대신 현재 프로세스에서 에뮬레이터를 시작 합니다. |
| **중지** |저장소 에뮬레이터를 중지 합니다. |`AzureStorageEmulator.exe stop` | |
| **상태** |저장소 에뮬레이터의 상태를 인쇄 합니다. |`AzureStorageEmulator.exe status` | |
| **지우기** |명령줄에 지정된 모든 서비스의 데이터를 지웁니다. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob*: blob 데이터를 지웁니다. <br/>*queue*: 큐 데이터를 지웁니다. <br/>*table*: 테이블 데이터를 지웁니다. <br/>*all*: 모든 서비스의 모든 데이터를 지웁니다. |
| **Init** |에뮬레이터를 설정 하기 위해 일회성 초기화를 수행 합니다. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*: SQL 인스턴스를 호스팅하는 서버를 지정합니다. <br/>*-sqlinstance instanceName*: 기본 서버 인스턴스에서 사용할 SQL 인스턴스의 이름을 지정합니다. <br/>*-forcecreate*: 이미 존재하는 경우라도 SQL Database를 강제로 생성합니다. <br/>*-skipcreate*: SQL 데이터베이스 만들기를 건너뜁니다. 이 옵션은 -forcecreate보다 우선합니다.<br/>*-reserveports*: 서비스와 연결된 HTTP 포트를 예약하려고 합니다.<br/>*-unreserveports*: 서비스와 연결된 HTTP 포트에 대한 예약을 제거하려고 합니다. 이 옵션은 -reserveports보다 우선합니다.<br/>*-inprocess*: 새 프로세스를 생성하는 대신 현재 프로세스의 초기화를 수행합니다. 포트 예약을 변경할 경우 관리자 권한으로 현재 프로세스를 시작해야 합니다. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>저장소 에뮬레이터와 Azure Storage 간의 차이점

저장소 에뮬레이터는 로컬에서 에뮬레이트된 환경 이므로 클라우드의 Azure Storage 계정 및 에뮬레이터를 사용 하는 것 사이에 차이가 있습니다.

* 저장소 에뮬레이터는 단일 고정 계정과 잘 알려진 인증 키만 지원 합니다.
* 저장소 에뮬레이터는 확장 가능한 저장소 서비스가 아니며 많은 수의 동시 클라이언트를 지원 하지 않습니다.
* [저장소 에뮬레이터에서 리소스를 주소 지정](#addressing-resources-in-the-storage-emulator)하는 방법에 설명 된 대로 리소스는 저장소 에뮬레이터와 Azure storage 계정에서 다르게 처리 됩니다. 차이점은 도메인 이름 확인은 클라우드에서 사용할 수 있지만 로컬 컴퓨터에서는 사용할 수 없기 때문입니다.
* 버전 3.1부터 저장소 에뮬레이터 계정은 읽기 액세스 지역 중복 복제 (RA-GRS)를 지원 합니다. 에뮬레이터에서 모든 계정은 RA-GRS를 사용 하도록 설정 되어 있으며 주 복제본과 보조 복제본 사이에 지연이 발생 하지 않습니다. Blob 서비스 통계 가져오기, 큐 서비스 통계 가져오기 및 테이블 서비스 통계 가져오기 작업은 보조 계정에서 지원되며 기본 SQL 데이터베이스에 따라 현재 시간으로 `LastSyncTime` 응답 요소의 값을 항상 반환합니다.
* 파일 서비스 및 SMB 프로토콜 서비스 끝점은 현재 저장소 에뮬레이터에서 지원 되지 않습니다.
* 에뮬레이터에서 지원 되지 않는 저장소 서비스 버전을 사용 하는 경우 에뮬레이터가 VersionNotSupportedByEmulator 오류 (HTTP 상태 코드 400-잘못 된 요청)를 반환 합니다.

### <a name="differences-for-blob-storage"></a>Blob Storage의 차이점

다음과 같은 차이점이 에뮬레이터의 Blob Storage에 적용됩니다.

* 저장소 에뮬레이터는 최대 2gb의 blob 크기만 지원 합니다.
* 저장소 에뮬레이터에서 blob 이름의 최대 길이는 256 자이 고 Azure Storage에 있는 blob 이름의 최대 길이는 1024 자입니다.
* 증분 복사를 사용하면 덮어쓴 Blob의 스냅샷을 복사할 수 있으며 이로 인해 서비스에 오류가 반환됩니다.
* 페이지 범위 가져오기 Diff는 증분 복사 Blob을 사용 하 여 복사한 스냅숏 간에 작동 하지 않습니다.
* 요청에 임대 ID가 지정 되지 않은 경우에도 활성 임대를 사용 하 여 저장소 에뮬레이터에 있는 blob에 대 한 Put Blob 작업이 성공할 수 있습니다.
* 에뮬레이터에서 추가 blob 작업을 지원 하지 않습니다. 추가 Blob에 대한 작업을 시도하면 FeatureNotSupportedByEmulator 오류(HTTP 상태 코드 400 - 잘못된 요청)가 반환됩니다.

### <a name="differences-for-table-storage"></a>Table Storage의 차이점

다음과 같은 차이점이 에뮬레이터의 Table Storage에 적용됩니다.

* 저장소 에뮬레이터의 Table service에 있는 날짜 속성은 SQL Server 2005에서 지원 되는 범위만 지원 합니다 (1753 년 1 월 1 일 이후 여야 함). 1753년 1월 1일 이전의 모든 날짜는 이 값으로 변경됩니다. 날짜의 정밀도는 SQL Server 2005의 정밀도로 제한되며, 즉 날짜의 정밀도는 1/300 초입니다.
* 저장소 에뮬레이터는 각각 512 바이트 미만의 파티션 키 및 행 키 속성 값을 지원 합니다. 계정 이름, 테이블 이름 및 키 속성 이름의 총 크기는 900 바이트를 초과할 수 없습니다.
* 저장소 에뮬레이터의 테이블에 있는 행의 총 크기는 1mb 미만으로 제한 됩니다.
* 저장소 에뮬레이터에서 데이터 형식 또는의 속성 `Edm.Guid` `Edm.Binary` 은 `Equal (eq)` `NotEqual (ne)` 쿼리 필터 문자열의 및 비교 연산자도 지원 합니다.

### <a name="differences-for-queue-storage"></a>Queue Storage의 차이점

에뮬레이터에서 Queue Storage에 특정 차이점이 있습니다.

## <a name="storage-emulator-release-notes"></a>저장소 에뮬레이터 릴리스 정보

### <a name="version-510"></a>버전 5.10

* 저장소 에뮬레이터는 Blob, Queue 및 Table service 끝점에서 저장소 서비스 버전 2019-07-07을 거부 하지 않습니다.

### <a name="version-59"></a>버전 5.9

* 저장소 에뮬레이터는 Blob, Queue 및 Table service 끝점에서 저장소 서비스 버전 2019-02-02을 거부 하지 않습니다.

### <a name="version-58"></a>버전 5.8

* 저장소 에뮬레이터는 Blob, Queue 및 Table service 끝점에서 저장소 서비스 버전 2018-11-09을 거부 하지 않습니다.

### <a name="version-57"></a>버전 5.7

* 로깅을 사용하도록 설정한 경우 충돌을 야기하는 버그가 수정되었습니다.

### <a name="version-56"></a>버전 5.6

* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 끝점에서 2018-03-28 버전의 저장소 서비스를 지원 합니다.

### <a name="version-55"></a>버전 5.5

* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 끝점에서 2017-11-09 버전의 저장소 서비스를 지원 합니다.
* BLOB 생성 시간을 반환하는 BLOB **Created** 속성에 대한 지원이 추가되었습니다.

### <a name="version-54"></a>버전 5.4

* 설치 안정성을 향상하기 위해, 에뮬레이터가 더 이상 설치 중에 포트를 예약하려고 시도하지 않습니다. 포트 예약을 원하는 경우 **init** 명령의 *-reserveports* 옵션을 사용 하 여 지정 합니다.

### <a name="version-53"></a>버전 5.3

* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 끝점에서 2017-07-29 버전의 저장소 서비스를 지원 합니다.

### <a name="version-52"></a>버전 5.2

* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 끝점에서 2017-04-17 버전의 저장소 서비스를 지원 합니다.
* 테이블 속성 값이 제대로 인코딩되지 않은 버그를 수정 했습니다.

### <a name="version-51"></a>버전 5.1

* 저장소 에뮬레이터가 `DataServiceVersion` 서비스가 아닌 일부 응답에서 헤더를 반환 하는 버그를 수정 했습니다.

### <a name="version-50"></a>버전 5.0

* 저장소 에뮬레이터 설치 관리자가 더 이상 기존 MSSQL 및 .NET Framework 설치를 확인 하지 않습니다.
* 저장소 에뮬레이터 설치 관리자가 더 이상 설치의 일부로 데이터베이스를 만들 필요가 없습니다. 필요한 경우 시작 시 데이터베이스가 만들어집니다.
* 데이터베이스를 만들 때 더 이상 관리자 권한이 필요하지 않습니다.
* 시작 시 더 이상 포트 예약이 필요하지 않습니다.
* `init`에 `-reserveports`(권한 상승 필요), `-unreserveports`(권한 상승 필요), `-skipcreate` 옵션을 추가합니다.
* 이제 시스템 트레이 아이콘의 저장소 에뮬레이터 UI 옵션은 명령줄 인터페이스를 시작 합니다. 이전 GUI는 더 이상 사용할 수 없습니다.
* 일부 DLL이 제거되거나 이름이 바뀌었습니다.

### <a name="version-46"></a>버전 4.6

* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 끝점에서 2016-05-31 버전의 저장소 서비스를 지원 합니다.

### <a name="version-45"></a>버전 4.5

* 백업 데이터베이스의 이름을 바꿀 때 설치 및 초기화가 실패 하는 버그를 수정 했습니다.

### <a name="version-44"></a>버전 4.4

* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 끝점에서 2015-12-11 버전의 저장소 서비스를 지원 합니다.
* Blob 데이터의 저장소 에뮬레이터 가비지 수집은 많은 개수의 blob을 처리할 때 더 효율적입니다.
* 컨테이너 ACL XML이 스토리지 서비스의 방법과는 약간 다르게 유효성 검사가 이뤄지게 하던 버그를 수정했습니다.
* 때로 최대 및 최소 DateTime 값이 잘못된 표준 시간대에 보고되도록 하던 버그를 수정했습니다.

### <a name="version-43"></a>버전 4.3

* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 끝점에서 2015-07-08 버전의 저장소 서비스를 지원 합니다.

### <a name="version-42"></a>버전 4.2

* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 끝점에서 2015-04-05 버전의 저장소 서비스를 지원 합니다.

### <a name="version-41"></a>버전 4.1

* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 끝점에서 2015-02-21 버전의 저장소 서비스를 지원 합니다. 새로운 blob 추가 기능을 지원 하지 않습니다.
* 이제 에뮬레이터에서 지원 되지 않는 저장소 서비스 버전에 대 한 의미 있는 오류 메시지를 반환 합니다. 최신 버전의 에뮬레이터를 사용하는 것이 좋습니다. VersionNotSupportedByEmulator 오류 (HTTP 상태 코드 400-잘못 된 요청)를 가져오는 경우 에뮬레이터의 최신 버전을 다운로드 합니다.
* 경합 조건으로 인해 동시 병합 작업 중 잘못된 테이블 엔터티 데이터가 발생하는 버그가 수정되었습니다.

### <a name="version-40"></a>버전 4.0

* 저장소 에뮬레이터 실행 파일의 이름이 *AzureStorageEmulator.exe*로 바뀌었습니다.

### <a name="version-32"></a>버전 3.2

* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 끝점에서 2014-02-14 버전의 저장소 서비스를 지원 합니다. 파일 서비스 끝점은 현재 저장소 에뮬레이터에서 지원 되지 않습니다. 2014-02-14 버전에 대한 자세한 내용은 [Azure Storage 서비스에 대한 버전 관리](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 를 참조하세요.

### <a name="version-31"></a>버전 3.1

* 이제 저장소 에뮬레이터에서 읽기 액세스 지역 중복 저장소 (RA-GRS)가 지원 됩니다. `Get Blob Service Stats`, `Get Queue Service Stats` 및 api는 `Get Table Service Stats` 계정 보조에 대해 지원 되며 기본 SQL 데이터베이스에 따라 항상 LastSyncTime response 요소의 값을 현재 시간으로 반환 합니다. 저장소 에뮬레이터를 사용 하 여 보조 데이터베이스에 프로그래밍 방식으로 액세스 하려면 .NET 버전 3.2 이상에 대 한 저장소 클라이언트 라이브러리를 사용 합니다. 자세한 내용은 .NET용 Microsoft Azure Storage 클라이언트 라이브러리 참조를 참조하세요.

### <a name="version-30"></a>버전 3.0

* Azure Storage 에뮬레이터는 계산 에뮬레이터와 동일한 패키지에 더 이상 제공 되지 않습니다.
* 저장소 에뮬레이터 그래픽 사용자 인터페이스는 더 이상 사용 되지 않습니다. 스크립트 가능한 명령줄 인터페이스로 대체 되었습니다. 명령줄 인터페이스에 대한 자세한 내용은 스토리지 에뮬레이터 명령줄 도구 참조를 참조하세요. 그래픽 인터페이스는 버전 3.0에 계속 있지만 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 스토리지 에뮬레이터 UI 표시 보기를 선택하여 Compute 에뮬레이터를 설치할 때에만 액세스 할 수 있습니다.
* 이제 Azure Storage 서비스의 2013-08-15 버전이 완벽 하게 지원됩니다. (이전에 이 버전은 스토리지 에뮬레이터 버전 2.2.1 미리 보기에서만 지원되었습니다.)

## <a name="next-steps"></a>다음 단계

* 커뮤니티에서 유지 관리 되는 플랫폼 간 오픈 소스 저장소 에뮬레이터 [Azurite](https://github.com/azure/azurite)를 평가 합니다. 
* [.NET을 사용한 Azure Storage 샘플](../storage-samples-dotnet.md)에는 애플리케이션을 개발할 때 사용할 수 있는 몇 가지 코드 샘플에 대한 링크가 있습니다.
* [Microsoft Azure Storage 탐색기](https://storageexplorer.com) 를 사용 하 여 클라우드 저장소 계정 및 저장소 에뮬레이터에서 리소스 작업을 수행할 수 있습니다.

## <a name="see-also"></a>참고 항목

* [Azurite, Azure Sdk 및 Azure Storage 탐색기를 사용 하 여 로컬 Azure Storage 개발](https://blog.jongallant.com/2020/04/local-azure-storage-development-with-azurite-azuresdks-storage-explorer/)
