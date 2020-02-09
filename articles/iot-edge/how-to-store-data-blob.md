---
title: 디바이스에 블록 Blob 저장 - Azure IoT Edge | Microsoft Docs
description: 계층화 및 라이브 라이브 기능 이해를 참조 하십시오. 지원 되는 blob storage 작업을 참조 하 고 blob storage 계정에 연결 합니다.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bea00f429f31f2be62ee6a9c00f88873c595d94c
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509821"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>IoT Edge에서 Azure Blob Storage를 사용 하 여에 지에 데이터 저장

IoT Edge에서 Azure Blob Storage는 [블록 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 을 제공 하 고에 지에 blob Storage 솔루션을 [추가](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) 합니다. IoT Edge 장치의 blob storage 모듈은 blob이 IoT Edge 장치에 로컬로 저장 된다는 점을 제외 하 고 Azure blob service 처럼 동작 합니다. 이미 사용 하 고 있는 것과 동일한 Azure storage SDK 메서드 또는 blob API 호출을 사용 하 여 blob에 액세스할 수 있습니다. 이 문서에서는 IoT Edge 장치에서 Blob 서비스를 실행 하는 IoT Edge 컨테이너의 Azure Blob Storage 관련 된 개념을 설명 합니다.

이 모듈은 시나리오에서 유용 합니다.

* 데이터를 처리 하거나 클라우드로 전송할 수 있을 때까지 로컬로 저장 해야 하는 경우 이 데이터는 비디오, 이미지, 재무 데이터, 병원 데이터 또는 기타 구조화 되지 않은 데이터 일 수 있습니다.
* 장치가 연결을 제한 하는 위치에 있는 경우
* 데이터를 로컬에서 효율적으로 처리 하 여 데이터에 대 한 대기 시간이 짧고 emergencies에 최대한 빠르게 대응할 수 있도록 하려는 경우
* 대역폭 비용을 절감 하 고 tb의 데이터를 클라우드로 전송 하지 않으려는 경우 데이터를 로컬로 처리 하 고 처리 된 데이터만 클라우드에 보낼 수 있습니다.

비디오에서 빠른 소개 보기
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

이 모듈은 **deviceToCloudUpload** 및 **deviceautodelete** 기능과 함께 제공 됩니다.

**deviceToCloudUpload** 는 구성 가능한 기능입니다. 이 함수는 임시 인터넷 연결을 지원 하 여 로컬 blob 저장소에서 Azure로 데이터를 자동으로 업로드 합니다. 다음 작업을 수행할 수 있습니다.

* DeviceToCloudUpload 기능을 설정/해제 합니다.
* NewestFirst 또는 OldestFirst와 같이 Azure에 데이터를 복사 하는 순서를 선택 합니다.
* 데이터를 업로드 하려는 Azure Storage 계정을 지정 합니다.
* Azure에 업로드 하려는 컨테이너를 지정 합니다. 이 모듈을 사용 하 여 원본 및 대상 컨테이너 이름을 모두 지정할 수 있습니다.
* 클라우드 저장소에 업로드가 완료 된 후 blob을 즉시 삭제할 수 있는 기능을 선택 합니다.
* `Put Blob` 작업을 사용 하 여 전체 blob 업로드를 수행 하 고 `Put Block`, `Put Block List` 및 `Append Block` 작업을 사용 하 여 블록 수준 업로드를 수행 합니다.

이 모듈은 blob이 블록으로 구성 된 경우 블록 수준 업로드를 사용 합니다. 몇 가지 일반적인 시나리오는 다음과 같습니다.

* 응용 프로그램은 이전에 업로드 된 블록 blob의 일부 블록을 업데이트 하거나 추가 blob에 새 블록을 추가 합니다 .이 모듈은 전체 blob이 아닌 업데이트 된 블록만 업로드 합니다.
* 모듈이 blob을 업로드 하는 중 이며, 연결이 다시 되 면 전체 blob이 아닌 나머지 블록만 업로드 합니다.

Blob을 업로드 하는 동안 예기치 않은 프로세스 종료 (예: 전원 오류)가 발생 하면 모듈이 다시 온라인 상태가 되 면 업로드에 대 한 모든 블록이 다시 업로드 됩니다.

**Deviceautodelete** 는 구성 가능한 기능입니다. 이 함수는 지정 된 기간 (분 단위)이 만료 될 때 로컬 저장소에서 blob을 자동으로 삭제 합니다. 다음 작업을 수행할 수 있습니다.

* DeviceAutoDelete 기능을 설정/해제 합니다.
* Blob이 자동으로 삭제 되는 시간 (분)을 지정 합니다 (deleteAfterMinutes).
* DeleteAfterMinutes 값이 만료 되는 경우 업로드 하는 동안 blob을 유지 하는 기능을 선택 합니다.

## <a name="prerequisites"></a>필수 조건

Azure IoT Edge 디바이스:

* [Linux](quickstart-linux.md) 또는 [Windows 장치](quickstart.md)에 대 한 빠른 시작의 단계를 수행 하 여 개발 컴퓨터 또는 가상 컴퓨터를 IoT Edge 장치로 사용할 수 있습니다.

* 지원 되는 운영 체제 및 아키텍처 목록은 [지원 되는 Azure IoT Edge 시스템](support.md#operating-systems) 을 참조 하세요. IoT Edge 모듈의 Azure Blob Storage는 다음 아키텍처를 지원 합니다.
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (미리 보기)

클라우드 리소스:

Azure의 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload 및 deviceAutoDelete 속성

모듈의 desired 속성을 사용 하 여 **deviceToCloudUploadProperties** 및 **deviceautodeleteproperties**를 설정 합니다. 원하는 속성은 배포 중에 설정 하거나 나중에 다시 배포할 필요 없이 모듈 쌍을 편집 하 여 변경할 수 있습니다. `reported configuration`에 대 한 "모듈 쌍"을 확인 하 고 `configurationValidation` 값이 올바르게 전파 되는지 확인 하는 것이 좋습니다.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

이 설정의 이름은 `deviceToCloudUploadProperties`입니다. IoT Edge 시뮬레이터를 사용 하는 경우 이러한 속성에 대 한 관련 환경 변수로 값을 설정 합니다. 설명 섹션에서 찾을 수 있습니다.

| 속성 | 가능한 값 | 설명 |
| ----- | ----- | ---- |
| uploadOn | true, false | 기본적으로 `false`로 설정 합니다. 이 기능을 설정 하려면이 필드를 `true`설정 합니다. <br><br> 환경 변수: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | 데이터를 Azure로 복사 하는 순서를 선택할 수 있습니다. 기본적으로 `OldestFirst`로 설정 합니다. 순서는 Blob의 마지막 수정 시간에 따라 결정 됩니다. <br><br> 환경 변수: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`은 데이터를 업로드 하려는 저장소 계정을 지정할 수 있는 연결 문자열입니다. `Azure Storage Account Name`, `Azure Storage Account Key``End point suffix`를 지정 합니다. 데이터가 업로드 되는 Azure의 적절 한 EndpointSuffix를 추가 합니다 .이는 글로벌 Azure, 정부 Azure 및 Microsoft Azure Stack에 따라 달라 집니다. <br><br> 여기에서 Azure Storage SAS 연결 문자열을 지정 하도록 선택할 수 있습니다. 그러나 만료 될 때이 속성을 업데이트 해야 합니다. <br><br> 환경 변수: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Azure에 업로드 하려는 컨테이너 이름을 지정할 수 있습니다. 이 모듈을 사용 하 여 원본 및 대상 컨테이너 이름을 모두 지정할 수 있습니다. 대상 컨테이너 이름을 지정 하지 않으면 `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`으로 컨테이너 이름이 자동으로 할당 됩니다. 대상 컨테이너 이름에 대 한 템플릿 문자열을 만들고 가능한 값 열을 체크 아웃할 수 있습니다. <br>*% h-> IoT Hub 이름 (3-50 자)입니다. <br>*% d-> IoT Edge 장치 ID (1 ~ 129 자)입니다. <br>*% m-> 모듈 이름 (1 ~ 64 자)입니다. <br>*% c-> 원본 컨테이너 이름 (3 ~ 63 자) <br><br>컨테이너 이름의 최대 크기는 63 자입니다. 컨테이너 크기가 63 자를 초과 하는 경우 대상 컨테이너 이름을 자동으로 할당 하는 동안 각 섹션 (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName)은 15로 잘립니다. 자를. <br><br> 환경 변수: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | true, false | 기본적으로 `false`로 설정 합니다. `true`로 설정 된 경우 클라우드 저장소에 업로드가 완료 되 면 데이터가 자동으로 삭제 됩니다. <br><br> **주의**: 추가 blob을 사용 하는 경우이 설정은 성공적으로 업로드 한 후 로컬 저장소에서 추가 blob을 삭제 하 고 나중에 해당 blob에 대 한 추가 블록 작업이 실패 하 게 됩니다. 응용 프로그램이 드물게 추가 작업을 수행 하거나 연속 추가 작업을 지원 하지 않는 경우이 설정을 사용 하지 않도록 주의 하십시오.<br><br> 환경 변수: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`. |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

이 설정의 이름은 `deviceAutoDeleteProperties`입니다. IoT Edge 시뮬레이터를 사용 하는 경우 이러한 속성에 대 한 관련 환경 변수로 값을 설정 합니다. 설명 섹션에서 찾을 수 있습니다.

| 속성 | 가능한 값 | 설명 |
| ----- | ----- | ---- |
| deleteOn | true, false | 기본적으로 `false`로 설정 합니다. 이 기능을 설정 하려면이 필드를 `true`설정 합니다. <br><br> 환경 변수: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | 시간을 분 단위로 지정 합니다. 이 값이 만료 되 면 모듈에서 로컬 저장소의 blob을 자동으로 삭제 합니다. <br><br> 환경 변수: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | 기본적으로 `true`로 설정 되며, deleteAfterMinutes가 만료 되는 경우 클라우드 저장소에 업로드 하는 동안 blob이 유지 됩니다. `false`로 설정할 수 있으며, deleteAfterMinutes 만료 되는 즉시 데이터를 삭제 합니다. 참고:이 속성을 work로 설정 하려면 uploadOn를 true로 설정 해야 합니다.  <br><br> **주의**: 추가 blob을 사용 하는 경우이 설정은 값이 만료 될 때 로컬 저장소에서 추가 blob을 삭제 하 고 나중에 해당 blob에 대 한 추가 블록 작업이 실패 하 게 됩니다. 응용 프로그램에서 수행 하는 추가 작업의 예상 된 빈도로 만료 값이 충분 한지 확인 하는 것이 좋습니다.<br><br> 환경 변수: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>로컬 저장소로 SMB 공유 사용

Windows 호스트에서이 모듈의 Windows 컨테이너를 배포할 때 로컬 저장소 경로로 SMB 공유를 제공할 수 있습니다.

SMB 공유 및 IoT 장치가 상호 트러스트 된 도메인에 있는지 확인 합니다.

`New-SmbGlobalMapping` PowerShell 명령을 실행 하 여 Windows를 실행 하는 IoT 장치에서 로컬로 SMB 공유를 매핑할 수 있습니다.

구성 단계는 다음과 같습니다.

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

예:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

이 명령은 자격 증명을 사용 하 여 원격 SMB 서버에 인증 합니다. 그런 다음 원격 공유 경로를 G: 드라이브 문자에 매핑합니다(사용 가능한 다른 모든 드라이브 문자일 수 있음). 이제 IoT 장치에는 G: 드라이브의 경로에 매핑된 데이터 볼륨이 있습니다.

IoT 장치의 사용자가 원격 SMB 공유에 대 한 읽기/쓰기를 수행할 수 있는지 확인 합니다.

배포의 `<storage mount>` 값은 **G:/ContainerData: C:/BlobRoot**일 수 있습니다.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Linux에서 컨테이너 사용자에 게 디렉터리 액세스 권한 부여

Linux 컨테이너에 대 한 만들기 옵션에서 저장소에 [볼륨 탑재](https://docs.docker.com/storage/volumes/) 를 사용한 경우 추가 단계를 수행 하지 않아도 되지만, [bind 탑재](https://docs.docker.com/storage/bind-mounts/) 를 사용 하는 경우 이러한 단계는 서비스를 올바르게 실행 하는 데 필요 합니다.

최소 권한의 원칙에 따라 사용자가 작업을 수행 하는 데 필요한 최소 권한으로 사용자의 액세스 권한을 제한 합니다 .이 모듈에는 사용자 (이름: absie, ID: 11000) 및 사용자 그룹 (이름: absie, ID: 11000)이 포함 됩니다. 컨테이너가 **root** (기본 사용자 **루트**)로 시작 되는 경우 서비스는 낮은 권한 **absie** 사용자로 시작 됩니다.

이 동작은 서비스가 올바르게 작동 하는 데 매우 중요 한 호스트 경로 바인딩에 대 한 권한을 구성 합니다. 그렇지 않으면 서비스가 액세스 거부 오류로 인해 중단 됩니다. 디렉터리 바인딩에 사용 되는 경로는 컨테이너 사용자가 액세스할 수 있어야 합니다 (예: absie 11000). 호스트에서 아래 명령을 실행 하 여 컨테이너 사용자에 게 디렉터리에 대 한 액세스 권한을 부여할 수 있습니다.

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

예:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

**Absie**이외의 사용자로 서비스를 실행 해야 하는 경우 배포 매니페스트의 "user" 속성 아래에서 createoptions에 사용자 지정 사용자 ID를 지정할 수 있습니다. 이 경우 기본 또는 루트 그룹 ID `0`를 사용 해야 합니다.

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

이제 컨테이너 사용자에 게 디렉터리에 대 한 액세스 권한을 부여 합니다.

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>로그 파일 구성

모듈에 대 한 로그 파일을 구성 하는 방법에 대 한 자세한 내용은 이러한 [프로덕션 모범 사례](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)를 참조 하세요.

## <a name="connect-to-your-blob-storage-module"></a>Blob Storage 모듈에 연결

모듈에 대해 구성한 계정 이름 및 계정 키를 사용하여 IoT Edge 디바이스의 Blob Storage에 액세스할 수 있습니다.

만드는 스토리지 요청의 Blob 엔드포인트로 IoT Edge 디바이스를 지정합니다. 구성한 IoT Edge 디바이스 정보 및 계정 이름을 사용하여 [명시적 스토리지 엔드포인트에 대한 연결 문자열을 만들 수 있습니다](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint).

* IoT Edge 모듈의 Azure Blob Storage와 동일한 장치에 배포 된 모듈의 경우 Blob 엔드포인트은 `http://<module name>:11002/<account name>`입니다.
* 다른 장치에서 실행 되는 모듈 또는 응용 프로그램의 경우 네트워크에 적합 한 엔드포인트을 선택 해야 합니다. 네트워크 설정에 따라 외부 모듈이 나 응용 프로그램의 데이터 트래픽이 IoT Edge 모듈의 Azure Blob Storage를 실행 하는 장치에 도달할 수 있도록 엔드포인트 형식을 선택 합니다. 이 시나리오에 대 한 blob 엔드포인트은 다음 중 하나입니다.
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob Storage 빠른 시작 샘플

Azure Blob Storage 설명서에는 여러 언어의 빠른 시작 샘플 코드가 포함 되어 있습니다. 이러한 샘플을 실행 하면 blob 엔드포인트을 변경 하 여 로컬 blob Storage 모듈에 연결 하 여 IoT Edge에서 Azure Blob Storage를 테스트할 수 있습니다.

다음 퀵 스타트 샘플에서는 IoT Edge 에서도 지원 되는 언어를 사용 하므로 blob storage 모듈과 함께 IoT Edge 모듈로 배포할 수 있습니다.

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Python SDK의 V 2.1 이전 버전에는 모듈이 blob 생성 시간을 반환 하지 않는 알려진 문제가 있습니다. 이러한 문제로 인해 blob 나열과 같은 일부 메서드는 작동 하지 않습니다. 해결 방법으로 blob 클라이언트의 API 버전을 ' 2017-04-17 '로 명시적으로 설정 합니다. 예제: `block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Blob 추가 샘플](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.JS](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Go](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Azure Storage 탐색기를 사용 하 여 로컬 저장소에 연결

[Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) 를 사용 하 여 로컬 저장소 계정에 연결할 수 있습니다.

1. Azure Storage Explorer 다운로드 및 설치

1. 연결 문자열을 사용 하 여 Azure Storage에 연결

1. 연결 문자열 제공: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. 연결 단계를 진행 합니다.

1. 로컬 저장소 계정 내에 컨테이너 만들기

1. 블록 blob 또는 추가 Blob로 파일 업로드를 시작 합니다.
   > [!NOTE]
   > 이 모듈은 페이지 blob을 지원 하지 않습니다.

1. Storage 탐색기에서 Azure storage 계정을 연결 하도록 선택할 수 있습니다. 이 구성은 로컬 저장소 계정 및 Azure storage 계정 모두에 대 한 단일 보기를 제공 합니다.

## <a name="supported-storage-operations"></a>지원되는 스토리지 작업

IoT Edge의 blob storage 모듈은 Azure Storage Sdk를 사용 하며, 블록 blob 엔드포인트에 대 한 Azure Storage API의 2017-04-17 버전과 일치 합니다.

IoT Edge Azure Blob Storage에서 모든 Azure Blob Storage 작업을 지원 하지 않기 때문에이 섹션에는 각 작업의 상태가 나열 됩니다.

### <a name="account"></a>계정

지원됨:

* 컨테이너 나열

지원되지 않음:

* Blob 서비스 속성을 가져오고 설정
* 실행 전 Blob 요청
* Blob 서비스 통계 가져오기
* 계정 정보 가져오기

### <a name="containers"></a>컨테이너

지원됨:

* 컨테이너 만들기 및 삭제
* 컨테이너 속성 및 메타데이터 가져오기
* Blob 나열
* 컨테이너 ACL 가져오기 및 설정
* 컨테이너 메타데이터 설정

지원되지 않음:

* 컨테이너 임대

### <a name="blobs"></a>Blob

지원됨:

* Blob 배치, 가져오기 및 삭제
* Blob 속성 가져오기 및 설정
* Blob 메타데이터 가져오기 및 설정

지원되지 않음:

* Blob 임대
* Blob 스냅샷
* Blob 복사 및 중단
* Blob 삭제 취소
* Blob 계층 설정

### <a name="block-blobs"></a>블록 Blob

지원됨:

* 블록 배치
* 블록 목록 배치 및 가져오기

지원되지 않음:

* URL에서 블록 배치

### <a name="append-blobs"></a>추가 Blob

지원됨:

* 블록 추가

지원되지 않음:

* URL의 블록 추가

## <a name="event-grid-on-iot-edge-integration"></a>IoT Edge 통합 Event Grid

> [!CAUTION]
> IoT Edge에서 Event Grid와의 통합이 미리 보기 상태입니다.

IoT Edge 모듈의이 Azure Blob Storage은 이제 IoT Edge의 Event Grid와의 통합을 제공 합니다. 이 통합에 대 한 자세한 내용은 자습서를 참조 하 여 [모듈을 배포 하 고 이벤트를 게시 하 고 이벤트 배달을 확인](../event-grid/edge/react-blob-storage-events-locally.md)하세요.

## <a name="release-notes"></a>릴리스 정보

이 모듈에 대 한 [docker 허브의 릴리스 정보](https://hub.docker.com/_/microsoft-azure-blob-storage) 는 다음과 같습니다.

## <a name="feedback"></a>피드백

사용자 의견은이 모듈과 기능을 유용 하 고 사용 하기 쉽도록 하는 데 중요 합니다. 피드백을 공유 하 고 개선할 수 있는 방법을 알려주세요.

absiotfeedback@microsoft.com에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[IoT Edge에서 Azure Blob Storage를 배포](how-to-deploy-blob.md) 하는 방법을 알아봅니다.

[IoT Edge 블로그의 Azure Blob Storage](https://aka.ms/abs-iot-blogpost) 에서 최신 업데이트 및 공지를 최신 상태로 유지
