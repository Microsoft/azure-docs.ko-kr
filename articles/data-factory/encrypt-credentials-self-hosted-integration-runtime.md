---
title: Azure Data Factory에서 자격 증명 암호화
description: 자체 호스팅 통합 런타임을 사용하는 컴퓨터에서 온-프레미스 데이터 저장소의 자격 증명을 암호화하고 저장하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 67ba2fadd5376997b528af4fcd2c5a666bb134a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443994"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Azure Data Factory에서 온-프레미스 데이터 저장소에 대한 자격 증명 암호화
자체 호스팅 통합 런타임을 사용하는 컴퓨터에서 온-프레미스 데이터 저장소(중요한 정보가 있는 연결된 서비스)의 자격 증명을 암호화하고 저장할 수 있습니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

자격 증명이 있는 JSON 정의 파일을 <br/>[**AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) cmdlet을 사용 하 여 암호화 된 자격 증명이 있는 출력 JSON 정의 파일을 생성 합니다. 그런 다음 업데이트된 JSON 정의를 사용하여 연결된 서비스를 만듭니다.

## <a name="author-sql-server-linked-service"></a>SQL Server 연결된 서비스 작성
다음 콘텐츠로 원하는 폴더에 **SqlServerLinkedService.json**이라는 JSON 파일을 만듭니다.  

파일을 저장하기 전에 `<servername>`, `<databasename>`, `<username>` 및 `<password>`를 SQL Server 값으로 바꿉니다. 그리고 `<integration runtime name>`을 통합 런타임의 이름으로 바꿉니다. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>자격 증명 암호화
온-프레미스 자체 호스팅 통합 런타임에서 JSON 페이로드의 중요 한 데이터를 암호화 하려면 **AzDataFactoryV2LinkedServiceEncryptedCredential**을 실행 하 고 json 페이로드를 전달 합니다. 이 cmdlet을 사용하면 자격 증명이 DPAPI를 사용하여 암호화되고 자체 호스팅 통합 런타임 노드에 로컬로 저장됩니다. 자격 증명에 대한 암호화 된 참조를 포함 하는 출력 페이로드는 다른 JSON 파일 (이 경우 ' Encryptedlinkedservice.json ')로 리디렉션될 수 있습니다.

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>암호화된 자격 증명이 있는 JSON 사용
이제 암호화된 자격 증명이 포함된 이전 명령의 출력 JSON 파일을 사용하여 **SqlServerLinkedService**를 설정합니다.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>다음 단계
데이터 이동에 대한 보안 고려 사항에 대한 정보는 [ 데이터 이동 보안 고려 사항 ](data-movement-security-considerations.md)을 참조하십시오.

