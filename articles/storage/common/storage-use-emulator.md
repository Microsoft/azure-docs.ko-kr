---
title: 개발 및 테스트에 Azure Storage 에뮬레이터 사용 | Microsoft Docs
description: Azure Storage 에뮬레이터는 Azure Storage 애플리케이션에 대한 개발 및 테스트에 대해 무료 로컬 개발 환경을 제공합니다. 요청을 인증하는 방법, 애플리케이션에서 에뮬레이터에 연결하는 방법 및 명령줄 도구를 사용하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/10/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: bc78dade345c9f9c72cf936136d9969b79bc1398
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125527"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>개발 및 테스트에 Azure Storage 에뮬레이터 사용

Microsoft Azure 저장소 에뮬레이터는 개발 목적으로 Azure Blob, 큐 및 Table service를 에뮬레이트하는 로컬 환경을 제공합니다. 저장소 에뮬레이터를 사용하면 Azure 구독을 구입하거나 비용을 발생시키지 않고도 로컬에서 저장소 서비스에 대해 애플리케이션을 테스트할 수 있습니다. 에뮬레이터에서 애플리케이션이 작동하는 방식에 만족하는 경우 Azure 스토리지 계정을 클라우드에서 사용하도록 전환할 수 있습니다.

## <a name="get-the-storage-emulator"></a>저장소 에뮬레이터 가져오기
저장소 에뮬레이터는 [Microsoft Azure SDK](https://azure.microsoft.com/downloads/)의 일부로 제공됩니다. [독립 실행형 설치 관리자](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409)(직접 다운로드)를 사용하여 저장소 에뮬레이터를 설치할 수도 있습니다. 저장소 에뮬레이터를 설치하려면 컴퓨터에 대한 관리자 권한이 있어야 합니다.

저장소 에뮬레이터는 현재 Windows에서만 실행됩니다. Linux용 저장소 에뮬레이터를 고려 중인 경우 커뮤니티에서 유지 관리하는 오픈 소스 저장소 에뮬레이터 [Azurite](https://github.com/azure/azurite)가 하나의 옵션이 될 수 있습니다.

> [!NOTE]
> 저장소 에뮬레이터의 어느 한 버전으로 작성한 데이터에 다른 버전으로 반드시 액세스할 수 있는 것은 아닙니다. 장기간 보존하려는 데이터는 스토리지 에뮬레이터가 아닌 Azure Storage 계정에 저장하는 것이 좋습니다.
> 
> 저장소 에뮬레이터는 OData 라이브러리의 특정 버전에 따라 달라집니다. 저장소 에뮬레이터에서 사용되는 OData DLL을 다른 버전으로 바꾸는 것은 지원되지 않으며 예기치 않은 동작을 초래합니다. 그러나 저장소 서비스가 지원하는 어떤 버전의 OData도 에뮬레이터에 요청을 보내는 데 사용할 수 있습니다.

## <a name="how-the-storage-emulator-works"></a>저장소 에뮬레이터의 작동 원리
스토리지 에뮬레이터는 로컬 Microsoft SQL Server 인스턴스와 로컬 파일 시스템을 사용하여 Azure Storage 서비스를 에뮬레이트합니다. 기본적으로 저장소 에뮬레이터는 Microsoft SQL Server 2012 Express LocalDB의 데이터베이스를 사용합니다. 저장소 에뮬레이터가 LocalDB가 아닌 SQL Server의 로컬 인스턴스에 액세스하도록 구성할 수 있습니다. 자세한 내용은 이 문서 다음에 나오는 [저장소 에뮬레이터 시작 및 초기화](#start-and-initialize-the-storage-emulator) 섹션을 참조하세요.

저장소 에뮬레이터는 Windows 인증을 사용하여 SQL Server 또는 LocalDB에 연결합니다.

저장소 에뮬레이터와 Azure 저장소 서비스 간에는 기능에 몇 가지 차이점이 있습니다. 이러한 차이점에 대한 자세한 내용은 이 문서 다음에 나오는 [스토리지 에뮬레이터와 Azure Storage의 차이점](#differences-between-the-storage-emulator-and-azure-storage) 섹션을 참조하세요.

## <a name="start-and-initialize-the-storage-emulator"></a>저장소 에뮬레이터 시작 및 초기화

Azure Storage 에뮬레이터를 시작하려면:
1. **시작** 단추를 선택하거나 **Windows** 키를 누릅니다.
2. `Azure Storage Emulator`를 입력하여 시작합니다.
3. 표시된 애플리케이션 목록에서 에뮬레이터를 선택합니다.

저장소 에뮬레이터가 시작되면 명령 프롬프트 창이 표시됩니다. 이 콘솔 창을 사용하여 저장소 에뮬레이터를 시작/중지하고, 데이터를 지우고, 상태를 가져오고, 에뮬레이터를 초기화할 수 있습니다. 자세한 내용은 이 문서 뒷부분에 나오는 [저장소 에뮬레이터 명령줄 도구 참조](#storage-emulator-command-line-tool-reference) 섹션을 참조하세요.

에뮬레이터를 실행 하는 경우 Windows 작업 표시줄 알림 영역에 아이콘이 표시 됩니다.

저장소 에뮬레이터 명령 프롬프트 창을 닫으면 저장소 에뮬레이터가 계속 실행됩니다. 저장소 에뮬레이터 콘솔 창을 다시 불러오려면 저장소 에뮬레이터를 시작하는 것처럼 위의 단계를 수행합니다.

저장소 에뮬레이터를 처음 실행하면 로컬 저장소 환경이 자동으로 초기화됩니다. 초기화 프로세스에서는 LocalDB에 데이터베이스가 작성되며 각 로컬 저장소 서비스용으로 HTTP 포트가 예약됩니다.

저장소 에뮬레이터는 기본적으로 `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`에 설치됩니다.

> [!TIP]
> [Microsoft Azure Storage 탐색기](https://storageexplorer.com)를 사용하여 로컬 스토리지 에뮬레이터 리소스를 사용할 수 있습니다. 스토리지 에뮬레이터를 설치 및 시작한 다음 스토리지 탐색기 리소스 트리의 "Storage 계정"에서 "(개발)"을 찾습니다.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>다른 SQL 데이터베이스를 사용하여 저장소 에뮬레이터를 초기화 합니다.

저장소 에뮬레이터 명령줄 도구를 사용하여 기본 LocalDB 인스턴스가 아닌 SQL 데이터베이스 인스턴스를 가리키도록 저장소 에뮬레이터를 초기화할 수 있습니다.

1. [저장소 에뮬레이터 시작 및 초기화](#start-and-initialize-the-storage-emulator) 섹션에서 설명한대로 저장소 에뮬레이터 콘솔 창을 엽니다.
1. 콘솔 창에 다음 명령을 입력합니다. 여기서 `<SQLServerInstance>`은 SQL Server 인스턴스의 이름입니다. LocalDB를 사용하려면 SQL Server 인스턴스로 `(localdb)\MSSQLLocalDb`을 지정합니다.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   기본 SQL Server 인스턴스를 사용하도록 에뮬레이터에 지시하는 다음 명령을 사용할 수도 있습니다.

   `AzureStorageEmulator.exe init /server .`

   또는 LocalDB 인스턴스를 다시 초기화하는 다음 명령을 사용할 수도 있습니다.

   `AzureStorageEmulator.exe init /forceCreate`

이 명령에 대한 자세한 내용은 [저장소 에뮬레이터 명령줄 도구 참조](#storage-emulator-command-line-tool-reference)를 참조하세요.

> [!TIP]
> LocalDB 설치를 포함하여 SQL Server 인스턴스를 관리하는 데 SSMS([Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms))를 사용할 수 있습니다. SMSS **서버에 연결** 대화 상자에서 **서버 이름:** 필드에 `(localdb)\MSSQLLocalDb`을 지정하여 LocalDB 인스턴스에 연결합니다.

## <a name="authenticating-requests-against-the-storage-emulator"></a>저장소 에뮬레이터에 대한 인증 요청
저장소 에뮬레이터를 설치하고 시작하면 코드를 테스트할 수 있습니다. 클라우드의 Azure Storage와 마찬가지로, 익명 요청이 아닌 이상 스토리지 에뮬레이터에 대해 만드는 모든 요청을 인증해야 합니다. 공유 키 인증 또는 SAS(공유 액세스 서명)을 사용하여 저장소 에뮬레이터에 대한 요청을 인증할 수 있습니다.

### <a name="authorize-with-shared-key-credentials"></a>공유 키 자격 증명 인증
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

연결 문자열에 대한 자세한 내용은 [Azure Storage 연결 문자열 구성](../storage-configure-connection-string.md)을 참조하세요.

### <a name="authorize-with-a-shared-access-signature"></a>공유 액세스 서명을 사용하여 인증

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Xamarin 라이브러리와 같은 일부 Azure 저장소 클라이언트 라이브러리는 공유 액세스 서명 (SAS) 토큰 인증만 지원합니다. [저장소 탐색기](https://storageexplorer.com/) 또는 공유 키 인증을 지원하는 다른 애플리케이션과 같은 도구를 사용하여 SAS 토큰을 만들 수 있습니다.

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

공유 액세스 서명에 대한 자세한 내용은 [Azure Storage에서 SAS(공유 액세스 서명) 사용](../storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

## <a name="addressing-resources-in-the-storage-emulator"></a>저장소 에뮬레이터에서 리소스의 주소 지정
저장소 에뮬레이터에 대한 서비스 엔드포인트는 Azure 저장소 계정의 엔드포인트와 다릅니다. 이 차이는 로컬 컴퓨터가 도메인 이름 확인을 수행하지 않기 때문이며, 따라서 저장소 에뮬레이터 엔드포인트는 로컬 주소여야 합니다.

Azure Storage 계정에 리소스 주소를 지정할 때는 다음 체계를 사용합니다. 계정 이름은 URI 호스트 이름의 일부이고 주소를 지정하는 리소스는 URI 경로의 일부입니다.

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

예를 들어, 다음 URI는 Azure 저장소 계정의 blob에 대한 올바른 주소입니다.

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

그러나 저장소 에뮬레이터에서는 로컬 컴퓨터가 도메인 이름 확인을 수행하지 않기 때문에 계정 이름은 호스트 이름이 아닌 URI의 일부입니다. 저장소 에뮬레이터의 리소스에 대해 다음 URI 형식을 사용합니다.

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

예를 들어 저장소 에뮬레이터의 Blob에 액세스하려는 경우 다음 주소를 사용할 수 있습니다.

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

저장소 에뮬레이터에 대한 서비스 엔드포인트는 다음과 같습니다.

* Blob service: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* 큐 서비스: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Table service: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>RA-GRS를 사용하여 보조 계정 주소 지정
버전 3.1부터는 저장소 에뮬레이터에 대해 RA-GRS(읽기 권한 지역 중복 복제)를 사용할 수 있습니다. 클라우드와 로컬 에뮬레이터의 저장소 리소스에 대해 계정 이름에 -secondary를 추가하면 보조 위치에 액세스할 수 있습니다. 예를 들어 저장소 에뮬레이터에서 읽기 전용 보조 계정을 사용하여 Blob에 액세스하려는 경우 다음 주소를 사용할 수 있습니다.

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> 스토리지 에뮬레이터를 사용하여 프로그래밍 방식으로 보조 계정에 액세스하려는 경우 .NET용 Storage 클라이언트 라이브러리 버전 3.2 이상을 사용합니다. 자세한 내용은 [.NET용 Microsoft Azure Storage 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/dn261237.aspx)를 참조하세요.
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>저장소 에뮬레이터 명령줄 도구 참조
버전 3.0부터 저장소 에뮬레이터를 시작할 때 콘솔 창이 표시됩니다. 콘솔 창에서 명령줄을 사용하여 에뮬레이터를 시작/중지할 수 있으며 상태를 쿼리하고 기타 작업을 수행할 수도 있습니다.

> [!NOTE]
> Microsoft Azure 컴퓨팅 에뮬레이터가 설치되어 있으면 스토리지 에뮬레이터를 시작할 때 시스템 트레이 아이콘이 표시됩니다. 해당 아이콘을 마우스 오른쪽 단추로 클릭하면 표시되는 메뉴에서 그래픽 방식으로 저장소 에뮬레이터를 시작하고 중지할 수 있습니다.
>
>

### <a name="command-line-syntax"></a>명령줄 구문
`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>옵션
옵션 목록을 보려면 명령 프롬프트에 `/help` 을(를) 입력합니다.

| 옵션 | 설명 | 명령 | 인수 |
| --- | --- | --- | --- |
| **시작** |저장소 에뮬레이터를 시작합니다. |`AzureStorageEmulator.exe start [-inprocess]` |*-inprocess*: 새 프로세스를 만드는 대신 현재 프로세스에서 에뮬레이터를 시작합니다. |
| **중지** |저장소 에뮬레이터를 중지합니다. |`AzureStorageEmulator.exe stop` | |
| **상태** |저장소 에뮬레이터의 상태를 인쇄합니다. |`AzureStorageEmulator.exe status` | |
| **지우기** |명령줄에 지정된 모든 서비스의 데이터를 지웁니다. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob*: blob 데이터를 지웁니다. <br/>*queue*: 큐 데이터를 지웁니다. <br/>*table*: 테이블 데이터를 지웁니다. <br/>*all*: 모든 서비스의 모든 데이터를 지웁니다. |
| **Init** |에뮬레이터를 설정하기 위해 하는 일회 초기화를 수행 합니다. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*: SQL 인스턴스를 호스트하는 서버를 지정합니다. <br/>*-sqlinstance instanceName*: 기본 서버 인스턴스에서 사용할 SQL 인스턴스의 이름을 지정합니다. <br/>*-forcecreate*: 이미 존재하는 경우라도 SQL 데이터베이스를 강제로 생성합니다. <br/>*-skipcreate*: SQL 데이터베이스 만들기를 건너뜁니다. 이 옵션은 -forcecreate보다 우선합니다.<br/>*-reserveports*: 서비스와 연결된 HTTP 포트를 예약하려고 시도합니다.<br/>*-unreserveports*: 서비스와 연결된 HTTP 포트에 대한 예약을 제거하려고 합니다. 이 옵션은 -reserveports보다 우선합니다.<br/>*-inprocess*: 새 프로세스를 생성하는 대신 현재 프로세스의 초기화를 수행합니다. 포트 예약을 변경할 경우 관리자 권한으로 현재 프로세스를 시작해야 합니다. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>스토리지 에뮬레이터와 Azure Storage의 차이점
스토리지 에뮬레이터는 로컬 SQL 인스턴스를 실행하는 에뮬레이트된 환경이기 때문에 클라우드에서 에뮬레이터와 Azure Storage 계정 간에 기능에 차이가 있습니다.

* 저장소 에뮬레이터는 단일 고정 계정과 알려진 인증 키만 지원합니다.
* 저장소 에뮬레이터는 확장 가능한 저장소 서비스가 아니며 많은 수의 동시 클라이언트를 지원하지 않습니다.
* [저장소 에뮬레이터에서 리소스의 주소 지정](#addressing-resources-in-the-storage-emulator)에 설명한 대로, 저장소 에뮬레이터 및 Azure 저장소 계정에서 리소스의 주소가 서로 다르게 지정됩니다. 이러한 차이는 도메인 이름 확인이 클라우드에서는 가능하지만 로컬 컴퓨터에서는 불가능하기 때문에 발생합니다.
* 버전 3.1부터는 저장소 에뮬레이터 계정에 대해 RA-GRS(읽기 권한 지역 중복 복제)를 사용할 수 있습니다. 에뮬레이터에서 모든 계정은 RA-GRS 활성화 되어 있으며 기본 및 보조 복제본 간에 지연되지 않습니다. Blob 서비스 통계 가져오기, 큐 서비스 통계 가져오기 및 테이블 서비스 통계 가져오기 작업은 보조 계정에서 지원되며 기본 SQL 데이터베이스에 따라 현재 시간으로 `LastSyncTime` 응답 요소의 값을 항상 반환합니다.
* 파일 서비스 및 SMB 프로토콜 서비스 엔드포인트는 저장소 에뮬레이터에서 현재 지원되지 않습니다.
* 에뮬레이터 버전에서 지원되지 않는 저장소 서비스 버전을 사용하는 경우 저장소 에뮬레이트에서 VersionNotSupportedByEmulator 오류(HTTP 상태 코드 400 - 잘못된 요청)가 반환됩니다.

### <a name="differences-for-blob-storage"></a>Blob Storage의 차이점
다음과 같은 차이점이 에뮬레이터의 Blob Storage에 적용됩니다.

* 저장소 에뮬레이터는 blob 크기를 최대 2GB까지만 지원합니다.
* 스토리지 에뮬레이터에서 blob 이름의 최대 길이는 256자인 반면, Azure Storage에서 blob 이름의 최대 길이는 1024자입니다.
* 증분 복사를 사용하면 덮어쓴 Blob의 스냅숏을 복사할 수 있으며 이로 인해 서비스에 오류가 반환됩니다.
* Get Page Ranges Diff는 Incremental Copy Blob을 사용하여 복사한 스냅숏 간에는 작동하지 않습니다.
* 임대 ID가 요청에 지정되어 있지 않은 경우에도 Put Blob 작업은 저장소 에뮬레이터에 있으며 활성 임대가 있는 blob에 대해 성공할 수 있습니다.
* 추가 Blob 작업은 에뮬레이터에서 지원되지 않습니다. 추가 Blob에 대한 작업을 시도하면 FeatureNotSupportedByEmulator 오류(HTTP 상태 코드 400 - 잘못된 요청)가 반환됩니다.

### <a name="differences-for-table-storage"></a>Table Storage의 차이점
다음과 같은 차이점이 에뮬레이터의 Table Storage에 적용됩니다.

* 저장소 에뮬레이터에서 Table service의 날짜 속성은 SQL Server 2005에서 지원되는 범위만 지원합니다. 날짜 속성은 1753년 1월 1일 이후여야 합니다. 1753년 1월 1일 이전의 모든 날짜는 이 값으로 변경됩니다. 날짜의 정밀도는 SQL Server 2005의 정밀도로 제한되며, 즉 날짜의 정밀도는 1/300 초입니다.
* 저장소 에뮬레이터는 각각 512 바이트 미만의 파티션 키 및 행 키 속성 값을 지원합니다. 또한 계정 이름, 테이블 이름 및 키 속성 이름의 총 크기는 900 바이트를 초과할 수 없습니다.
* 저장소 에뮬레이터의 테이블에 있는 행의 총 크기는 1MB 미만으로 제한 합니다.
* 저장소 에뮬레이터에서 데이터 형식 `Edm.Guid` 또는 `Edm.Binary`의 속성은 쿼리 필터 문자열의 `Equal (eq)` 및 `NotEqual (ne)` 비교 연산자만 지원합니다.

### <a name="differences-for-queue-storage"></a>Queue Storage의 차이점
에뮬레이터에서 Queue Storage에 특정 차이점이 있습니다.

## <a name="storage-emulator-release-notes"></a>저장소 에뮬레이터 릴리스 정보

### <a name="version-57"></a>버전 5.7
로깅을 사용하도록 설정한 경우 충돌을 야기하는 버그가 수정되었습니다.

### <a name="version-56"></a>버전 5.6
* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 엔드포인트에서 2018-03-28 버전의 저장소 서비스를 지원합니다.

### <a name="version-55"></a>버전 5.5
* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 엔드포인트에서 2017-11-09 버전의 저장소 서비스를 지원합니다.
* BLOB 생성 시간을 반환하는 BLOB **Created** 속성에 대한 지원이 추가되었습니다.

### <a name="version-54"></a>버전 5.4
설치 안정성을 향상하기 위해, 에뮬레이터가 더 이상 설치 중에 포트를 예약하려고 시도하지 않습니다. 포트 예약을 원하는 경우 **init** 명령의 *-reserveports* 옵션을 사용하여 지정하세요.

### <a name="version-53"></a>버전 5.3
저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 엔드포인트에서 2017-07-29 버전의 저장소 서비스를 지원합니다.

### <a name="version-52"></a>버전 5.2
* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 엔드포인트에서 2017-04-17 버전의 저장소 서비스를 지원합니다.
* 테이블 속성 값이 제대로 인코딩되지 않는 버그를 수정했습니다.

### <a name="version-51"></a>버전 5.1
서비스가 `DataServiceVersion` 헤더를 반환하지 않았던 일부 응답에서 저장소 에뮬레이터가 이 헤더를 반환하는 버그를 수정했습니다.

### <a name="version-50"></a>버전 5.0
* 저장소 에뮬레이터 설치 관리자가 더 이상 기존 MSSQL 및 .NET Framework 설치를 확인하지 않습니다.
* 저장소 에뮬레이터 설치 관리자가 더 이상 설치 시 데이터베이스를 만들지 않습니다. 필요한 경우 시작 시 데이터베이스가 만들어집니다.
* 데이터베이스를 만들 때 더 이상 관리자 권한이 필요하지 않습니다.
* 시작 시 더 이상 포트 예약이 필요하지 않습니다.
* `init`에 `-reserveports`(권한 상승 필요), `-unreserveports`(권한 상승 필요), `-skipcreate` 옵션을 추가합니다.
* 이제 시스템 트레이 아이콘에서 저장소 에뮬레이터 UI 옵션을 선택하면 명령줄 인터페이스가 시작됩니다. 이전 GUI는 더 이상 사용할 수 없습니다.
* 일부 DLL이 제거되거나 이름이 바뀌었습니다.

### <a name="version-46"></a>버전 4.6
* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 엔드포인트에서 2016-05-31 버전의 저장소 서비스를 지원합니다.

### <a name="version-45"></a>버전 4.5
* 백업 데이터베이스의 이름이 변경되었을 때 저장소 에뮬레이터의 초기화 및 설치가 실패하는 버그가 수정되었습니다.

### <a name="version-44"></a>버전 4.4
* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 엔드포인트에서 2015-12-11 버전의 저장소 서비스를 지원합니다.
* 저장소 에뮬레이터의 blob 데이터 가비지 수집은 이제 많은 수의 blob 처리할 때 더 효율적입니다.
* 컨테이너 ACL XML이 저장소 서비스의 방법과는 약간 다르게 유효성 검사가 이뤄지게 하던 버그를 수정했습니다.
* 때로 최대 및 최소 DateTime 값이 잘못된 표준 시간대에 보고되도록 하던 버그를 수정했습니다.

### <a name="version-43"></a>버전 4.3
* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 엔드포인트에서 2015-07-08 버전의 저장소 서비스를 지원합니다.

### <a name="version-42"></a>버전 4.2
* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 엔드포인트에서 2015-04-05 버전의 저장소 서비스를 지원합니다.

### <a name="version-41"></a>버전 4.1
* 저장소 에뮬레이터는 이제 새로운 추가 Blob 기능을 제외하고 Blob, 큐 및 Table service 엔드포인트에서 2015-02-21 버전의 저장소 서비스를 지원합니다.
* 에뮬레이터에서 지원되지 않는 저장소 서비스 버전을 사용하는 경우 에뮬레이트에서 의미 있는 오류 메시지가 반환됩니다. 최신 버전의 에뮬레이터를 사용하는 것이 좋습니다. VersionNotSupportedByEmulator 오류(HTTP 상태 코드 400 - 잘못된 요청)가 발생하는 경우 최신 버전의 저장소 에뮬레이터를 다운로드하세요.
* 경합 조건으로 인해 동시 병합 작업 중 잘못된 테이블 엔터티 데이터가 발생하는 버그가 수정되었습니다.

### <a name="version-40"></a>버전 4.0
* 스토리지 에뮬레이터 실행 파일의 이름이 *AzureStorageEmulator.exe*로 바뀌었습니다.

### <a name="version-32"></a>버전 3.2
* 저장소 에뮬레이터는 이제 Blob, 큐 및 Table service 엔드포인트에서 2014-02-14 버전의 저장소 서비스를 지원합니다. 파일 서비스 엔드포인트는 저장소 에뮬레이터에서 현재 지원되지 않습니다. 2014-02-14 버전에 대한 자세한 내용은 [Azure Storage 서비스에 대한 버전 관리](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 를 참조하세요.

### <a name="version-31"></a>버전 3.1
* 읽기 액세스 지역 중복 저장소 (RA-GRS)는 이제 저장소 에뮬레이터에서 지원됩니다. Blob 서비스 통계 가져오기, 큐 서비스 통계 가져오기 및 테이블 서비스 통계 가져오기 API는 보조 계정에서 지원되며 기본 SQL 데이터베이스에 따라 현재 시간으로 LastSync 시간 응답 요소의 값을 항상 반환합니다. 스토리지 에뮬레이터를 사용하여 프로그래밍 방식으로 보조 계정에 액세스하려는 경우 .NET용 Storage 클라이언트 라이브러리 버전 3.2 이상을 사용합니다. 자세한 내용은 .NET용 Microsoft Azure Storage 클라이언트 라이브러리 참조를 참조하세요.

### <a name="version-30"></a>버전 3.0
* Azure 스토리지 에뮬레이터는 컴퓨팅 에뮬레이터와 같은 패키지에 더 이상 제공되지 않습니다.
* 저장소 에뮬레이터 그래픽 사용자 인터페이스는 더 이상 사용되지 않으며, 대신 스크립트 가능한 명령줄 인터페이스가 사용됩니다. 명령줄 인터페이스에 대한 자세한 내용은 저장소 에뮬레이터 명령줄 도구 참조를 참조하세요. 그래픽 인터페이스는 버전 3.0에 계속 있지만 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 스토리지 에뮬레이터 UI 표시 보기를 선택하여 Compute 에뮬레이터를 설치할 때에만 액세스 할 수 있습니다.
* 이제 Azure 저장소 서비스의 2013-08-15 버전이 완벽 하게 지원됩니다. (이전에 이 버전은 저장소 에뮬레이터 버전 2.2.1 미리 보기에서만 지원되었습니다.)

## <a name="next-steps"></a>다음 단계

* 커뮤니티에서 유지 관리하는 플랫폼 간 오픈 소스 저장소 에뮬레이터 [Azurite](https://github.com/arafato/azurite)를 평가합니다. 
* [.NET을 사용한 Azure Storage 샘플](../storage-samples-dotnet.md)에는 애플리케이션을 개발할 때 사용할 수 있는 몇 가지 코드 샘플에 대한 링크가 있습니다.
* [Microsoft Azure Storage 탐색기](https://storageexplorer.com)를 사용하여 클라우드 스토리지 계정 및 스토리지 에뮬레이터의 리소스를 사용할 수 있습니다.
