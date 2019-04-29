---
title: Azure Data Factory를 사용하여 SFTP 서버에서 데이터 이동 | Microsoft Docs
description: Azure Data Factory를 사용하여 온-프레미스 또는 클라우드 SFTP 서버에서 데이터를 이동하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fe253feca6a22ee0177082e178f897c5b634bb3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61257208"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Azure Data Factory를 사용하여 SFTP 서버에서 데이터 이동
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](data-factory-sftp-connector.md)
> * [버전 2(현재 버전)](../connector-sftp.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 SFTP 커넥터](../connector-sftp.md)를 참조하세요.

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 온-프레미스/클라우드 SFTP 서버의 데이터를 지원되는 싱크 데이터 저장소로 이동하는 방법에 대해 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소를 원본/싱크로 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

현재 데이터 팩터리는 SFTP 서버에서 다른 데이터 저장소로의 데이터 이동만 지원하며, 다른 데이터 저장소에서 SFTP 서버로의 데이터 이동은 지원하지 않습니다. 온-프레미스 및 클라우드 SFTP 서버를 지원합니다.

> [!NOTE]
> 복사 작업 시 원본 파일이 대상에 성공적으로 복사된 후 원본 파일이 삭제되지 않습니다. 성공적 복사 후 원본 파일을 삭제해야 할 경우 파일을 삭제하는 사용자 지정 작업을 만들고 파이프라인에서 해당 작업을 사용합니다.

## <a name="supported-scenarios-and-authentication-types"></a>지원되는 시나리오 및 인증 형식
이 SFTP 커넥터를 사용하여 **클라우드 SFTP 서버와 온-프레미스 SFTP 서버**의 데이터를 복사할 수 있습니다. SFTP 서버에 연결할 때 **기본** 및 **SshPublicKey** 인증 유형이 지원됩니다.

온-프레미스 SFTP 서버에서 데이터를 복사할 때 온-프레미스 환경/Azure VM에서 데이터 관리 게이트웨이를 설치해야 합니다. 게이트웨이에 대한 자세한 내용은 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md)를 참조하세요. 게이트웨이 설정 및 사용에 대한 단계별 지침은 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요.

## <a name="getting-started"></a>시작
다른 도구/API를 사용하여 SFTP 원본의 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다.

- 파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사**를 사용하는 것입니다. 단계별 지침은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습을 볼 수 있습니다.

- 또한 다음 도구를 사용하여 파이프라인을 만들 수 있습니다. **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API** 및 **REST API** 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요. SFTP 서버에서 Azure Blob Storage로 데이터를 복사하는 JSON 샘플은 이 문서의 [JSON 예: SFTP 서버에서 Azure Blob으로 데이터 복사](#json-example-copy-data-from-sftp-server-to-azure-blob) 섹션을 참조하세요.

## <a name="linked-service-properties"></a>연결된 서비스 속성
다음 테이블은 FTP 연결 서비스에 특정한 JSON 요소에 대한 설명을 제공합니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| 형식 | type 속성을 `Sftp`로 설정해야 합니다. |예 |
| host | SFTP 서버의 이름 또는 IP 주소입니다. |예 |
| 포트 |SFTP 서버가 수신하는 포트입니다. 기본값은 다음과 같습니다. 21 |아닙니다. |
| authenticationType |인증 유형을 지정합니다. 허용되는 값은 다음과 같습니다. **Basic**, **SshPublicKey**. <br><br> 더 많은 속성 및 각 속성의 JSON 샘플은 [기본 인증 사용](#using-basic-authentication) 및 [SSH 공개 키 인증 사용](#using-ssh-public-key-authentication) 섹션을 참조하세요. |예 |
| skipHostKeyValidation | 호스트 키 유효성 검사를 건너뛸지 여부를 지정합니다. | 아니요. 기본값: false |
| hostKeyFingerprint | 호스트 키의 지문을 지정합니다. | `skipHostKeyValidation`이 false로 지정되면 필수입니다.  |
| gatewayName |온-프레미스 SFTP 서버에 연결하기 위한 데이터 관리 게이트웨이의 이름입니다. | 온-프레미스 SFTP 서버에서 데이터를 복사하는 경우에는 필수입니다. |
| encryptedCredential | SFTP 서버 액세스를 위한 암호화된 자격 증명입니다. 복사 마법사 또는 ClickOnce 팝업 대화 상자에서 기본 인증(사용자 이름 + 암호) 또는 SshPublicKey 인증(사용자 이름 + 개인 키 경로 또는 콘텐츠)를 지정할 때 자정으로 생성됩니다. | 아니요. 온-프레미스 SFTP 서버에서 데이터를 복사하는 경우에만 적용됩니다. |

### <a name="using-basic-authentication"></a>기본 인증 사용

기본 인증을 사용하려면 `authenticationType`을 `Basic`으로 설정하고, 마지막 섹션에서 소개한 SFTP 커넥터 일반 속성 외에 다음 속성을 지정합니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| 사용자 이름 | SFTP 서버에 액세스하는 사용자. |예 |
| 암호 | 사용자(사용자 이름) 암호. | 예 |

#### <a name="example-basic-authentication"></a>예제: 기본 인증
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>예제: 암호화된 자격 증명으로 기본 인증

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>SSH 공개 키 인증 사용

SSH 공개 키 인증을 사용하려면 `authenticationType`을 `SshPublicKey`으로 설정하고, 마지막 섹션에서 소개한 SFTP 커넥터 일반 속성 외에 다음 속성을 지정합니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| 사용자 이름 |SFTP 서버에 액세스하는 사용자 |예 |
| privateKeyPath | 게이트웨이에서 액세스할 수 있는 개인 키 파일의 절대 경로를 지정합니다. | `privateKeyPath` 또는 `privateKeyContent`를 지정합니다. <br><br> 온-프레미스 SFTP 서버에서 데이터를 복사하는 경우에만 적용됩니다. |
| privateKeyContent | 개인 키 콘텐츠의 직렬화된 문자열입니다. 복사 마법사는 개인 키 파일을 읽고 개인 키 콘텐츠를 자동으로 추출할 수 있습니다. 다른 도구/SDK를 사용하는 경우 privateKeyPath 속성을 대신 사용합니다. | `privateKeyPath` 또는 `privateKeyContent`를 지정합니다. |
| passPhrase | 키 파일이 암호문으로 보호되는 경우 개인 키를 해독하는 암호문/암호를 지정합니다. | 개인 키 파일이 암호문으로 보호되는 경우에는 필수입니다. |

> [!NOTE]
> SFTP 커넥터는 RSA/DSA OpenSSH 키를 지원합니다. 키 파일 콘텐츠는 "-----BEGIN [RSA/DSA] PRIVATE KEY-----"로 시작되어야 합니다. 개인 키 파일이 ppk 형식 파일인 경우 Putty 도구를 사용하여 .ppk를 OpenSSH 형식으로 변환합니다.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>예제: 개인 키 filePath를 사용한 SshPublicKey 인증

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>예제: 개인 키 콘텐츠를 사용한 SshPublicKey 인증

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성
데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 세트의 정책과 같은 섹션이 모든 데이터 세트 형식에 대해 유사합니다.

**typeProperties** 섹션은 데이터 세트의 각 형식마다 다릅니다. 데이터 세트 형식에 대한 정보를 제공합니다. **FileShare** 데이터 세트 형식의 데이터 세트에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| folderPath |폴더에 대한 하위 경로. 문자열의 특수 문자에 이스케이프 문자 '\'를 사용합니다. 예제를 살펴보려면 연결된 서비스 및 데이터 세트 정의 샘플을 참조하세요.<br/><br/>이 속성을 **partitionBy**와 결합하여 조각 시작/종료 날짜/시간을 기준으로 폴더 경로를 지정할 수 있습니다. |예 |
| fileName |폴더에서 특정 파일을 참조하기 위해 테이블을 사용하려는 경우 **folderPath** 에 있는 파일의 이름을 지정합니다. 이 속성에 값을 지정하지 않으면 테이블은 폴더에 있는 모든 파일을 가리킵니다.<br/><br/>출력 데이터 세트에 대한 fileName이 지정되는 경우 생성되는 파일의 이름 형식은 다음과 같습니다. <br/><br/>`Data.<Guid>.txt`(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |아닙니다. |
| fileFilter |모든 파일이 아닌 folderPath의 파일 하위 집합을 선택하는데 사용할 필터를 지정합니다.<br/><br/>허용 되는 값은 `*`(여러 문자) 및 `?`(하나의 문자)입니다.<br/><br/>예 1: `"fileFilter": "*.log"`<br/>예 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter는 FileShare 입력 데이터 세트에 적용할 수 있습니다. 이 속성은 HDFS에는 지원되지 않습니다. |아닙니다. |
| partitionedBy |동적 folderPath, 시계열 데이터에 대한 filename을 지정하는 데 partitionedBy를 사용할 수 있습니다. 예를 들어 매시간 데이터에 대한 매개 변수가 있는 folderPath입니다. |아닙니다. |
| format | 다음 포맷 형식이 지원됩니다. **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**입니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [Json 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사**하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다. |아닙니다. |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다. 지원되는 수준은 **최적** 및 **가장 빠름**입니다. 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |아닙니다. |
| useBinaryTransfer |이전 전송 모드를 사용할지 여부를 지정합니다. 이진 모드인 경우 true이고 ASCII인 경우 false입니다. 기본값: True 이 속성은 연결된 서비스 유형이 다음과 같은 경우에만 사용할 수 있습니다. FtpServer. |아닙니다. |

> [!NOTE]
> filename 및 fileFilter는 동시에 사용할 수 없습니다.

### <a name="using-partionedby-property"></a>partionedBy 속성 사용
이전 섹션에서 설명했듯이 동적 folderPath, partitionedBy된 시계열 데이터에 대한 filename을 지정할 수 있습니다. 지정된 데이터 조각에 대한 논리적 기간을 나타내는 데이터 팩터리 매크로 및 시스템 변수 SliceStart, SliceEnd를 사용하여 이 작업을 수행할 수 있습니다.

시계열 데이터 세트, 예약 및 조각에 대한 자세한 내용은 [데이터 세트 만들기](data-factory-create-datasets.md), [일정 예약 및 실행](data-factory-scheduling-and-execution.md) 및 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요.

#### <a name="sample-1"></a>샘플 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
이 예제에서 {Slice}는 지정된 형식(YYYYMMDDHH)의 Data Factory 시스템 변수 SliceStart 값으로 대체됩니다. SliceStart는 조각의 시작 시간을 가리킵니다. folderPath는 각 조각에 따라 다릅니다. 예를 들어 wikidatagateway/wikisampledataout/2014100103 또는 wikidatagateway/wikisampledataout/2014100104입니다.

#### <a name="sample-2"></a>샘플 2:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
이 예제에서 SliceStart의 연도, 월, 일 및 시간은 folderPath 및 fileName 속성에서 사용하는 별도 변수로 추출됩니다.

## <a name="copy-activity-properties"></a>복사 작업 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 유형에 따라 달라집니다. 복사 활동의 경우 유형은 소스 및 싱크의 형식에 따라 달라집니다.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>지원되는 파일 및 압축 형식
자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md) 문서를 참조하세요.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON 예제: SFTP 서버에서 Azure Blob으로 데이터 복사
다음 예제에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. SFTP 원본에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure 데이터 팩터리의 복사 작업을 사용하여 임의의 원본에서 **여기**에 설명한 싱크로 [직접](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 데이터를 복사할 수 있습니다.

> [!IMPORTANT]
> 이 샘플은 JSON 코드 조각을 제공합니다. 데이터 팩터리를 만들기 위한 단계별 지침은 포함하지 않습니다. 단계별 지침은 [온-프레미스 위치와 클라우드 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요.

이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

* [sftp](#linked-service-properties) 형식의 연결된 서비스
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
* [FileShare](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
* [FileSystemSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 1시간마다 SFTP 서버의 데이터를 Azure Blob으로 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**SFTP 연결된 서비스**

이 예에서는 일반 텍스트의 사용자 이름과 암호를 기본 인증으로 사용합니다. 다음 방법 중 하나를 사용할 수도 있습니다.

* 암호화된 자격 증명으로 기본 인증 
* SSH 공개 키 인증

사용할 수 있는 다른 유형의 인증은 [FTP 연결 서비스](#linked-service-properties) 섹션을 참조하세요.

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Azure Storage 연결된 서비스**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**SFTP 입력 데이터 세트**

이 데이터 세트는 SFTP 폴더 `mysharedfolder`와 `test.csv` 파일을 참조합니다. 파이프라인은 파일을 대상에 복사합니다.

"external": "true" 설정은 데이터 세트가 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실을 Data Factory 서비스에 전달합니다.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Azure Blob 출력 데이터 세트**

데이터는 1시간마다 새 blob에 기록됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**복사 작업을 포함하는 파이프라인**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **FileSystemSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

* [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .
