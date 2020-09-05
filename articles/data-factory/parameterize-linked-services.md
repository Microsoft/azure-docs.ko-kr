---
title: Azure Data Factory의 연결된 서비스 매개 변수화
description: Azure Data Factory에서 연결된 서비스를 매개 변수화하고 런타임에 동적 값을 전달하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/21/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: b3aadab1b4af80f98c57a279b69606a02846e996
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716846"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Azure Data Factory의 연결된 서비스 매개 변수화
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이제 연결된 서비스를 매개 변수화하고 런타임에 동적 값을 전달할 수 있습니다. 예를 들어 동일한 논리 SQL server의 다른 데이터베이스에 연결 하려는 경우 이제 연결 된 서비스 정의에서 데이터베이스 이름을 매개 변수화 할 수 있습니다. 이렇게 하면 논리 SQL server의 각 데이터베이스에 대해 연결 된 서비스를 만들 필요가 없습니다. 예를 들어 연결된 서비스 정의에서 *사용자 이름*과 같은 기타 속성을 매개 변수화할 수 있습니다.

Azure Portal 또는 프로그래밍 인터페이스에서 Data Factory UI를 사용 하 여 연결 된 서비스를 매개 변수화 할 수 있습니다.

> [!TIP]
> 암호니 비밀은 매개 변수화하지 않는 것이 좋습니다. 대신, Azure Key Vault의 모든 연결 문자열을 저장하고 *비밀 이름*을 매개 변수화합니다.

7분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>지원되는 데이터 저장소

이번에는 다음 데이터 저장소에 대 한 Data Factory UI에서 연결 된 서비스 매개 변수화가 지원 됩니다. 다른 모든 데이터 저장소의 경우, **연결** 탭에서 **코드** 아이콘을 선택하고 JSON 편집기를 사용하여 연결된 서비스를 매개 변수화할 수 있습니다.

- Amazon Redshift
- Azure Cosmos DB (SQL API)
- Azure Database for MySQL
- Azure SQL Database
- Azure Synapse Analytics(이전의 SQL DW)
- MySQL
- Oracle
- SQL Server
- 일반 HTTP
- 일반 REST

## <a name="data-factory-ui"></a>Data Factory UI

![연결된 서비스 정의에 동적 콘텐츠 추가](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![새 매개 변수 만들기](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
