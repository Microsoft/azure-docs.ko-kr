---
title: Azure Blob Storage에서 데이터 복사 및 변환
description: Data Factory를 사용하여 Blob Storage 간에 데이터를 복사하고 Blob Storage에서 데이터를 변환하는 방법에 대해 알아봅니다.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/31/2020
ms.openlocfilehash: 34ddea1445ef8a8eb8554add3ee8920078a6e573
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182567"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Blob Storage에서 데이터 복사 및 변환

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-azure-blob-connector.md)
> * [현재 버전](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 Azure Blob storage 간에 데이터를 복사 하는 방법을 설명 합니다. 또한 데이터 흐름 작업을 사용 하 여 Azure Blob 저장소에서 데이터를 변환 하는 방법을 설명 합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

>[!TIP]
>Data lake 또는 데이터 웨어하우스의 마이그레이션 시나리오에 대 한 자세한 내용은 [Azure Data Factory를 사용 하 여 data lake 또는 데이터 웨어하우스에서 데이터를 Azure로 마이그레이션](data-migration-guidance-overview.md)을 참조 하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Blob 저장소 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [삭제 작업](delete-activity.md)

복사 작업의 경우이 Blob storage 커넥터는 다음을 지원 합니다.

- 범용 Azure 스토리지 계정 및 핫/쿨 Blob Storage 간에 Blob을 복사합니다. 
- 계정 키, 서비스 공유 액세스 서명 (SAS), 서비스 주체 또는 Azure 리소스 인증에 대 한 관리 되는 id를 사용 하 여 blob을 복사 합니다.
- 블록, 추가 또는 페이지 Blob에서 Blob을 복사하고, 블록 Blob에만 데이터를 복사합니다.
- Blob을 있는 그대로 복사 하거나 [지원 되는 파일 형식 및 압축 코덱을](supported-file-formats-and-compression-codecs.md)사용 하 여 blob을 구문 분석 하거나 생성 합니다.
- [복사 중에 파일 메타 데이터를 유지](#preserving-metadata-during-copy)합니다.

>[!IMPORTANT]
>Azure Storage 방화벽 설정에서 **신뢰할 수 있는 Microsoft 서비스에서이 저장소 계정에 액세스 하도록 허용** 옵션을 사용 하도록 설정 하 고 Azure integration runtime을 사용 하 여 Blob storage에 연결 하려면 [관리 되는 id 인증](#managed-identity)을 사용 해야 합니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Blob Storage에 특정한 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

이 Blob storage 커넥터는 다음 인증 유형을 지원 합니다. 자세한 내용은 해당 섹션을 참조 하세요.

- [계정 키 인증](#account-key-authentication)
- [공유 액세스 서명 인증](#shared-access-signature-authentication)
- [서비스 주체 인증](#service-principal-authentication)
- [Azure 리소스 인증에 대 한 관리 되는 id](#managed-identity)

>[!NOTE]
>PolyBase를 사용 하 여 Azure SQL Data Warehouse에 데이터를 로드 하는 경우 원본 또는 스테이징 Blob 저장소가 Azure Virtual Network 끝점으로 구성 된 경우 PolyBase에서 요구 하는 대로 관리 되는 id 인증을 사용 해야 합니다. 또한 버전 3.18 이상에서 자체 호스팅 통합 런타임을 사용 해야 합니다. 자세한 구성 필수 구성 요소는 [관리 id 인증](#managed-identity) 섹션을 참조 하세요.

>[!NOTE]
>Azure HDInsight 및 Azure Machine Learning 작업은 Azure Blob 저장소 계정 키를 사용 하는 인증만 지원 합니다.

### <a name="account-key-authentication"></a>계정 키 인증

Data Factory는 저장소 계정 키 인증에 대 한 다음 속성을 지원 합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | **Type** 속성은 **azureblobstorage** (제안) 또는 **azurestorage** 로 설정 해야 합니다 (다음 참고 참조). |예 |
| connectionString | **ConnectionString** 속성에 대 한 저장소에 연결 하는 데 필요한 정보를 지정 합니다. <br/> Azure Key Vault에 계정 키를 입력 하 고 연결 문자열에서 구성을 끌어올 수도 있습니다 `accountKey` . 자세한 내용은 Azure Key Vault 문서의 다음 샘플 및 [저장소 자격 증명](store-credentials-in-key-vault.md) 을 참조 하세요. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure integration runtime 또는 자체 호스팅 integration runtime (데이터 저장소가 개인 네트워크에 있는 경우)을 사용할 수 있습니다. 이 속성이 지정 되지 않은 경우 서비스는 기본 Azure integration runtime을 사용 합니다. |예 |

>[!NOTE]
>계정 키 인증을 사용 하는 경우 보조 Blob service 끝점이 지원 되지 않습니다. 다른 인증 유형을 사용할 수 있습니다.

>[!NOTE]
>"AzureStorage" 형식 연결 된 서비스를 사용 하는 경우 아직 그대로 지원 됩니다. 그러나 앞으로 새 "AzureBlobStorage" 연결 된 서비스 유형을 사용 하는 것이 좋습니다.

**예제:**

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

**예: 계정 키를에 저장 Azure Key Vault**

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

공유 액세스 서명은 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 공유 액세스 서명을 사용하여 스토리지 계정의 개체에 대해 지정된 시간 동안 제한된 권한을 클라이언트에 부여할 수 있습니다. 

계정 액세스 키를 공유할 필요가 없습니다. 공유 액세스 서명은 스토리지 리소스에 대해 인증된 액세스에 필요한 모든 정보를 쿼리 매개 변수에 포함하는 URI입니다. 공유 액세스 서명을 사용하여 스토리지 리소스에 액세스하려면 클라이언트에서 공유 액세스 서명을 해당 생성자 또는 메서드에 전달하기만 하면 됩니다. 

공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명: 공유 액세스 서명 모델 이해](../storage/common/storage-dotnet-shared-access-signature-part-1.md)를 참조하세요.

> [!NOTE]
>- Data Factory에서 이제 *서비스 공유 액세스 서명*과 *계정 공유 액세스 서명*이 모두 지원됩니다. 공유 액세스 서명에 대 한 자세한 내용은 [공유 액세스 서명을 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여](../storage/common/storage-sas-overview.md)를 참조 하세요.
>- 이후 데이터 집합 구성에서 폴더 경로는 컨테이너 수준에서 시작 하는 절대 경로입니다. SAS URI의 경로와 일치하는 경로를 구성해야 합니다.

Data Factory는 공유 액세스 서명 인증을 사용 하는 다음 속성을 지원 합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | **Type** 속성은 **azureblobstorage** (제안) 또는 **azurestorage** 로 설정 해야 합니다 (다음 참고 참조). |예 |
| sasUri | Blob 또는 컨테이너와 같은 저장소 리소스에 대 한 공유 액세스 서명 URI를 지정 합니다. <br/>이 필드를 **SecureString** 으로 표시 하 여 Data Factory에 안전 하 게 저장 합니다. SAS 토큰을 Azure Key Vault에 배치 하 여 자동 회전을 사용 하 고 토큰 부분을 제거할 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)을 참조 하세요. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure integration runtime 또는 자체 호스팅 integration runtime (데이터 저장소가 개인 네트워크에 있는 경우)을 사용할 수 있습니다. 이 속성이 지정 되지 않은 경우 서비스는 기본 Azure integration runtime을 사용 합니다. |예 |

>[!NOTE]
>"AzureStorage" 형식 연결 된 서비스를 사용 하는 경우 아직 그대로 지원 됩니다. 그러나 앞으로 새 "AzureBlobStorage" 연결 된 서비스 유형을 사용 하는 것이 좋습니다.

**예제:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예: 계정 키를에 저장 Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName with value of SAS token e.g. ?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>" 
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
- URI는 필요에 따라 올바른 컨테이너 또는 blob에 생성 되어야 합니다. Blob에 대한 공유 액세스 서명 URI를 사용하면 Data Factory에서 해당 특정 Blob에 액세스할 수 있습니다. Blob 스토리지 컨테이너에 대한 공유 액세스 서명 URI를 사용하면 Data Factory에서 해당 컨테이너의 Blob을 반복할 수 있습니다. 나중에 더 많거나 적은 개체에 대한 액세스를 제공하거나 공유 액세스 서명 URI를 업데이트하려면 연결된 서비스를 새 URI로 업데이트해야 합니다.

### <a name="service-principal-authentication"></a>서비스 주체 인증

Azure Storage 서비스 주체 인증에 대 한 일반 정보는 [Azure Active Directory를 사용 하 여 Azure Storage에 대 한 액세스 인증](../storage/common/storage-auth-aad.md)을 참조 하세요.

서비스 주체 인증을 사용하려면 다음 단계를 수행합니다.

1. [Azure AD 테넌트에 애플리케이션 등록](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)에 따라 Azure AD(Azure Active Directory)에 애플리케이션 항목을 등록합니다. 연결 된 서비스를 정의 하는 데 사용 하는 다음 값을 기록해 둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. Azure Blob Storage에서 서비스 주체에게 적절한 권한을 부여합니다. 역할에 대 한 자세한 내용은 RBAC를 [사용 하 여 데이터를 Azure Storage에 대 한 액세스 권한 관리](../storage/common/storage-auth-aad-rbac.md)를 참조 하세요.

    - **원본으로** **액세스 제어 (IAM)** 에서 적어도 **저장소 Blob 데이터 판독기** 역할을 부여 합니다.
    - **싱크로** **액세스 제어 (IAM)** 에서 적어도 **저장소 Blob 데이터 참가자** 역할을 부여 합니다.

Azure Blob Storage 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | **Type** 속성은 **azureblobstorage**로 설정 해야 합니다. |예 |
| serviceEndpoint | 패턴이 `https://<accountName>.blob.core.windows.net/`인 Azure Blob Storage 서비스 엔드포인트를 지정합니다. |예 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString** 으로 표시 하 여 Data Factory에 안전 하 게 저장 하거나 [Azure Key Vault에 저장 된 암호를 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모퉁이를 가리켜 검색 합니다. | 예 |
| azureCloudType | 서비스 사용자 인증의 경우 Azure Active Directory 응용 프로그램이 등록 된 Azure 클라우드 환경의 유형을 지정 합니다. <br/> 허용 되는 값은 **Azurepublic**, **azurepublic**, **azureus정부**및 **AzureGermany**입니다. 기본적으로 데이터 팩터리의 클라우드 환경이 사용 됩니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure integration runtime 또는 자체 호스팅 integration runtime (데이터 저장소가 개인 네트워크에 있는 경우)을 사용할 수 있습니다. 이 속성이 지정 되지 않은 경우 서비스는 기본 Azure integration runtime을 사용 합니다. |예 |

>[!NOTE]
>서비스 사용자 인증은 이전 "AzureStorage" 형식 연결 된 서비스가 아닌 "AzureBlobStorage" 유형의 연결 된 서비스 에서만 지원 됩니다.

**예제:**

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

### <a name="managed-identities-for-azure-resource-authentication"></a><a name="managed-identity"></a> Azure 리소스 인증에 대 한 관리 되는 id

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. 사용자 고유의 서비스 주체를 사용 하는 것과 비슷한 Blob storage 인증에 대해이 관리 되는 id를 직접 사용할 수 있습니다. 이 지정 된 팩터리에서 Blob 저장소에서 데이터에 액세스 하 고 복사할 수 있습니다.

Azure Storage 인증에 대 한 일반 정보는 [Azure Active Directory를 사용 하 여 Azure Storage에 대 한 액세스 인증](../storage/common/storage-auth-aad.md)을 참조 하세요. Azure 리소스 인증에 관리 되는 id를 사용 하려면 다음 단계를 수행 합니다.

1. 팩터리와 함께 생성 된 관리 id 개체 ID의 값을 복사 하 여 [관리 되는 id 정보를 Data Factory 검색](data-factory-service-identity.md#retrieve-managed-identity) 합니다.

2. Azure Blob storage에서 관리 id 사용 권한을 부여 합니다. 역할에 대 한 자세한 내용은 RBAC를 [사용 하 여 데이터를 Azure Storage에 대 한 액세스 권한 관리](../storage/common/storage-auth-aad-rbac.md)를 참조 하세요.

    - **원본으로** **액세스 제어 (IAM)** 에서 적어도 **저장소 Blob 데이터 판독기** 역할을 부여 합니다.
    - **싱크로** **액세스 제어 (IAM)** 에서 적어도 **저장소 Blob 데이터 참가자** 역할을 부여 합니다.

>[!IMPORTANT]
>PolyBase를 사용 하 여 Blob 저장소 (원본 또는 스테이징)에서 SQL Data Warehouse로 데이터를 로드 하는 경우 Blob 저장소에 대해 관리 id 인증을 사용 하는 경우 [이 지침](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)의 1 및 2 단계를 수행 해야 합니다. 이러한 단계에서는 서버를 Azure AD에 등록 하 고 저장소 Blob 데이터 참가자 역할을 서버에 할당 합니다. Data Factory 나머지를 처리 합니다. Azure Virtual Network 끝점을 사용 하 여 Blob 저장소를 구성한 경우 PolyBase를 사용 하 여 데이터를 로드 하려면 PolyBase에서 요구 하는 대로 관리 되는 id 인증을 사용 해야 합니다.

Azure Blob Storage 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | **Type** 속성은 **azureblobstorage**로 설정 해야 합니다. |예 |
| serviceEndpoint | 패턴이 `https://<accountName>.blob.core.windows.net/`인 Azure Blob Storage 서비스 엔드포인트를 지정합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure integration runtime 또는 자체 호스팅 integration runtime (데이터 저장소가 개인 네트워크에 있는 경우)을 사용할 수 있습니다. 이 속성이 지정 되지 않은 경우 서비스는 기본 Azure integration runtime을 사용 합니다. |예 |

> [!NOTE]
> Azure 리소스 인증에 대 한 관리 되는 id는 이전의 "AzureStorage" 형식 연결 된 서비스가 아닌 "AzureBlobStorage" 유형의 연결 된 서비스 에서만 지원 됩니다.

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

형식 기반 데이터 집합의 설정에서 Azure Blob storage에 대해 지원 되는 속성은 다음과 `location` 같습니다.

| 속성   | Description                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 데이터 집합에 있는 위치의 **type** 속성은 **Azureblobstoragelocation**으로 설정 해야 합니다. | 예      |
| container  | Blob 컨테이너입니다.                                          | 예      |
| folderPath | 지정 된 컨테이너 아래에 있는 폴더의 경로입니다. 와일드 카드를 사용 하 여 폴더를 필터링 하려면이 설정을 건너뛰고 작업 원본 설정에서 지정 합니다. | 예       |
| fileName   | 지정 된 컨테이너 및 폴더 경로 아래에 있는 파일 이름입니다. 와일드 카드를 사용 하 여 파일을 필터링 하려는 경우이 설정을 건너뛰고 작업 원본 설정에서 지정 합니다. | 예       |

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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Blob 저장소 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="blob-storage-as-a-source-type"></a>Blob Storage를 원본 형식으로

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

다음은 `storeSettings` 형식 기반 복사 원본에서 설정 아래의 Azure Blob storage에 대해 지원 되는 속성입니다.

| 속성                 | Description                                                  | 필수                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 아래의 **type** 속성은 `storeSettings` **Azureblobstoragereadsettings**로 설정 되어야 합니다. | 예                                           |
| ***복사할 파일 찾기:*** |  |  |
| 옵션 1: 정적 경로<br> | 제공된 컨테이너 또는 데이터 세트에 지정된 폴더/파일 경로에서 복사합니다. 컨테이너 또는 폴더에서 모든 blob을 복사 하려면 as를 추가로 지정 `wildcardFileName` `*` 합니다. |  |
| 옵션 2: Blob 접두사<br>- prefix | 원본 blob을 필터링 하기 위해 데이터 집합에 구성 된 지정 된 컨테이너 아래의 blob 이름에 대 한 접두사입니다. 이름이로 시작 하 `container_in_dataset/this_prefix` 는 blob이 선택 됩니다. Blob 저장소에 대 한 서비스 쪽 필터를 활용 하 여 와일드 카드 필터 보다 더 나은 성능을 제공 합니다. | 예                                                          |
| 옵션 3: 와일드카드<br>- wildcardFolderPath | 원본 폴더를 필터링 하기 위해 데이터 집합에 구성 된 지정 된 컨테이너 아래에 와일드 카드 문자가 있는 폴더 경로입니다. <br>허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다. `^`폴더 이름에 와일드 카드나이 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 처리 합니다. <br>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 예                                            |
| 옵션 3: 와일드카드<br>- wildcardFileName | 소스 파일을 필터링 하기 위해 지정 된 컨테이너 및 폴더 경로 (또는 와일드 카드 폴더 경로) 아래에 와일드 카드 문자가 있는 파일 이름입니다. <br>허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다. `^`폴더 이름에 와일드 카드나이 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다. 더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 예 |
| 옵션 4: 파일 목록<br>- fileListPath | 지정된 파일 집합을 복사하도록 지정합니다. 복사할 파일 목록이 포함 된 텍스트 파일을 가리키고, 데이터 집합에 구성 된 경로에 대 한 상대 경로인 한 줄에 하나씩 파일을 표시 합니다.<br/>이 옵션을 사용 하는 경우 데이터 집합에서 파일 이름을 지정 하지 마십시오. [파일 목록 예](#file-list-examples)에서 더 많은 예를 참조하세요. |예 |
| ***추가 설정:*** |  | |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. **Recursive** 를 **true** 로 설정 하 고 싱크가 파일 기반 저장소 인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사 되거나 생성 되지 않습니다. <br>허용되는 값은 **true**(기본값) 및 **false**입니다.<br>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. |예 |
| deleteFilesAfterCompletion | 대상 저장소로 이동한 후에 소스 저장소에서 이진 파일을 삭제할지 여부를 나타냅니다. 파일 삭제는 파일 단위 이므로 복사 작업에 실패 하면 일부 파일이 이미 대상에 복사 되 고 원본에서 삭제 된 것을 확인할 수 있습니다. 반면 다른 파일은 원본 저장소에 남아 있습니다. <br/>이 속성은 이진 복사 시나리오 에서만 유효 합니다. 여기에서 데이터 원본 저장소는 Blob, ADLS Gen1, ADLS Gen2, S3, Google 클라우드 저장소, 파일, Azure 파일, SFTP 또는 FTP입니다. 기본값은 false입니다. |예 |
| modifiedDatetimeStart    | 파일은 특성을 기준으로 필터링 됩니다. 마지막으로 수정한 날짜입니다. <br>마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 "2018-12-01T05:00:00Z" 형식의 UTC 표준 시간대에 적용 됩니다. <br> 속성은 **NULL**일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` 가 **NULL**이면 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` 가 **NULL**이면 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 됩니다.<br/>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. | 예                                            |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 예                                            |
| Enable파티션 검색 | 분할 된 파일의 경우 파일 경로에서 파티션을 구문 분석할 지 여부를 지정 하 고 추가 원본 열로 추가 합니다.<br/>허용 되는 값은 **false** (기본값) 및 **true**입니다. | 예                                            |
| 파티션 (partitionRootPath) | 파티션 검색을 사용 하는 경우 분할 된 폴더를 데이터 열로 읽도록 절대 루트 경로를 지정 합니다.<br/><br/>지정 되지 않은 경우 기본적으로<br/>-원본에 있는 파일 또는 데이터 집합의 파일 경로를 사용 하는 경우 파티션 루트 경로는 데이터 집합에서 구성 된 경로입니다.<br/>-와일드 카드 폴더 필터를 사용 하는 경우 파티션 루트 경로는 첫 번째 와일드 카드 앞의 하위 경로입니다.<br/>-접두사를 사용 하는 경우 파티션 루트 경로는 마지막 "/" 앞의 하위 경로입니다. <br/><br/>예를 들어 데이터 집합의 경로를 "root/folder/year = 2020/month = 08/day = 27"로 구성 한다고 가정 합니다.<br/>-파티션 루트 경로를 "root/folder/year = 2020"으로 지정 하는 경우 복사 작업은 파일 내의 열 외에도 각각 두 개의 열을 생성 하 `month` 고 `day` 값을 "08" 및 "27"로 생성 합니다.<br/>-파티션 루트 경로를 지정 하지 않으면 추가 열이 생성 되지 않습니다. | 예                                            |
| maxConcurrentConnections | 저장소에 대 한 동시 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 예                                            |

> [!NOTE]
> Parquet/구분 된 텍스트 형식의 경우 다음 섹션에 언급 된 복사 작업 원본에 대 한 **blobsource** 형식은 이전 버전과의 호환성을 위해 계속 지원 됩니다. Data Factory 작성 UI가 이러한 새 형식을 생성 하도록 전환 될 때까지 새 모델을 사용 하는 것이 좋습니다.

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

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)] 

형식 기반 복사 싱크의 설정에서 Azure Blob storage에 대해 지원 되는 속성은 다음과 `storeSettings` 같습니다.

| 속성                 | Description                                                  | 필수 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 아래의 **type** 속성은 `storeSettings` **Azureblobstoragewritesettings**로 설정 되어야 합니다. | 예      |
| copyBehavior             | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 병합되는 파일 이름은 지정된 파일 또는 Blob 이름이 적용됩니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 예       |
| blockSizeInMB | 블록 blob에 데이터를 쓰는 데 사용 되는 블록 크기 (mb)를 지정 합니다. [블록 Blob에 대한](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 자세한 내용을 알아보세요. <br/>허용 되는 값은 *4mb에서 100 mb 사이*입니다. <br/>기본적으로 Data Factory는 원본 저장소 형식 및 데이터를 기반으로 블록 크기를 자동으로 결정 합니다. Blob storage로 복사 하는 이진이 아닌의 경우 기본 블록 크기는 100 이며 최대 4.95 TB의 데이터에 맞출 수 있습니다. 특히 작업 시간 초과 또는 성능 문제를 초래 하는 네트워크 연결이 좋지 않은 자체 호스팅 통합 런타임을 사용 하는 경우 데이터가 크지 않은 경우에는 최적이 아닐 수 있습니다. 블록 크기를 명시적으로 지정 하는 동시에 `blockSizeInMB*50000` 데이터를 저장 하기에 충분 한 크기를 보장할 수 있습니다. 그렇지 않으면 복사 작업 실행이 실패 합니다. | 예 |
| maxConcurrentConnections | 저장소에 대 한 동시 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 예       |

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

### <a name="file-list-examples"></a>파일 목록 예

이 섹션에서는 복사 작업 원본에서 파일 목록 경로를 사용 하는 경우의 결과 동작을 설명 합니다.

다음 원본 폴더 구조가 있고 파일을 굵게 표시 한다고 가정 합니다.

| 샘플 원본 구조                                      | FileListToCopy.txt의 콘텐츠                             | Data Factory 구성                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;메타데이터<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **데이터 세트:**<br>- 컨테이너: `container`<br>- 폴더 경로: `FolderA`<br><br>**복사 활동 원본에서:**<br>- 파일 목록 경로: `container/Metadata/FileListToCopy.txt` <br><br>파일 목록 경로는 데이터 집합에 구성 된 경로에 대 한 상대 경로를 사용 하 여 복사 하려는 파일 목록이 포함 된 동일한 데이터 저장소의 텍스트 파일을 한 줄에 하나씩 가리킵니다. |

### <a name="some-recursive-and-copybehavior-examples"></a>일부 recursive 및 copyBehavior 예제

이 섹션에서는 여러 가지 **재귀** 및 복사 **동작** 값 조합에 대 한 복사 작업의 결과 동작을 설명 합니다.

| recursive | copyBehavior | 원본 폴더 구조 | 결과 대상 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 대상 폴더인 Folder1는 원본과 동일한 구조를 사용 하 여 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 대상 폴더인 Folder1는 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 대상 폴더인 Folder1는 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1, File2, File3, File4 및 File5의 내용이 자동 생성된 파일 이름이 있는 하나의 파일로 병합됩니다. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 대상 폴더인 Folder1는 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 대상 폴더인 Folder1는 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 대상 폴더인 Folder1는 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1과 File2의 내용이 자동 생성된 파일 이름이 있는 하나의 파일로 병합됩니다. File1에 대해 자동 생성된 이름<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |

## <a name="preserving-metadata-during-copy"></a>복사 중 메타 데이터 유지

Amazon S3, Azure Blob storage 또는 Azure Data Lake Storage Gen2에서 Azure Data Lake Storage Gen2 또는 Azure Blob storage로 파일을 복사 하는 경우 데이터와 함께 파일 메타 데이터를 유지 하도록 선택할 수 있습니다. [메타데이터 유지](copy-activity-preserve-metadata.md#preserve-metadata)에서 자세히 알아보세요.

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

매핑 데이터 흐름에서 데이터를 변환 하는 경우 Azure Blob storage에서 다음과 같은 형식으로 파일을 읽고 쓸 수 있습니다.
* [Avro](format-avro.md#mapping-data-flow-properties)
* [구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties)
* [삼각](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

서식 지정 설정은 해당 형식에 대 한 설명서에 있습니다. 자세한 내용은 데이터 흐름 매핑에서 데이터 흐름 매핑 및 [싱크 변환](data-flow-sink.md) [의 원본 변환](data-flow-source.md) 을 참조 하세요.

### <a name="source-transformation"></a>원본 변환

원본 변환에서 Azure Blob 저장소의 컨테이너, 폴더 또는 개별 파일을 읽을 수 있습니다. **원본 옵션** 탭을 사용 하 여 파일을 읽는 방법을 관리할 수 있습니다. 

![원본 옵션](media/data-flow/sourceOptions1.png "원본 옵션")

**와일드 카드 경로:** 와일드 카드 패턴을 사용 하면 일치 하는 각 폴더와 파일을 단일 원본 변환에서 반복 하도록 Data Factory에 지시할 수 있습니다. 이는 단일 흐름 내에서 여러 파일을 처리하는 효과적인 방법입니다. 기존 와일드 카드 패턴을 마우스로 가리킬 때 표시 되는 더하기 기호를 사용 하 여 와일드 카드 일치 패턴을 여러 개 추가 합니다.

원본 컨테이너에서 패턴과 일치하는 일련의 파일을 선택합니다. 데이터 집합에는 컨테이너만 지정할 수 있습니다. 따라서 와일드카드 경로에는 루트 폴더의 폴더 경로도 포함되어야 합니다.

와일드카드 예제:

* ```*``` 모든 문자 집합을 나타냅니다.
* ```**``` 재귀 디렉터리 중첩을 나타냅니다.
* ```?``` 한 문자를 대체 합니다.
* ```[]``` 대괄호에서 하나 이상의 문자를 찾습니다.

* ```/data/sales/**/*.csv``` /Data/sales. 아래의 모든 .csv 파일을 가져옵니다.
* ```/data/sales/20??/**/``` 20 세기의 모든 파일을 가져옵니다.
* ```/data/sales/*/*/*.csv``` /Data/sales.에서 .csv 파일을 두 수준으로 가져옵니다.
* ```/data/sales/2004/*/12/[XY]1?.csv``` X 또는 Y 접두사가 2 자리 숫자로 시작 하는 12 월 2004의 모든 .csv 파일을 가져옵니다.

**파티션 루트 경로:** 파일 원본에 형식으로 분할 된 폴더가 있는 경우 ```key=value``` (예: `year=2019` ) 해당 파티션 폴더 트리의 최상위 수준을 데이터 흐름의 데이터 스트림에 있는 열 이름에 할당할 수 있습니다.

먼저 분할 된 폴더와 읽을 리프 파일의 모든 경로를 포함 하도록 와일드 카드를 설정 합니다.

![파티션 원본 파일 설정](media/data-flow/partfile2.png "파티션 파일 설정")

**파티션 루트 경로** 설정을 사용 하 여 폴더 구조의 최상위 수준을 정의 합니다. 데이터 미리 보기를 통해 데이터의 내용을 볼 때 각 폴더 수준에서 발견 된 확인 된 파티션을 Data Factory 추가 하는 것을 볼 수 있습니다.

![파티션 루트 경로](media/data-flow/partfile1.png "파티션 루트 경로 미리 보기")

**파일 목록:** 파일 집합입니다. 처리할 상대 경로 파일 목록이 포함된 텍스트 파일을 만듭니다. 이 텍스트 파일을 가리킵니다.

**파일 이름을 저장할 열:** 원본 파일의 이름을 데이터 열에 저장합니다. 파일 이름 문자열을 저장하려면 여기에 새 열 이름을 입력합니다.

**완료 후 기간(일):** 데이터 흐름이 실행된 후 원본 파일을 사용해 아무 작업도 수행하지 않도록 선택하거나, 원본 파일을 삭제 또는 이동합니다. 이동 경로는 상대 경로입니다.

원본 파일을 다른 위치에 후처리로 이동하려면 먼저 파일 작업에서 "이동"을 선택합니다. 그런 다음, "원본" 디렉터리를 설정합니다. 경로에 와일드카드를 사용하지 않을 경우 "원본" 설정은 원본 폴더와 같은 폴더에 있습니다.

와일드 카드가 있는 원본 경로를 사용 하는 경우 구문은 다음과 같습니다.

```/data/sales/20??/**/*.csv```

"From"을 다음과 같이 지정할 수 있습니다.

```/data/sales```

"To"를 다음과 같이 지정할 수 있습니다.

```/backup/priorSales```

이 경우 /data/sale 아래에 원본이 있는 모든 파일이 /backup/priorSales로 이동됩니다.

> [!NOTE]
> 파이프라인에서 Data Flow 실행 작업을 사용하는 파이프라인 실행(파이프라인 디버그 또는 실행)에서 데이터 흐름을 시작하는 경우에만 파일 작업이 실행됩니다. Data Flow 디버그 모드에서는 파일 작업이 실행되지 *않습니다.*

**마지막으로 수정한 사람 필터링:** 마지막으로 수정된 날짜 범위를 지정하여 처리하는 파일을 필터링할 수 있습니다. 모든 날짜/시간은 UTC 시간입니다. 

### <a name="sink-properties"></a>싱크 속성

싱크 변환에서 컨테이너 또는 Azure Blob storage의 폴더에 쓸 수 있습니다. **설정** 탭을 사용 하 여 파일을 작성 하는 방법을 관리할 수 있습니다.

![싱크 옵션](media/data-flow/file-sink-settings.png "싱크 옵션")

**폴더 선택 취소:** 데이터를 쓰기 전에 대상 폴더를 지울 것인지 여부를 결정합니다.

**파일 이름 옵션:** 대상 폴더에서 대상 파일의 이름을 지정하는 방법을 결정합니다. 파일 이름 옵션은 다음과 같습니다.
   * **기본값**: Spark에서 PART 기본값에 따라 파일 이름을 지정하도록 허용합니다.
   * **패턴**: 파티션별로 출력 파일을 열거하는 패턴을 입력합니다. 예를 들어, **loans[n].csv**에서 loans1.csv, loans2.csv 등을 만듭니다.
   * **파티션 기준**: 파티션별 파일 이름을 입력합니다.
   * **열의 데이터로**: 출력 파일을 열 값으로 설정합니다. 경로는 대상 폴더가 아닌 데이터 세트 컨테이너를 기준으로 합니다. 데이터 세트에 폴더 경로가 있는 경우 재정의됩니다.
   * **단일 파일로 출력**: 분할된 출력 파일을 명명된 단일 파일로 결합합니다. 경로는 데이터 세트 폴더를 기준으로 합니다. 병합 작업은 노드 크기에 따라 실패할 수 있습니다. 대량 데이터 집합에는이 옵션을 권장 하지 않습니다.

**모두 인용:** 모든 값을 따옴표로 묶을 지 여부를 결정 합니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 작업 속성

속성에 대 한 자세한 내용을 보려면 [GetMetadata 활동](control-flow-get-metadata-activity.md)을 확인 하세요. 

## <a name="delete-activity-properties"></a>삭제 작업 속성

속성에 대 한 자세한 내용을 보려면 [삭제 작업](delete-activity.md)을 선택 합니다.

## <a name="legacy-models"></a>레거시 모델

>[!NOTE]
>다음 모델은 이전 버전과의 호환성을 위해 계속 지원 됩니다. 앞에서 설명한 새 모델을 사용 하는 것이 좋습니다. Data Factory authoring UI가 새 모델을 생성 하도록 전환 되었습니다.

### <a name="legacy-dataset-model"></a>레거시 데이터 세트 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 **type** 속성은 **azureblob**으로 설정 해야 합니다. |예 |
| folderPath | Blob 저장소의 컨테이너 및 폴더에 대 한 경로입니다. <br/><br/>컨테이너 이름을 제외 하 고 경로에 와일드 카드 필터가 지원 됩니다. 허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다. `^`폴더 이름에 와일드 카드나이 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다. <br/><br/>예: myblobcontainer/myblobcontainer/. 더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. |복사 또는 조회 작업의 경우 예, GetMetadata 작업의 경우 아니요 |
| fileName | 지정 된 **folderPath** 값 아래의 blob에 대 한 이름 또는 와일드 카드 필터입니다. 이 속성의 값을 지정하지 않으면 데이터 세트는 폴더에 있는 모든 Blob을 가리킵니다. <br/><br/>필터의 경우 허용 되는 와일드 카드는 `*` (0 개 이상의 문자와 일치) 및 `?` (는 0 또는 단일 문자와 일치)입니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>`^`파일 이름에 와일드 카드 또는이 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다.<br/><br/>**FileName** 이 출력 데이터 집합에 대해 지정 되지 않고 **preserveHierarchy** 가 활동 싱크에 지정 되지 않은 경우 복사 작업은 다음 패턴으로 blob 이름을 자동으로 생성 합니다. "*Data. [ 작업 실행 ID입니다. [GUID if FlattenHierarchy]입니다. [구성 된 경우 형식]. [압축이 구성 된 경우]*". 예: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. release.tar.gz". <br/><br/>쿼리 대신 테이블 이름을 사용 하 여 테이블 형식 원본에서 복사 하는 경우 이름 패턴은 "*[table name]. [ format]. [압축이 구성 된 경우]*". 예를 들면 "MyTable.csv"입니다. |예 |
| modifiedDatetimeStart | 파일은 특성을 기준으로 필터링 됩니다. 마지막으로 수정한 날짜입니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 이 설정을 사용 하면 대량의 파일을 필터링 하려는 경우 데이터 이동의 전반적인 성능에 영향을 줍니다. <br/><br/> 속성은 **NULL**일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` 가 **NULL**이면 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` 가 **NULL**이면 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 됩니다.| 예 |
| modifiedDatetimeEnd | 파일은 특성을 기준으로 필터링 됩니다. 마지막으로 수정한 날짜입니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 이 설정을 사용 하면 대량의 파일을 필터링 하려는 경우 데이터 이동의 전반적인 성능에 영향을 줍니다. <br/><br/> 속성은 **NULL**일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` 가 **NULL**이면 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` 가 **NULL**이면 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 됩니다.| 예 |
| format | 파일 기반 저장소(이진 복사본) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 format 섹션을 건너뜁니다.<br/><br/>특정 형식의 파일을 구문 분석하거나 생성하려는 경우, 지원되는 파일 형식 유형은 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** 및 **ParquetFormat**입니다. **format**의 **type** 속성을 이 값 중 하나로 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON 형식](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs-legacy.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs-legacy.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>**Optimal** 및 **Fastest** 수준이 지원됩니다. |예 |

>[!TIP]
>폴더 아래에서 모든 Blob을 복사하려면 **folderPath**만을 지정합니다.<br>지정 된 이름의 단일 blob을 복사 하려면 파일 이름 **에 대 한 폴더 및 파일** 이름에 대해 **folderPath** 를 지정 합니다.<br>폴더 아래에 있는 blob의 하위 집합을 복사 하려면 와일드 카드 필터를 사용 하 여 폴더 파트 및 **파일 이름** 에 대해 **folderPath** 를 지정 합니다. 

**예제:**

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

### <a name="legacy-source-model-for-the-copy-activity"></a>복사 작업에 대 한 레거시 원본 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **type** 속성은 **blobsource**로 설정 해야 합니다. |예 |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. **Recursive** 를 **true** 로 설정 하 고 싱크가 파일 기반 저장소 인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사 되거나 생성 되지 않습니다.<br/>허용되는 값은 **true**(기본값) 및 **false**입니다. | 예 |
| maxConcurrentConnections | 저장소에 대 한 동시 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 예 |

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

### <a name="legacy-sink-model-for-the-copy-activity"></a>복사 작업에 대 한 레거시 싱크 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 **type** 속성은 **blobsink**로 설정 해야 합니다. |예 |
| copyBehavior | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 병합되는 파일 이름은 지정된 파일 또는 Blob 이름이 적용됩니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 예 |
| maxConcurrentConnections | 저장소에 대 한 동시 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 예 |

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

Data Factory의 복사 작업에서 원본 및 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조 하세요.
