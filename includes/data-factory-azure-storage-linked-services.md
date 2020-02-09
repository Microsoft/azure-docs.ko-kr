---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468089"
---
### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스
**Azure Storage 연결된 서비스**에서 **계정 키**를 사용하여 Azure Storage 계정을 Azure Data Factory에 연결할 수 있으며, 이렇게 하면 데이터 팩터리에 Azure Storage에 대한 전역 액세스가 제공됩니다. 다음 테이블은 Azure Storage 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type |형식 속성은 **AzureStorage** |예 |
| connectionString |connectionString 속성에 대한 Azure Storage에 연결하는 데 필요한 정보를 지정합니다. |예 |

저장소 계정 액세스 키를 검색 하는 방법에 대한 자세한 내용은 [저장소 계정 액세스 키 관리](../articles/storage/common/storage-account-keys-manage.md)를 참조 하세요.

**예:**  

```json
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

### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS 연결된 서비스
SAS(공유 액세스 서명)는 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 계정 액세스 키를 공유할 필요 없이 지정된 권한 집합을 사용하여 지정된 기간 동안 클라이언트에 스토리지 계정의 개체에 대한 제한된 권한을 부여할 수 있습니다. SAS는 스토리지 리소스에 인증된 액세스를 수행하는 데 필요한 모든 정보가 쿼리 매개 변수에 있는 URI입니다. SAS를 사용하여 스토리지 리소스에 액세스하려는 클라이언트는 SAS를 적절한 생성자 또는 메서드에 전달하면 됩니다. SAS에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../articles/storage/common/storage-sas-overview.md)를 참조하세요.

> [!IMPORTANT]
> Azure Data Factory는 이제 **서비스 SAS**만 지원하며 계정 SAS는 지원하지 않습니다. Azure Portal 또는 Storage Explorer에서 생성할 수 있는 SAS URL는 지원되지 않는 계정 SAS입니다.

> [!TIP]
> 아래의 PowerShell 명령을 실행하여 스토리지 계정에 대한 서비스 SAS를 생성할 수 있습니다(자리 표시자를 바꾸고 필요한 권한 부여).`$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Azure Storage SAS 연결된 서비스에서 SAS(공유 액세스 서명)을 사용하여 Azure Storage 계정을 Azure Data Factory에 연결할 수 있습니다. 이 서비스는 스토리지의 모든/특정 리소스(Blob/컨테이너)에 대해 제한된/시간 제한 액세스를 데이터 팩터리에 제공합니다. 다음 표는 Azure Storage SAS 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다. 

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type |형식 속성은 **AzureStorageSas** |예 |
| sasUri |BLOB, 컨테이너, 테이블 등의 Azure Storage 리소스에 공유 액세스 서명 URI를 지정합니다.  |예 |

**예:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

**SAS URI**를 만들 때 다음 사항을 고려하세요.  

* 데이터 팩터리에서 연결된 서비스(읽기, 쓰기, 읽기/쓰기)를 사용하는 방식에 따라 개체에 적절한 읽기/쓰기 **권한**을 설정합니다.
* **만료 시간**을 적절하게 설정합니다. Azure Storage 개체에 대한 액세스가 파이프라인의 활성 기간 내에 만료되지 않아야 합니다.
* 필요에 따라 올바른 컨테이너/BLOB 또는 테이블 수준에서 URI가 만들어져야 합니다. Azure BLOB에 대한 SAS URI를 사용하면 Data Factory 서비스에서 특정 BLOB에 액세스할 수 있습니다. Azure BLOB 컨테이너에 대한 SAS URI를 사용하면 Data Factory 서비스의 해당 컨테이너에서 BLOB을 반복할 수 있습니다. 나중에 더 많은/더 적은 개체에 대한 액세스를 제공하거나 SAS URI를 업데이트해야 하는 경우 연결된 서비스를 새 URI로 업데이트합니다.   

