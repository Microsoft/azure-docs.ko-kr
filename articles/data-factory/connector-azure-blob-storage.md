---
title: Azure Blob storage에서 데이터 복사 및 변환
description: Data Factory를 사용 하 여 blob 저장소에서 데이터를 복사 하 고 Blob 저장소에서 데이터를 변환 하는 방법에 대해 알아봅니다.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/16/2020
ms.openlocfilehash: f245254c85cbf9223ab5dda60799c6b7ce72f11c
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76120216"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Azure Blob storage에서 데이터 복사 및 변환

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-azure-blob-connector.md)
> * [현재 버전](connector-azure-blob-storage.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 Azure Blob storage에서 데이터를 복사 하 고, 데이터 흐름을 사용 하 여 Azure Blob storage에서 데이터를 변환 하는 방법을 설명 합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Blob 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [작업 삭제](delete-activity.md)

복사 활동의 경우이 Blob storage 커넥터는 다음을 지원 합니다.

- 범용 Azure 스토리지 계정 및 핫/쿨 Blob Storage 간에 Blob을 복사합니다. 
- Azure 리소스 인증을 위해 계정 키, 서비스 공유 액세스 서명, 서비스 주체 또는 관리 ID를 사용하여 Blob을 복사합니다.
- 블록, 추가 또는 페이지 Blob에서 Blob을 복사하고, 블록 Blob에만 데이터를 복사합니다.
- Blob을 있는 그대로 복사하거나 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md)을 사용하여 Blob을 구문 분석하거나 생성합니다.
- [복사 중에 파일 메타 데이터를 유지](#preserve-metadata-during-copy)합니다.

>[!IMPORTANT]
>Azure Storage 방화벽 설정에서 **신뢰할 수 있는 Microsoft 서비스에서이 저장소 계정에 액세스 하도록 허용** 옵션을 사용 하도록 설정 하 고 Azure integration runtime을 사용 하 여 Blob Storage에 연결 하려면 [관리 되는 id 인증](#managed-identity)을 사용 해야 합니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Blob Storage에 특정한 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Blob 커넥터에서 지원하는 인증 유형은 다음과 같습니다. 자세한 내용은 해당 섹션을 참조하세요.

- [계정 키 인증](#account-key-authentication)
- [공유 액세스 서명 인증](#shared-access-signature-authentication)
- [서비스 주체 인증](#service-principal-authentication)
- [Azure 리소스 인증용 관리 ID](#managed-identity)

>[!NOTE]
>PolyBase를 사용 하 여 SQL Data Warehouse에 데이터를 로드 하는 경우 원본 또는 스테이징 Blob 저장소가 Virtual Network 엔드포인트으로 구성 된 경우 PolyBase에서 요구 하는 대로 관리 되는 id 인증을 사용 해야 하 고 버전과 함께 자체 호스팅 Integration Runtime를 사용 해야 합니다. 3.18 이상. 추가 구성 필수 구성 요소가 포함 된 [관리 되는 id 인증](#managed-identity) 섹션을 참조 하세요.

>[!NOTE]
>HDInsights 및 Azure Machine Learning 작업은 Azure Blob 저장소 계정 키 인증만 지원 합니다.

### <a name="account-key-authentication"></a>계정 키 인증

스토리지 계정 키 인증을 사용하는 데 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **AzureBlobStorage**(권장) 또는 **AzureStorage**로 설정해야 합니다(아래 참고 참조). |예 |
| connectionString | connectionString 속성에 대한 Storage에 연결하는 데 필요한 정보를 지정합니다. <br/> Azure Key Vault에 계정 키를 넣고, 연결 문자열에서 `accountKey` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

>[!NOTE]
>계정 키 인증을 사용 하는 경우 보조 Blob Service 엔드포인트이 지원 되지 않습니다. 다른 인증 유형을 사용할 수 있습니다.

>[!NOTE]
>"AzureStorage" 유형의 연결된 서비스를 사용하는 경우에도 있는 그대로 계속 지원되지만, 앞으로 이 새로운 "AzureBlobStorage" 연결된 서비스 유형을 사용하는 것이 좋습니다.

**예:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Azure Key Vault에 계정 키 저장**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>공유 액세스 서명 인증

공유 액세스 서명은 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 공유 액세스 서명을 사용하여 스토리지 계정의 개체에 대해 지정된 시간 동안 제한된 권한을 클라이언트에 부여할 수 있습니다. 계정 액세스 키를 공유할 필요가 없습니다. 공유 액세스 서명은 스토리지 리소스에 대해 인증된 액세스에 필요한 모든 정보를 쿼리 매개 변수에 포함하는 URI입니다. 공유 액세스 서명을 사용하여 스토리지 리소스에 액세스하려면 클라이언트에서 공유 액세스 서명을 해당 생성자 또는 메서드에 전달하기만 하면 됩니다. 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명: 공유 액세스 서명 모델 이해](../storage/common/storage-dotnet-shared-access-signature-part-1.md)를 참조하세요.

> [!NOTE]
>- Data Factory에서 이제 **서비스 공유 액세스 서명**과 **계정 공유 액세스 서명**이 모두 지원됩니다. 공유 액세스 서명에 대한 자세한 내용은 [SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대한 제한 된 액세스 권한 부여](../storage/common/storage-sas-overview.md)를 참조 하세요.
>- 이후 데이터 세트 구성에서 폴더 경로는 컨테이너 수준에서 시작하는 절대 경로입니다. SAS URI의 경로와 일치하는 경로를 구성해야 합니다.

> [!TIP]
> 스토리지 계정에 대한 서비스 공유 액세스 서명을 생성하려면 다음 PowerShell 명령을 실행합니다. 자리 표시자를 바꾸고 필요한 권한을 부여합니다.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

공유 액세스 서명 인증을 사용하는 데 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **AzureBlobStorage**(권장) 또는 **AzureStorage**로 설정해야 합니다(아래 참고 참조). |예 |
| sasUri | 스토리지 리소스(예: Blob/컨테이너)에 대한 공유 액세스 서명 URI를 지정합니다. <br/>이 필드를 SecureString으로 표시하여 Data Factory에서 안전하게 저장합니다. SAS 토큰을 Azure Key Vault에 배치 하 여 자동 회전을 활용 하 고 토큰 부분을 제거할 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

>[!NOTE]
>"AzureStorage" 유형의 연결된 서비스를 사용하는 경우에도 있는 그대로 계속 지원되지만, 앞으로 이 새로운 "AzureBlobStorage" 연결된 서비스 유형을 사용하는 것이 좋습니다.

**예:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Azure Key Vault에 계정 키 저장**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

공유 액세스 서명 URI를 만들 때 고려해야 할 사항은 다음과 같습니다.

- 데이터 팩터리에서 연결된 서비스(읽기, 쓰기, 읽기/쓰기)를 사용하는 방법에 따라 개체에 대한 적절한 읽기/쓰기 권한을 설정합니다.
- **만료 시간**을 적절하게 설정합니다. Storage 개체에 대한 액세스가 파이프라인의 활성 기간 내에 만료되지 않도록 합니다.
- URI는 필요에 따라 적절한 컨테이너/Blob에 만들어야 합니다. Blob에 대한 공유 액세스 서명 URI를 사용하면 Data Factory에서 해당 특정 Blob에 액세스할 수 있습니다. Blob 스토리지 컨테이너에 대한 공유 액세스 서명 URI를 사용하면 Data Factory에서 해당 컨테이너의 Blob을 반복할 수 있습니다. 나중에 더 많거나 적은 개체에 대한 액세스를 제공하거나 공유 액세스 서명 URI를 업데이트하려면 연결된 서비스를 새 URI로 업데이트해야 합니다.

### <a name="service-principal-authentication"></a>서비스 주체 인증

Azure Storage 서비스 주체 인증은 일반적으로 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증](../storage/common/storage-auth-aad.md)을 참조하세요.

서비스 주체 인증을 사용하려면 다음 단계를 수행합니다.

1. [Azure AD 테넌트에 애플리케이션 등록](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)에 따라 Azure AD(Azure Active Directory)에 애플리케이션 항목을 등록합니다. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. Azure Blob Storage에서 서비스 주체에게 적절한 권한을 부여합니다. 역할에 대한 자세한 내용은 [RBAC를 사용하여 Azure Storage 데이터에 대한 액세스 권한 관리](../storage/common/storage-auth-aad-rbac.md)를 참조하세요.

    - **원본으로**의 액세스 제어(IAM)에서 최소한 **Storage Blob 데이터 읽기 권한자** 역할을 부여합니다.
    - **싱크의 경우** 액세스 제어(IAM)에서 **Storage Blob 데이터 기여자** 역할 이상을 부여합니다.

Azure Blob Storage 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **AzureBlobStorage**로 설정해야 합니다. |예 |
| serviceEndpoint | 패턴이 `https://<accountName>.blob.core.windows.net/`인 Azure Blob Storage 서비스 엔드포인트를 지정합니다. |예 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString**으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리를 마우스로 가리켜 검색합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

>[!NOTE]
>서비스 주체 인증은 "AzureBlobStorage" 유형 연결된 서비스에서만 지원되고, 이전의 "AzureStorage" 유형 연결된 서비스에서는 지원되지 않습니다.

**예:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Azure 리소스 인증용 관리 ID

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. 사용자 고유의 서비스 주체를 사용 하는 것과 비슷한 방식으로 Blob storage 인증에이 관리 되는 id를 사용할 수 있습니다. 이렇게 하면 이 지정된 팩터리에서 Blob Storage 간에 데이터에 액세스하고 복사할 수 있습니다.

일반적으로 Azure Storage 인증을 위해 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증](../storage/common/storage-auth-aad.md)을 참조하세요. Azure 리소스 인증을 위해 관리 ID를 사용하려면 다음 단계를 따릅니다.

1. 팩터리와 함께 생성 된 **관리 id 개체 ID** 의 값을 복사 하 여 [데이터 팩터리 관리 Id 정보를 검색](data-factory-service-identity.md#retrieve-managed-identity) 합니다.

2. Azure Blob Storage에서 적절한 사용 권한을 관리 ID에 부여합니다. 역할에 대한 자세한 내용은 [RBAC를 사용하여 Azure Storage 데이터에 대한 액세스 권한 관리](../storage/common/storage-auth-aad-rbac.md)를 참조하세요.

    - **원본으로**의 액세스 제어(IAM)에서 최소한 **Storage Blob 데이터 읽기 권한자** 역할을 부여합니다.
    - **싱크의 경우** 액세스 제어(IAM)에서 **Storage Blob 데이터 기여자** 역할 이상을 부여합니다.

>[!IMPORTANT]
>PolyBase를 사용 하 여 Blob (원본 또는 스테이징)에서 SQL Data Warehouse로 데이터를 로드 하는 경우 Blob에 대한 관리 되는 id 인증을 사용 하는 경우 [이 가이드](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 의 1 단계와 2 단계를 수행 해야 합니다. 또한 SQL Database 서버를 Azure Active Directory (Azure AD)에 등록 하 고 2) 저장소 Blob 데이터 참가자 역할을 SQL Database 서버에 할당 합니다. 나머지는 Data Factory에 의해 처리 됩니다. Blob 저장소가 Azure Virtual Network 엔드포인트을 사용 하 여 구성 된 경우 PolyBase를 사용 하 여 데이터를 로드 하려면 PolyBase에서 요구 하는 대로 관리 되는 id 인증을 사용 해야 합니다.

Azure Blob Storage 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **AzureBlobStorage**로 설정해야 합니다. |예 |
| serviceEndpoint | 패턴이 `https://<accountName>.blob.core.windows.net/`인 Azure Blob Storage 서비스 엔드포인트를 지정합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

> [!NOTE]
> Azure 리소스 인증을 위한 관리 ID는 "AzureBlobStorage" 유형 연결된 서비스에서만 지원되고, 이전의 "AzureStorage" 유형 연결된 서비스에서는 지원되지 않습니다. 

**예:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

형식 기반 데이터 집합의 `location` 설정에서 Azure Blob에 대해 지원 되는 속성은 다음과 같습니다.

| 속성   | Description                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 데이터 집합에 있는 위치의 type 속성은 **Azureblobstoragelocation**으로 설정 해야 합니다. | 예      |
| container  | Blob 컨테이너입니다.                                          | 예      |
| folderPath | 지정 된 컨테이너 아래에 있는 폴더의 경로입니다. 와일드 카드를 사용 하 여 폴더를 필터링 하려면이 설정을 건너뛰고 작업 원본 설정에서를 지정 합니다. | 아닙니다.       |
| fileName   | 지정 된 컨테이너 + folderPath 아래의 파일 이름입니다. 와일드 카드를 사용 하 여 파일을 필터링 하려는 경우이 설정을 건너뛰고 작업 원본 설정에서를 지정 합니다. | 아닙니다.       |

**예:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Blob Storage 원본 및 싱크에서 지원되는 속성 목록을 제공합니다.

### <a name="blob-storage-as-a-source-type"></a>Blob Storage를 원본 형식으로

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

다음 속성은 형식 기반 복사 원본의 `storeSettings` 설정에서 Azure Blob에 대해 지원 됩니다.

| 속성                 | Description                                                  | 필수                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 아래의 type 속성은 **Azureblobstoragereadsettings**로 설정 되어야 합니다. | 예                                           |
| recursive                | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. recursive를 true로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 만들어지지 않습니다. 허용되는 값은 **true**(기본값) 및 **false**입니다. | 아닙니다.                                            |
| wildcardFolderPath       | 원본 폴더를 필터링 하기 위해 데이터 집합에 구성 된 지정 된 컨테이너 아래에 와일드 카드 문자가 있는 폴더 경로입니다. <br>허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. <br>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 아닙니다.                                            |
| wildcardFileName         | 소스 파일을 필터링 하는 지정 된 컨테이너 + folderPath/wildcardFolderPath의 와일드 카드 문자를 포함 하는 파일 이름입니다. <br>허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다.  더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | `fileName` 데이터 집합에 지정 되지 않은 경우에는 예입니다. |
| modifiedDatetimeStart    | 특성을 기반으로 하는 파일 필터: 마지막으로 수정한 날짜입니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br> 속성은 NULL일 수 있습니다. 이 경우 파일 특성 필터가 데이터 세트에 적용되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다. | 아닙니다.                                            |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 아닙니다.                                            |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아닙니다.                                            |

> [!NOTE]
> Parquet/구분 된 텍스트 형식의 경우 다음 섹션에 언급 된 **blobsource** 형식 복사 작업 원본은 이전 버전과의 호환성을 위해 그대로 계속 지원 됩니다. 앞으로이 새 모델을 사용 하 고 ADF 제작 UI가 이러한 새 유형을 생성 하도록 전환 하는 것이 좋습니다.

**예:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>Blob Storage를 싱크 형식으로

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

형식 기반 복사 싱크의 `storeSettings` 설정에서 Azure Blob에 대해 지원 되는 속성은 다음과 같습니다.

| 속성                 | Description                                                  | 필수 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 아래의 type 속성은 **Azureblobstoragewritesettings**로 설정 해야 합니다. | 예      |
| copyBehavior             | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있게 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 병합되는 파일 이름은 지정된 파일 또는 Blob 이름이 적용됩니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 아닙니다.       |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아닙니다.       |

**예:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>폴더 및 파일 필터 예제

이 섹션에서는 와일드카드 필터가 있는 폴더 경로 및 파일 이름의 결과 동작에 대해 설명합니다.

| folderPath | fileName | recursive | 원본 폴더 구조 및 필터 결과(**굵게** 표시된 파일이 검색됨)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (비어 있음, 기본값 사용) | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (비어 있음, 기본값 사용) | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>일부 recursive 및 copyBehavior 예제

이 섹션에서는 다양한 recursive 및 copyBehavior 값 조합에 대한 복사 작업의 결과 동작을 설명합니다.

| recursive | copyBehavior | 원본 폴더 구조 | 결과 대상 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같이 원본 폴더와 동일한 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1, File2, File3, File4 및 File5의 내용이 자동 생성된 파일 이름이 있는 하나의 파일로 병합됩니다. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1과 File2의 내용이 자동 생성된 파일 이름이 있는 하나의 파일로 병합됩니다. File1에 대해 자동 생성된 이름<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |

## <a name="preserve-metadata-during-copy"></a>복사 하는 동안 메타 데이터 유지

Amazon S3/Azure Blob/Azure Data Lake Storage Gen2에서 Azure Data Lake Storage Gen2/Azure Blob으로 파일을 복사 하는 경우 데이터와 함께 파일 메타 데이터를 유지 하도록 선택할 수 있습니다. [메타 데이터 유지](copy-activity-preserve-metadata.md#preserve-metadata)에서 자세히 알아보세요.

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

매핑 데이터 흐름에서 데이터를 변환할 때 JSON, Avro, 구분 기호로 분리 된 텍스트 또는 Parquet 형식으로 Azure Blob Storage에서 파일을 읽고 쓸 수 있습니다. 자세한 내용은 데이터 흐름 매핑 기능에서 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md) 을 참조 하세요.

### <a name="source-transformation"></a>원본 변환

원본 변환에서 Azure Blob Storage의 컨테이너, 폴더 또는 개별 파일을 읽을 수 있습니다. **원본 옵션** 탭에서 파일을 읽는 방법을 관리할 수 있습니다. 

![원본 옵션](media/data-flow/sourceOptions1.png "원본 옵션")

**와일드 카드 경로:** 와일드 카드 패턴을 사용 하면 ADF가 일치 하는 각 폴더와 파일을 단일 원본 변환에서 반복 하도록 지시할 수 있습니다. 단일 흐름 내에서 여러 파일을 처리 하는 효과적인 방법입니다. 기존 와일드 카드 패턴을 가리킬 때 표시 되는 + 기호를 사용 하 여 와일드 카드 일치 패턴을 여러 개 추가 합니다.

소스 컨테이너에서 패턴과 일치 하는 일련의 파일을 선택 합니다. 데이터 집합에는 컨테이너만 지정할 수 있습니다. 따라서 와일드 카드 경로에도 루트 폴더의 폴더 경로가 포함 되어야 합니다.

와일드 카드 예제:

* ```*```는 모든 문자 집합을 나타냅니다.
* 재귀 디렉터리 중첩을 나타내는 ```**```
* ```?``` 한 문자를 대체 합니다.
* ```[]```는 대괄호 안에 있는 하나 이상의 문자를 찾습니다.

* />/> 아래에 있는 모든 csv 파일을 ```/data/sales/**/*.csv``` 가져옵니다.
* ```/data/sales/20??/**``` 20 세기의 모든 파일을 가져옵니다.
* 12 월 2004에 X 또는 Y 접두사가 2 자리 숫자로 시작 하는 모든 csv 파일을 ```/data/sales/2004/*/12/[XY]1?.csv``` 가져옵니다.

**파티션 루트 경로:** 파일 원본에 ```key=value``` 형식으로 분할 된 폴더가 있는 경우 (예: year = 2019) 해당 파티션 폴더 트리의 최상위 수준을 데이터 흐름 데이터 스트림의 열 이름에 할당할 수 있습니다.

먼저 분할 된 폴더와 읽으려는 리프 파일의 모든 경로를 포함 하도록 와일드 카드를 설정 합니다.

![파티션 원본 파일 설정](media/data-flow/partfile2.png "파티션 파일 설정")

파티션 루트 경로 설정을 사용 하 여 폴더 구조의 최상위 수준을 정의 합니다. 데이터 미리 보기를 통해 데이터의 내용을 볼 때 ADF가 각 폴더 수준에 있는 확인 된 파티션을 추가 하는 것을 볼 수 있습니다.

![파티션 루트 경로](media/data-flow/partfile1.png "파티션 루트 경로 미리 보기")

**파일 목록:** 이 파일은 파일 집합입니다. 처리할 상대 경로 파일 목록이 포함 된 텍스트 파일을 만듭니다. 이 텍스트 파일을 가리킵니다.

**저장할 열 파일 이름:** 원본 파일의 이름을 데이터의 열에 저장 합니다. 여기에 새 열 이름을 입력 하 여 파일 이름 문자열을 저장 합니다.

**완료 후:** 데이터 흐름이 실행 된 후 원본 파일을 사용 하 여 아무 작업도 수행 하지 않도록 선택 하거나 원본 파일을 삭제 하거나 원본 파일을 이동 합니다. 이동 경로는 상대 경로입니다.

원본 파일을 다른 위치로 처리 후에 이동 하려면 먼저 파일 작업에 대해 "이동"을 선택 합니다. 그런 다음 "from" 디렉터리를 설정 합니다. 경로에 와일드 카드를 사용 하지 않는 경우 "보낸 사용자" 설정은 원본 폴더와 같은 폴더에 있습니다.

와일드 카드가 있는 원본 경로를 사용 하는 경우 구문은 다음과 같습니다.

```/data/sales/20??/**/*.csv```

다음으로 "시작"을 지정할 수 있습니다.

```/data/sales```

및 "to"로

```/backup/priorSales```

이 경우/cv/ps 아래에서 소스인 모든 파일이/backup/priorSales.로 이동 됩니다.

> [!NOTE]
> 파이프라인에서 데이터 흐름 실행 작업을 사용 하는 파이프라인 실행 (파이프라인 디버그 또는 실행 실행)에서 데이터 흐름을 시작 하는 경우에만 파일 작업이 실행 됩니다. 파일 작업은 데이터 흐름 디버그 모드에서 실행 *되지* 않습니다.

**마지막으로 수정한 사람 필터링:** 마지막으로 수정 된 날짜 범위를 지정 하 여 처리 하는 파일을 필터링 할 수 있습니다. 모든 날짜/시간은 UTC 시간입니다. 

### <a name="sink-properties"></a>싱크 속성

싱크 변환에서 Azure Blob Storage의 컨테이너 또는 폴더에 쓸 수 있습니다. **설정** 탭에서 파일을 작성 하는 방법을 관리할 수 있습니다.

![싱크 옵션](media/data-flow/file-sink-settings.png "싱크 옵션")

**폴더 지우기:** 데이터를 쓰기 전에 대상 폴더를 지우도록 할지 여부를 결정 합니다.

**파일 이름 옵션:** 대상 폴더에서 대상 파일의 이름을 지정 하는 방법을 결정 합니다. 파일 이름 옵션은 다음과 같습니다.
   * **기본값**: SPARK에서 파트 기본값을 기준으로 파일의 이름을 지정할 수 있습니다.
   * **패턴**: 파티션당 출력 파일을 열거 하는 패턴을 입력 합니다. 예를 들어, **대출 [n] .csv** 는 loans1, loans2 등을 만듭니다.
   * **파티션당**: 파티션당 하나의 파일 이름을 입력 합니다.
   * **열의 데이터로**출력: 출력 파일을 열의 값으로 설정 합니다. 경로는 대상 폴더가 아니라 데이터 집합 컨테이너를 기준으로 합니다.
   * **단일 파일로 출력**: 분할 된 출력 파일을 단일 명명 된 파일로 결합 합니다. 경로는 데이터 집합 폴더에 대한 상대 경로입니다. Te merge 작업은 노드 크기에 따라 실패할 수 있습니다. 이 옵션은 대량 데이터 집합에는 권장 되지 않습니다.

**모두 인용:** 모든 값을 따옴표로 묶을 지 여부를 결정 합니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 활동 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 활동](control-flow-get-metadata-activity.md) 을 확인 하세요. 

## <a name="delete-activity-properties"></a>작업 속성 삭제

속성에 대한 자세한 내용을 보려면 [삭제 작업](delete-activity.md) 을 선택 합니다.

## <a name="legacy-models"></a>레거시 모델

>[!NOTE]
>다음 모델은 이전 버전과의 호환성을 위해 그대로 계속 지원 됩니다. 앞의 섹션에서 설명한 새 모델을 사용 하는 것이 좋습니다. 그러면 ADF 제작 UI가 새 모델을 생성 하도록 전환 됩니다.

### <a name="legacy-dataset-model"></a>레거시 데이터 집합 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성은 **AzureBlob**으로 설정해야 합니다. |예 |
| folderPath | Blob Storage에서 컨테이너 및 폴더에 대한 경로입니다. <br/><br/>와일드카드 필터가 컨테이너 이름을 제외한 경로에 지원됩니다. 허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. <br/><br/>예: myblobcontainer/myblobfolder/(더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples) 참조) |Copy/Lookup 활동의 경우 예, GetMetadata 활동의 경우 아니요 |
| fileName | 지정된 "folderPath" 아래의 Blob에 대한 **이름 또는 와일드 카드 필터**입니다. 이 속성의 값을 지정하지 않으면 데이터 세트는 폴더에 있는 모든 Blob을 가리킵니다. <br/><br/>필터에 허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>`^`을 사용하여 실제 파일 이름 내에 와일드카드 또는 이 이스케이프 문자가 있는 경우 이스케이프합니다.<br/><br/>FileName이 출력 데이터 집합에 대해 지정 되지 않고 **preserveHierarchy** 가 활동 싱크에 지정 되지 않은 경우 복사 작업은 다음 패턴으로 blob 이름을 자동으로 생성 합니다. "*Data. [ 작업 실행 ID입니다. [GUID if FlattenHierarchy]입니다. [구성 된 경우 형식]. [압축이 구성 된 경우]* "(예:" 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. release.tar.gz ";) 쿼리 대신 테이블 이름을 사용 하 여 테이블 형식 원본에서 복사 하는 경우 이름 패턴은 " *[table name]. [ format]. [압축이 구성 된 경우]* "(예:" MyTable "). |아닙니다. |
| modifiedDatetimeStart | 특성을 기반으로 하는 파일 필터: 마지막으로 수정한 날짜입니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 많은 양의 파일에서 파일을 필터링 하려는 경우이 설정을 사용 하면 데이터 이동의 전반적인 성능에 영향을 줄 수 있습니다. <br/><br/> 속성은 데이터 집합에 적용 되는 파일 특성 필터가 없음을 의미 하는 NULL 일 수 있습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.| 아닙니다. |
| modifiedDatetimeEnd | 특성을 기반으로 하는 파일 필터: 마지막으로 수정한 날짜입니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 많은 양의 파일에서 파일을 필터링 하려는 경우이 설정을 사용 하면 데이터 이동의 전반적인 성능에 영향을 줄 수 있습니다. <br/><br/> 속성은 데이터 집합에 적용 되는 파일 특성 필터가 없음을 의미 하는 NULL 일 수 있습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.| 아닙니다. |
| format | 파일 기반 저장소(이진 복사본) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 format 섹션을 건너뜁니다.<br/><br/>특정 형식으로 파일을 구문 분석하거나 생성하려면 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** 및 **ParquetFormat** 파일 형식 유형이 지원됩니다. **format**의 **type** 속성을 이 값 중 하나로 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON 형식](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs-legacy.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs-legacy.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>**Optimal** 및 **Fastest** 수준이 지원됩니다. |아닙니다. |

>[!TIP]
>폴더 아래에서 모든 Blob을 복사하려면 **folderPath**만을 지정합니다.<br>지정된 이름의 단일 Blob을 복사하려면 폴더 부분으로 **folderPath** 및 파일 이름으로 **fileName**을 지정합니다.<br>폴더 아래에서 Blob의 하위 집합을 복사하려면 폴더 부분으로 **folderPath** 및 와일드카드 필터로 **fileName**을 지정합니다. 

**예:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>레거시 복사 활동 원본 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 source의 type 속성은 **BlobSource**로 설정해야 합니다. |예 |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. recursive를 true로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 만들어지지 않습니다.<br/>허용되는 값은 **true**(기본값) 및 **false**입니다. | 아닙니다. |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아닙니다. |

**예:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>레거시 복사 활동 싱크 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 sink의 type 속성은 **BlobSink**로 설정해야 합니다. |예 |
| copyBehavior | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있게 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 병합되는 파일 이름은 지정된 파일 또는 Blob 이름이 적용됩니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 아닙니다. |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아닙니다. |

**예:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>다음 단계

Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
