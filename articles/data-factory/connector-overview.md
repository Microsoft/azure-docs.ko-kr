---
title: Azure Data Factory 커넥터 개요
description: Data Factory에서 지원 되는 커넥터에 대해 알아봅니다.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: a729d470cccd4121523c767ada9077a51361c061
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181982"
---
# <a name="azure-data-factory-connector-overview"></a>Azure Data Factory 커넥터 개요

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory는 복사, 데이터 흐름, 조회, 메타 데이터 가져오기 및 삭제 작업을 통해 다음과 같은 데이터 저장소와 형식을 지원 합니다. 각 데이터 저장소를 클릭 하 여 지원 되는 기능 및 해당 구성에 대해 자세히 알아보세요.

## <a name="supported-data-stores"></a>지원되는 데이터 저장소

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>추가 데이터 저장소와 통합

Azure Data Factory은 위에서 언급 한 목록 보다 더 광범위 한 데이터 저장소 집합에 도달할 수 있습니다. Azure Data Factory 기본 제공 커넥터 목록에 없는 데이터 저장소에서/로 데이터를 이동 해야 하는 경우 다음과 같은 몇 가지 확장 가능 옵션을 사용할 수 있습니다.
- 데이터베이스 및 데이터 웨어하우스의 경우 일반적으로 [일반 odbc 커넥터](connector-odbc.md)를 사용할 수 있는 해당 odbc 드라이버를 찾을 수 있습니다.
- SaaS 응용 프로그램:
    - RESTful Api를 제공 하는 경우 [일반 REST 커넥터](connector-rest.md)를 사용할 수 있습니다.
    - OData 피드가 있는 경우 [일반 odata 커넥터](connector-odata.md)를 사용할 수 있습니다.
    - SOAP Api를 제공 하는 경우 [일반 HTTP 커넥터](connector-http.md)를 사용할 수 있습니다.
    - ODBC 드라이버가 있는 경우 [일반 odbc 커넥터](connector-odbc.md)를 사용할 수 있습니다.
- 다른 경우에는 데이터를 ADF 지원 되는 데이터 저장소 (예: Azure Blob/File/FTP/SFTP/등)로 로드 하거나 표시할 수 있는지 확인 한 다음 ADF를 선택 합니다. [Azure Function](control-flow-azure-function-activity.md), [custom activity](transform-data-using-dotnet-custom-activity.md), [Databricks](transform-data-databricks-notebook.md) / [HDInsight](transform-data-using-hadoop-hive.md), [웹 활동](control-flow-web-activity.md)등을 통해 사용자 지정 데이터 로드 메커니즘을 호출할 수 있습니다.

## <a name="supported-file-formats"></a>지원되는 파일 형식

Azure Data Factory은 다음과 같은 파일 형식을 지원 합니다. 서식 기반 설정에 대 한 각 문서를 참조 하세요.

- [Avro 형식](format-avro.md)
- [이진 형식](format-binary.md)
- [Common Data Model 형식](format-common-data-model.md)
- [구분된 텍스트 형식](format-delimited-text.md)
- [델타 형식](format-delta.md)
- [Excel 형식](format-excel.md)
- [JSON 형식](format-json.md)
- [ORC 형식](format-orc.md)
- [Parquet 형식](format-parquet.md)
- [XML 형식](format-xml.md)

## <a name="next-steps"></a>다음 단계

- [복사 활동](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [활동 삭제](delete-activity.md)
