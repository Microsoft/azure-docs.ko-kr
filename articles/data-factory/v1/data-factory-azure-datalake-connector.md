---
title: Azure Data Lake Storage Gen1 간 데이터 복사 | Microsoft Docs
description: Azure Data Factory를 사용하여 Data Lake Store 간 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3bb372c4c3ddb79429df20c24c691c847e927e2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567353"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Data Factory를 사용하여 Data Lake Storage Gen1 간 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](data-factory-azure-datalake-connector.md)
> * [버전 2(현재 버전)](../connector-azure-data-lake-store.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우 [V2의 Azure Data Lake Storage Gen1 커넥터](../connector-azure-data-lake-store.md)를 참조하세요.

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Data Lake Storage Gen1(이전의 Azure Data Lake Store) 간에 데이터를 이동하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 개요를 보여 주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 기반으로 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오
**Azure Data Lake Store에서** 다음 데이터 저장소로 데이터를 복사할 수 있습니다.

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

다음 데이터 저장소에서 **Azure Data Lake Store로** 데이터를 복사할 수 있습니다.

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> 복사 작업을 사용하여 파이프라인을 만들기 전에 Data Lake Store 계정을 만듭니다. 자세한 내용은 [Azure Data Lake Store 시작](../../data-lake-store/data-lake-store-get-started-portal.md)을 참조하세요.

## <a name="supported-authentication-types"></a>지원되는 인증 형식
Data Lake Store 커넥터는 다음 인증 유형을 지원합니다.
* 서비스 주체 인증
* 사용자 자격 증명(OAuth) 인증

특히 예약된 데이터 복사의 경우 서비스 주체 인증을 사용하는 것이 좋습니다. 사용자 자격 증명 인증의 경우 토큰 만료 동작이 발생할 수 있습니다. 구성 세부 정보에서 [연결된 서비스 속성](#linked-service-properties) 섹션을 참조하세요.

## <a name="get-started"></a>시작하기
다른 도구/API를 사용하여 Azure Data Lake Store 간에 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다.

데이터를 복사하기 위한 파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사**를 사용하는 것입니다. 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 자습서는 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

또한 다음 도구를 사용하여 파이프라인을 만들 수 있습니다. **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API** 및 **REST API** 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. **데이터 팩터리**를 만듭니다. 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다.
2. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스**를 만듭니다. 예를 들어 Azure Blob Storage에서 Azure Data Lake Store로 데이터를 복사하는 경우 Azure Storage 계정 및 Azure Data Lake Store를 데이터 팩터리에 연결하는 두 개의 연결된 서비스를 만듭니다. Azure Data Lake Store와 관련된 연결된 서비스 속성은 [연결된 서비스 속성](#linked-service-properties) 섹션을 참조하세요.
2. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트**를 만듭니다. 마지막 단계에서 설명한 예제에서는 입력 데이터가 포함된 BLOB 컨테이너 및 폴더를 지정하는 데이터 세트를 만듭니다. 그리고 Blob Storage에서 복사한 데이터를 포함하는 Data Lake Store의 폴더 및 파일 경로를 지정하는 또 다른 데이터 세트를 만듭니다. Azure Data Lake Store와 관련된 데이터 세트 속성은 [데이터 세트 속성](#dataset-properties) 섹션을 참조하세요.
3. 입력으로 데이터 세트를, 출력으로 데이터 세트를 사용하는 복사 작업을 통해 **파이프라인**을 만듭니다. 앞에서 언급한 예제에서는 BlobSource를 원본으로, AzureDataLakeStoreSink를 복사 작업의 싱크로 사용합니다. 마찬가지로 Azure Data Lake Store에서 Azure Blob Storage로 복사하는 경우 복사 작업에서 AzureDataLakeStoreSource 및 BlobSink를 사용합니다. Azure Data Lake Store와 관련된 복사 작업 속성은 [복사 작업 속성](#copy-activity-properties) 섹션을 참조하세요. 원본 또는 싱크로 데이터 저장소를 사용하는 방법에 대한 자세한 내용을 보려면 데이터 저장소에 대한 이전 섹션의 링크를 클릭하세요.

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구/API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 Data Factory 엔터티를 정의합니다. 다른 곳에서 Azure Data Lake Store로 또는 그 반대로 데이터를 복사하는 데 사용되는 Data Factory 엔터티의 JSON 정의가 포함된 샘플은 이 문서의 [JSON 예](#json-examples-for-copying-data-to-and-from-data-lake-store) 섹션을 참조하세요.

다음 섹션에서는 Data Lake Store에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성
연결된 서비스는 데이터 저장소를 데이터 팩터리에 연결합니다. Data Lake Store 데이터를 데이터 팩터리에 연결하는 **AzureDataLakeStore** 형식의 연결된 서비스를 만듭니다. 다음 표에서는 Data Lake Store 연결된 서비스와 관련된 JSON 요소에 대해 설명합니다. 서비스 주체와 사용자 자격 증명 인증 중에서 선택할 수 있습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| **type** | type 속성은 **AzureDataLakeStore**로 설정해야 합니다. | 예 |
| **dataLakeStoreUri** | Azure Data Lake Store 계정에 대한 정보. 이 정보는 `https://[accountname].azuredatalakestore.net/webhdfs/v1` 또는 `adl://[accountname].azuredatalakestore.net/` 형식 중 하나를 사용합니다. | 예 |
| **subscriptionId** | Data Lake Store 계정이 속하는 Azure 구독 ID. | 싱크에 필요 |
| **resourceGroupName** | Data Lake Store 계정이 속하는 Azure 리소스 그룹 이름. | 싱크에 필요 |

### <a name="service-principal-authentication-recommended"></a>서비스 주체 인증(권장)
서비스 주체 인증을 사용하려면 Azure AD(Azure Active Directory)에서 애플리케이션 엔터티를 등록한 후 Data Lake Store에서 액세스 권한을 부여합니다. 자세한 단계는 [서비스 간 인증](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)을 참조하세요. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.
* 애플리케이션 UI
* 애플리케이션 키
* 테넌트 ID

> [!IMPORTANT]
> Azure Data Lake Store에서 서비스 주체에게 적절한 권한을 부여해야 합니다.
>- **Data Lake Store를 원본으로 사용하려면** 적어도 **읽기 + 실행** 데이터 액세스 권한을 부여하여 폴더의 내용을 나열하고 복사하거나 **읽기** 권한을 부여하여 단일 파일을 복사합니다. 계정 수준 액세스 제어가 필요하지 않습니다.
>- **Data Lake Store를 싱크로 사용하려면** 적어도 **쓰기 + 실행** 데이터 액세스 권한을 부여하여 폴더에서 자식 항목을 만듭니다. Azure IR을 사용하여 복사를 수행하는 경우(클라우드의 소스와 싱크 모두) Data Factory가 Data Lake Store의 지역을 감지하기 위해 계정 액세스 제어(IAM)에서 적어도 **읽기 권한자** 역할을 부여합니다. 이 IAM 역할을 방지하려면 복사 작업에서 Data Lake Store의 위치를 사용하여 [executionLocation을 지정](data-factory-data-movement-activities.md#global)합니다.
>- **복사 마법사를 사용하여 파이프라인을 작성**하는 경우 계정 액세스 제어(IAM)에서 최소한 **읽기 권한자** 역할을 부여합니다. 또한 Data Lake Store 루트("/") 및 해당 자식에 대한 **읽기+실행** 권한 이상을 부여해야 합니다. 그렇지 않으면 "제공된 자격 증명이 유효하지 않습니다." 메시지가 표시될 수 있습니다.

다음 속성을 지정하여 서비스 주체 인증을 사용합니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| **servicePrincipalId** | 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| **servicePrincipalKey** | 애플리케이션의 키를 지정합니다. | 예 |
| **테넌트** | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | 예 |

**예제: 서비스 주체 인증**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>사용자 자격 증명 인증
또는 아래 속성을 지정하여 사용자 자격 증명 인증을 사용하여 Data Lake Store로 데이터를 복사하거나 Data Lake Store에서 다른 위치로 복사할 수 있습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| **권한 부여** | Data Factory 편집기에서 **권한 부여** 단추를 클릭하고 자격 증명을 입력합니다. 그러면 자동 생성된 authorization URL이 이 속성에 할당됩니다. | 예 |
| **sessionId** | OAuth 권한 부여 세션에서 가져온 OAuth 세션 ID입니다. 각 세션 ID는 고유하고 한 번만 사용될 수 있습니다. 이 설정은 Data Factory 편집기를 사용하는 경우 자동으로 생성됩니다. | 예 |

> [!IMPORTANT]
> Azure Data Lake Store에서 사용자에게 적절한 권한을 부여해야 합니다.
>- **Data Lake Store를 원본으로 사용하려면** 적어도 **읽기 + 실행** 데이터 액세스 권한을 부여하여 폴더의 내용을 나열하고 복사하거나 **읽기** 권한을 부여하여 단일 파일을 복사합니다. 계정 수준 액세스 제어가 필요하지 않습니다.
>- **Data Lake Store를 싱크로 사용하려면** 적어도 **쓰기 + 실행** 데이터 액세스 권한을 부여하여 폴더에서 자식 항목을 만듭니다. Azure IR을 사용하여 복사를 수행하는 경우(클라우드의 소스와 싱크 모두) Data Factory가 Data Lake Store의 지역을 감지하기 위해 계정 액세스 제어(IAM)에서 적어도 **읽기 권한자** 역할을 부여합니다. 이 IAM 역할을 방지하려면 복사 작업에서 Data Lake Store의 위치를 사용하여 [executionLocation을 지정](data-factory-data-movement-activities.md#global)합니다.
>- **복사 마법사를 사용하여 파이프라인을 작성**하는 경우 계정 액세스 제어(IAM)에서 최소한 **읽기 권한자** 역할을 부여합니다. 또한 Data Lake Store 루트("/") 및 해당 자식에 대한 **읽기+실행** 권한 이상을 부여해야 합니다. 그렇지 않으면 "제공된 자격 증명이 유효하지 않습니다." 메시지가 표시될 수 있습니다.

**예제: 사용자 자격 증명 인증**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>토큰 만료
**권한 부여** 단추를 사용하여 생성한 권한 부여 코드는 특정 시간 후에 만료됩니다. 다음과 같은 메시지는 인증 토큰이 만료되었음을 의미합니다.

자격 증명 작업 오류: invalid_grant - AADSTS70002: 자격 증명의 유효성 검사 오류. AADSTS70008: 제공된 액세스 권한 부여가 만료되었거나 해지됩니다. 추적 ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 상관관계 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 타임스탬프: 2015-12-15 21-09-31Z.

다음 표에는 다양한 유형의 사용자 계정에 대한 만료 시간이 나와 있습니다.

| 사용자 유형 | 다음 시간 후에 만료 |
|:--- |:--- |
| Azure Active Directory에서 관리되지 *않는* 사용자 계정(예: @hotmail.com 또는 @live.com) |12시간 |
| Azure Active Directory에서 관리되는 사용자 계정 |마지막 조각이 실행되고 14일 후 <br/><br/>OAuth 기반 연결된 서비스를 기반으로 하는 조각이 14일마다 한 번 이상 실행된 경우 90일 |

토큰 만료 시간 전에 암호를 변경하면, 토큰이 즉시 만료됩니다. 이 섹션에 나와 있는 오류가 표시됩니다.

토큰이 만료되면 **권한 부여** 단추를 사용하여 계정을 다시 인증하고 연결된 서비스를 다시 배포할 수 있습니다. 다음 코드를 사용하여 프로그래밍 방식으로 **sessionId** 및 **authorization** 속성의 값을 생성할 수도 있습니다.


```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
코드에 사용되는 Data Factory 클래스에 대한 세부 정보는 [AzureDataLakeStoreLinkedService 클래스](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService 클래스](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) 및 [AuthorizationSessionGetResponse 클래스](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) 항목을 참조하세요. 코드에 사용되는 `WindowsFormsWebAuthenticationDialog` 클래스에 대해 `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll`의 버전 `2.9.10826.1824`에 대한 참조를 추가합니다.

## <a name="troubleshooting-tips"></a>문제 해결 팁

**증상:** Azure Data Lake Store**로** 데이터를 복사할 때 복사 활동이 실패하고 다음 오류가 발생합니다.

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**근본 원인:** 두 가지 가능한 이유는 다음과 같습니다.

1. Azure Data Lake Store 연결된 서비스에 지정된 `resourceGroupName` 및/또는 `subscriptionId`가 올바르지 않습니다.
2. 사용자 또는 서비스 주체에 필요한 권한이 없습니다.

**해결 방법:**

1. 연결된 서비스 `typeProperties`에 지정한 `subscriptionId` 및 `resourceGroupName`이 실제로 Data Lake 계정이 속한 항목인지 확인합니다.

2. Data Lake 계정에 있는 사용자 또는 서비스 주체에 하나 이상의 **읽기 권한자** 역할을 부여해야 합니다. 이렇게 하려면 다음을 수행합니다.

    1. Azure Portal -> Data Lake Store 계정으로 이동
    2. Data Lake Store의 블레이드에서 **액세스 제어(IAM)** 클릭
    3. **역할 할당 추가** 클릭
    4. **역할**을 **읽기 권한자**로 설정하고 복사 권한을 부여할 사용자 또는 서비스 주체 선택

3. 사용자 또는 서비스 주체에 **읽기 권한자** 역할을 부여하지 않으려면 Data Lake Store 위치의 복사 활동에서 [명시적으로 실행 위치를 지정](data-factory-data-movement-activities.md#global)할 수도 있습니다. 예제:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>데이터 세트 속성
Data Lake Store에서 입력 데이터를 표시할 데이터 세트를 지정하려면 데이터 세트의 **type** 속성을 **AzureDataLakeStore**로 설정합니다. 데이터 세트의 **linkedServiceName** 속성을 Data Lake Store 연결된 서비스의 이름으로 설정합니다. 데이터 세트 정의에 사용할 수 있는 JSON 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. **구조**, **가용성** 및 **정책**과 JSON의 데이터 세트 섹션은 모든 데이터 세트 형식(예: SQL Database, Azure Blob, Azure 테이블)에 대해 유사합니다. **typeProperties** 섹션은 데이터 세트의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치, 서식 등에 대한 정보를 제공합니다.

**AzureDataLakeStore** 형식의 데이터 세트에 대한 **typeProperties** 섹션에는 다음 속성이 있습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| **folderPath** |Data Lake Store의 컨테이너 및 폴더에 대한 경로입니다. |예 |
| **fileName** |Azure Data Lake Store에 있는 파일의 이름입니다. **fileName** 속성은 선택 사항이며 대/소문자를 구분합니다. <br/><br/>**fileName**을 지정하는 경우 활동(복사 포함)은 특정 파일에서 작동합니다.<br/><br/>**fileName**을 지정하지 않으면 복사는 입력 데이터 세트에 대한 **folderPath**에 모든 파일을 포함합니다.<br/><br/>때 **fileName** 출력 데이터 집합을 지정 하지 않으면 및 **preserveHierarchy** 형식으로 생성된 된 파일 이름이 활동 싱크에 지정 하지 않으면 `Data._Guid_.txt`합니다. 예를 들면 다음과 같습니다. Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |아닙니다. |
| **partitionedBy** |**partitionedBy** 속성은 선택 사항입니다. 시계열 데이터에 대한 동적 경로 및 파일 이름을 지정하는 데 사용할 수 있습니다. 예를 들어 **folderPath**는 매시간 데이터에 대한 매개 변수화됩니다. 자세한 내용과 예제는 partitionedBy 속성을 참조하세요. |아닙니다. |
| **format** | 다음 포맷 형식이 지원됩니다. **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** 및 **ParquetFormat** **format**의 **type** 속성을 이 값 중 하나로 설정합니다. 자세한 내용은 [Azure Data Factory에서 지원하는 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md) 문서의 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [JSON 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [ORC 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 파일을 “있는 그대로” 복사하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 `format` 섹션을 건너뜁니다. |아닙니다. |
| **compression** | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다. **Optimal** 및 **Fastest** 수준이 지원됩니다. 자세한 내용은 [Azure Data Factory에서 지원되는 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |아닙니다. |

### <a name="the-partitionedby-property"></a>partitionedBy 속성
**partitionedBy** 속성, Data Factory 함수 및 시스템 변수를 사용하여 시계열 데이터의 동적 **folderPath** 및 **fileName** 속성을 지정할 수 있습니다. 자세한 내용은 [Azure Data Factory - 함수 및 시스템 변수](data-factory-functions-variables.md) 문서를 참조하세요.


다음 예제에서 `{Slice}`는 Data Factory 시스템 변수인 `SliceStart`의 값(`yyyyMMddHH` 형식)으로 대체됩니다. 이름 `SliceStart`는 조각의 시작 시간을 가리킵니다. `folderPath` 속성은 `wikidatagateway/wikisampledataout/2014100103` 또는 `wikidatagateway/wikisampledataout/2014100104`과 같이 조각마다 다릅니다.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

다음 예제에서 `SliceStart`의 연도, 월, 일 및 시간은 `folderPath` 및 `fileName` 속성에서 사용하는 별도 변수로 추출됩니다.
```JSON
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
시계열 데이터 세트, 예약 및 조각에 대한 자세한 내용은 [Azure Data Factory의 데이터 세트](data-factory-create-datasets.md) 및 [Data Factory 예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요.


## <a name="copy-activity-properties"></a>복사 작업 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 유형에 따라 달라집니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

**AzureDataLakeStoreSource**는 **typeProperties** 섹션에서 다음 속성을 지원합니다.

| 자산 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| **recursive** |하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. |True(기본값), False |아닙니다. |

**AzureDataLakeStoreSink**는 **typeProperties** 섹션에서 다음 속성을 지원합니다.

| 자산 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| **copyBehavior** |복사 동작을 지정합니다. |<b>PreserveHierarchy</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><br/><b>FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 만들어집니다. 대상 파일은 자동 생성된 이름으로 만들어집니다.<br/><br/><b>MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 병합되는 파일 이름은 지정된 파일 또는 Blob 이름이 적용됩니다. 그렇지 않은 경우 파일 이름이 자동으로 생성됩니다. |아닙니다. |

### <a name="recursive-and-copybehavior-examples"></a>recursive 및 copyBehavior 예제
이 섹션에서는 다양한 recursive 및 copyBehavior 값 조합에 대한 복사 작업의 결과 동작을 설명합니다.

| recursive | copyBehavior | 결과 동작 |
| --- | --- | --- |
| true |preserveHierarchy |다음 구조를 가진 원본 폴더 Folder1의 경우:  <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같이 원본 폴더와 동일한 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |다음 구조를 가진 원본 폴더 Folder1의 경우:  <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles |다음 구조를 가진 원본 폴더 Folder1의 경우:  <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1, File2, File3, File4 및 File5의 파일 내용이 자동 생성된 파일 이름을 사용하는 파일 하나로 병합됩니다. |
| false |preserveHierarchy |다음 구조를 가진 원본 폴더 Folder1의 경우:  <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |
| false |flattenHierarchy |다음 구조를 가진 원본 폴더 Folder1의 경우: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |
| false |mergeFiles |다음 구조를 가진 원본 폴더 Folder1의 경우: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1과 File2의 파일 내용이 자동 생성된 파일 이름을 사용하는 파일 하나로 병합됩니다. File1에 대해 자동으로 생성된 이름<br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |

## <a name="supported-file-and-compression-formats"></a>지원되는 파일 및 압축 형식
자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md) 문서를 참조하세요.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Data Lake Store로/에서 데이터를 복사하는 JSON 예제
다음 예제에서는 샘플 JSON 정의를 제공합니다. 이러한 샘플 정의를 사용하여 [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)에서 파이프라인을 만들 수 있습니다. 이 예제에서는 Data Lake Store 및 Azure Blob Storage 간에 데이터를 복사하는 방법을 보여 줍니다. 그러나 임의의 원본에서 지원되는 싱크로 _직접_ 데이터를 복사할 수 있습니다. 자세한 내용은 [복사 작업을 사용하여 데이터 이동](data-factory-data-movement-activities.md) 문서에서 "지원되는 데이터 저장소 및 형식" 섹션을 참조하세요.

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>예제: Azure Blob Storage에서 Azure Data Lake Store로 데이터 복사
이 섹션의 예제 코드는 다음을 보여 줍니다.

* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
* [AzureDataLakeStore](#linked-service-properties) 형식의 연결된 서비스입니다.
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
* [AzureDataLakeStore](#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)입니다.
* [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) 및 [AzureDataLakeStoreSink](#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

이 예제에서는 Azure Blob Storage에서 Data Lake Store로 매시간 시계열 데이터가 복사되는 방법을 보여 줍니다.

**Azure Storage 연결된 서비스**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Azure Data Lake Store 연결된 서비스**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> 구성 세부 정보에서 [연결된 서비스 속성](#linked-service-properties) 섹션을 참조하세요.
>

**Azure Blob 입력 데이터 세트**

다음 예제에서 매시간 새 Blob에서 데이터가 선택됩니다(`"frequency": "Hour", "interval": 1`). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간의 년, 월 및 일 부분을 사용합니다. 파일 이름은 시작 시간의 시 부분을 사용합니다. `"external": true` 설정은 테이블이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Data Lake Store 출력 데이터 세트**

다음 예제에서는 Data Lake Store로 데이터를 복사합니다. 새 데이터가 Data Lake Store로 매시간 복사됩니다.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Blob 원본 및 Data Lake Store 싱크를 사용하는 파이프라인의 복사 작업**

다음 예제에서 파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함합니다. 복사 작업은 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 `source` 형식은 `BlobSource`로, `sink` 형식은 `AzureDataLakeStoreSink`로 설정되어 있습니다.

```json
{
    "name":"SamplePipeline",
    "properties":
    {
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [
            {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>예제: Azure Data Lake Store에서 Azure Blob으로 데이터 복사
이 섹션의 예제 코드는 다음을 보여 줍니다.

* [AzureDataLakeStore](#linked-service-properties) 형식의 연결된 서비스입니다.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
* [AzureDataLakeStore](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
* [AzureDataLakeStoreSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

이 코드는 Data Lake Store에서 Azure Blob로 매시간 시계열 데이터를 복사합니다.

**Azure Data Lake Store 연결된 서비스**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> 구성 세부 정보에서 [연결된 서비스 속성](#linked-service-properties) 섹션을 참조하세요.
>

**Azure Storage 연결된 서비스**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Data Lake 입력 데이터 세트**

이 예제에서 `"external"`을 `true`로 설정하면 테이블이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

```json
{
    "name": "AzureDataLakeStoreInput",
    "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
**Azure Blob 출력 데이터 세트:**

다음 예제에서 데이터는 매시간 새 Blob에 기록됩니다(`"frequency": "Hour", "interval": 1`). Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간의 년, 월, 일 및 시 부분을 사용합니다.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Azure Data Lake Store 원본 및 Blob 싱크를 사용하는 파이프라인의 복사 작업**

다음 예제에서 파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함합니다. 복사 작업은 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 `source` 형식은 `AzureDataLakeStoreSource`로, `sink` 형식은 `BlobSink`로 설정되어 있습니다.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureDataLakeStoreInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
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
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

복사 작업 정의에서 원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑할 수 있습니다. 자세한 내용은 [Azure Data Factory에서 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝
복사 작업 성능에 영향을 주는 요소 및 최적화하는 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md) 문서를 참조하세요.
