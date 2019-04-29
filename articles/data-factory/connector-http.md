---
title: Azure Data Factory를 사용하여 HTTP 원본에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 클라우드 또는 온-프레미스 HTTP 원본에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 87505081f16008dff7da1f567c1265c695f3f0ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653775"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 HTTP 엔드포인트에서 데이터 복사

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-http-connector.md)
> * [현재 버전](connector-http.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 HTTP 엔드포인트에서 데이터를 복사하는 방법을 간략히 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 기반으로 합니다.

HTTP 커넥터인 [REST 커넥터](connector-rest.md)와 [웹 테이블 커넥터](connector-web-table.md) 간의 차이점은 다음과 같습니다.

- **REST 커넥터**는 특히 RESTful API에서 데이터를 복사하는 것을 지원합니다. 
- **HTTP 커넥터**는 일반적으로 모든 HTTP 엔드포인트에서 데이터를 검색합니다(예: 파일 다운로드). REST 커넥터를 사용할 수 있게 되기 전에는 HTTP 커넥터를 사용하여 지원은 되지만 REST 커넥터와 비교할 때 기능이 적은 RESTful API에서 데이터를 복사할 수도 있습니다.
- **웹 테이블 커넥터**는 HTML 웹 페이지에서 테이블 콘텐츠를 추출합니다.

## <a name="supported-capabilities"></a>지원되는 기능

HTTP 원본에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

이 HTTP 커넥터를 사용하여 다음을 수행할 수 있습니다.

- HTTP **GET** 또는 **POST** 메서드를 사용하여 HTTP/S 엔드포인트에서 데이터를 검색합니다.
- 인증 방법으로 **Anonymous**, **Basic**, **Digest**, **Windows** 또는 **ClientCertificate** 중 하나를 사용하여 데이터를 검색합니다.
- HTTP 응답을 그대로 복사하거나 [지원되는 파일 형식과 압축 코덱](supported-file-formats-and-compression-codecs.md)을 사용하여 구문 분석합니다.

> [!TIP]
> Data Factory에서 HTTP 커넥터를 구성하기 전에 데이터 검색을 위한 HTTP 요청을 테스트하려면 헤더 및 본문 요구 사항의 API 사양을 알아봅니다. Postman 또는 웹 브라우저와 같은 도구를 사용하여 유효성을 검사할 수 있습니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 HTTP 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

HTTP 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | **type** 속성은 **HttpServer**로 설정해야 합니다. | 예 |
| URL | 웹 서버의 기본 URL입니다. | 예 |
| enableServerCertificateValidation | HTTP 엔드포인트에 연결할 때 서버 SSL 인증서 유효성 검사를 사용할지 지정합니다. HTTPS 서버에서 자체 서명된 인증서를 사용하는 경우 이 속성을 **false**로 설정합니다. | 아닙니다.<br /> (기본값: **true**) |
| authenticationType | 인증 유형을 지정합니다. 허용되는 값은 **Anonymous**, **Basic**, **Digest**, **Windows** 및 **ClientCertificate**입니다. <br><br> 이러한 인증 형식의 더 많은 속성 및 JSON 샘플은 이 표 뒤의 섹션을 참조하세요. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 데이터 저장소가 개인 네트워크에 있는 경우, 자체 호스팅 통합 런타임을 사용할 수 있습니다. 지정하지 않으면 이 속성은 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

### <a name="using-basic-digest-or-windows-authentication"></a>Basic, Digest 또는 Windows 인증 사용

**authenticationType** 속성을 **Basic**, **Digest** 또는 **Windows**로 설정합니다. 앞 섹션에서 설명한 일반 속성 외에 다음 속성을 지정합니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| userName | HTTP 엔드포인트에 액세스하는 데 사용할 사용자 이름입니다. | 예 |
| 암호 | 사용자(**userName** 값)의 암호입니다. 이 필드를 **SecureString** 형식으로 표시하여 Data Factory에서 안전하게 저장합니다. [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)할 수도 있습니다. | 예 |

**예제**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>ClientCertificate 인증 사용

ClientCertificate 인증을 사용하려면 **authenticationType** 속성을 **ClientCertificate**로 설정합니다. 앞 섹션에서 설명한 일반 속성 외에 다음 속성을 지정합니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| embeddedCertData | Base64로 인코딩된 인증서 데이터입니다. | **embeddedCertData** 또는 **certThumbprint**를 지정합니다. |
| certThumbprint | 자체 호스팅 통합 런타임 머신의 인증서 저장소에 설치된 인증서의 지문입니다. 자체 호스팅 형식의 통합 런타임이 **connectVia** 속성에 지정된 경우에만 적용됩니다. | **embeddedCertData** 또는 **certThumbprint**를 지정합니다. |
| 암호 | 인증서와 연결된 암호입니다. 이 필드를 **SecureString** 형식으로 표시하여 Data Factory에서 안전하게 저장합니다. [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)할 수도 있습니다. | 아닙니다. |

인증에 **certThumbprint**를 사용하고 인증서가 로컬 컴퓨터의 개인 저장소에 설치된 경우 자체 호스팅 통합 런타임에 읽기 권한을 부여합니다.

1. MMC(Microsoft Management Console)를 엽니다. **로컬 컴퓨터**를 대상으로 하는 **인증서** 스냅인을 추가합니다.
2. **인증서** > **개인**을 확장한 후 **인증서**를 선택합니다.
3. 개인 저장소에서 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업** > **개인 키 관리**를 선택합니다.
3. **보안** 탭에서 인증서에 대한 읽기 권한으로 통합 런타임 호스트 서비스(DIAHostService)를 실행 중인 사용자 계정을 추가합니다.

**예제 1: certThumbprint 사용**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제 2: embeddedCertData 사용**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

이 섹션에서는 HTTP 데이터 세트에서 지원하는 속성의 목록을 제공합니다. 

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요. 

HTTP에서 데이터를 복사하려면 데이터 세트의 **type** 속성을 **HttpFile**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 세트의 **type** 속성을 **HttpFile**로 설정해야 합니다. | 예 |
| relativeUrl | 데이터를 포함하는 리소스에 대한 상대 URL입니다. 이 속성을 지정하지 않으면 연결된 서비스 정의에 지정된 URL만 사용됩니다. | 아닙니다. |
| requestMethod | HTTP 메서드입니다. 허용되는 값은 **Get**(기본값) 또는 **Post**입니다. | 아닙니다. |
| additionalHeaders | 추가 HTTP 요청 헤더입니다. | 아닙니다. |
| requestBody | HTTP 요청의 본문입니다. | 아닙니다. |
| format | 데이터를 구문 분석하지 않고 HTTP 엔드포인트에서 데이터를 있는 그대로 검색하고 파일 기반 저장소에 복사하려면 입력 및 출력 데이터 세트 정의 모두에서 **형식** 섹션을 건너뜁니다.<br/><br/>복사 중에 HTTP 응답 콘텐츠를 구문 분석하려는 경우 지원되는 파일 형식 유형은 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** 및 **ParquetFormat** **형식**에서 **type** 속성을 이러한 값 중 하나로 설정합니다. 자세한 내용은 [JSON 형식](supported-file-formats-and-compression-codecs.md#json-format), [텍스트 형식](supported-file-formats-and-compression-codecs.md#text-format), [Avro 형식](supported-file-formats-and-compression-codecs.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs.md#parquet-format)을 참조하세요. |아닙니다. |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md#compression-support)을 참조하세요.<br/><br/>지원되는 형식: **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>지원되는 수준은  **최적** 및 **가장 빠름**입니다. |아닙니다. |

> [!NOTE]
> 지원되는 HTTP 요청 페이로드 크기는 약 500KB입니다. 웹 엔드포인트에 전달하려는 페이로드 크기가 500KB보다 큰 경우 더 작은 청크로 페이로드를 일괄 처리하는 것이 좋습니다.

**예제 1: Get 메서드(기본값) 사용**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**예제 2: POST 메서드 사용**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

이 섹션에서는 HTTP 원본에서 지원하는 속성의 목록을 제공합니다.

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 

### <a name="http-as-source"></a>HTTP를 원본으로

HTTP에서 데이터를 복사하려면 복사 작업의 **원본 형식**을 **HttpSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 **type** 속성을 **HttpSource**로 설정해야 합니다. | 예 |
| httpRequestTimeout | HTTP 요청이 응답을 받을 시간 제한(**TimeSpan** 값)입니다. 이 값은 응답 데이터를 읽는 시간 제한이 아니라, 응답을 받을 시간 제한입니다. 기본값은 **00:01:40**입니다.  | 아닙니다. |

**예제**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
