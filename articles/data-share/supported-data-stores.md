---
title: Azure 데이터 공유에서 지원 되는 데이터 저장소
description: Azure 데이터 공유 사용에 대해 지원 되는 데이터 저장소에 대해 알아봅니다.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 5d4b1282b0a08657aea6f8a13aae7ed1fe49079b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964212"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure 데이터 공유에서 지원 되는 데이터 저장소

Azure 데이터 공유는 다양 한 데이터 저장소에서 공유 하는 기능을 포함 하 여 유연한 개방형 데이터 공유 기능을 제공 합니다. 데이터 공급자는 특정 유형의 데이터 저장소에서 데이터를 공유할 수 있으며 데이터 소비자는 데이터를 받을 데이터 저장소를 선택할 수 있습니다. 

이 문서에서는 Azure 데이터 공유에서 지원 되는 다양 한 Azure 데이터 저장소 집합에 대해 알아봅니다. 데이터 공급자 및 데이터 소비자가 활용할 수 있는 데이터 저장소의 조합에 대한 정보를 찾을 수도 있습니다. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Azure 데이터 공유에서 지원 되는 데이터 저장소는 무엇입니까? 

아래 표에서는 Azure 데이터 공유에 대해 지원 되는 데이터 원본을 자세히 설명 합니다. 

| 데이터 저장소 | 스냅숏 기반 공유 | 현재 항목 공유 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |공개 미리 보기 | |
| Azure Synapse Analytics (이전의 Azure SQL DW) |공개 미리 보기 | |
| Azure Data Explorer | |공개 미리 보기 |

## <a name="data-store-support-matrix"></a>데이터 저장소 지원 매트릭스

Azure 데이터 공유는 데이터 저장소를 결정할 때 데이터 소비자에 게 데이터를 허용할 수 있는 유연성을 제공 합니다. 예를 들어 Azure SQL Database에서 공유 되는 데이터를 Azure Data Lake Store Gen2, Azure SQL Database 또는 Azure Synapse Analytics로 받을 수 있습니다. 고객은 수신 된 데이터 공유를 구성할 때 데이터를 받을 형식을 선택할 수 있습니다. 

아래 표에서는 데이터 소비자가 데이터 공유를 수락 하 고 구성할 때 제공 하는 다양 한 조합 및 선택 사항을 자세히 설명 합니다. 데이터 집합 매핑을 구성 하는 방법에 대한 자세한 내용은 [데이터 집합 매핑을 구성 하는 방법](how-to-configure-mapping.md)을 참조 하세요.

|  | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓|
| Azure Data Lake Storage Gen1 | ✓ | | ✓|
| Azure Data Lake Storage Gen2 | ✓ | | ✓|
| Azure SQL Database | ✓ | | ✓| ✓| ✓|
| Azure Synapse Analytics (이전의 Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>저장소 계정에서 공유
Azure 데이터 공유는 Azure Data Lake Gen1 및 Azure Data Lake Gen2에서 파일, 폴더 및 파일 시스템을 공유할 수 있도록 지원 합니다. 또한 Azure Blob Storage에서 blob, 폴더 및 컨테이너를 공유할 수 있도록 지원 합니다. 스냅숏 기반 공유에서 폴더를 공유 하는 경우 데이터 소비자는 공유 데이터의 전체 복사본을 만들도록 선택 하거나, 증분 스냅숏 기능을 활용 하 여 새 파일이 나 업데이트 된 파일만 복사할 수 있습니다. 동일한 이름을 가진 기존 파일을 덮어씁니다.

## <a name="share-from-a-sql-based-source"></a>SQL 기반 원본에서 공유
Azure 데이터 공유는 Azure SQL Database 및 Azure Synapse Analytics (이전의 Azure SQL DW)에서 테이블 또는 뷰를 공유할 수 있도록 지원 합니다. 데이터 소비자는 csv 또는 parquet 파일로 Azure Data Lake Storage Gen2 또는 Azure Blob Storage에 데이터를 수락 하도록 선택할 수 있습니다. 전체 스냅숏은 대상 파일의 콘텐츠를 덮어씁니다. 또는 데이터 소비자가 SQL 테이블에 데이터를 허용할 수 있습니다. 데이터 소비자 쪽에서 대상 SQL 테이블을 사용할 수 없는 경우 Azure 데이터 공유는 원본 스키마를 사용 하 여 SQL 테이블을 만듭니다. Full snapshot 대상 SQL 테이블에 원본 테이블의 내용을 추가 합니다. 증분 스냅숏은 현재 지원 되지 않습니다.

## <a name="share-from-azure-data-explorer"></a>Azure 데이터 탐색기에서 공유
Azure 데이터 공유는 Azure 데이터 탐색기 클러스터에서 전체 데이터베이스를 공유 하는 기능을 지원 합니다. 데이터 공급자는 데이터베이스 또는 클러스터 수준에서 공유할 수 있습니다. 데이터베이스 수준에서 공유 하는 경우 데이터 소비자는 데이터 공급자가 공유 하는 특정 데이터베이스에만 액세스할 수 있습니다. 클러스터 수준에서 공유 하는 경우 데이터 소비자는 데이터 공급자가 만든 미래의 모든 데이터베이스를 포함 하 여 공급자의 클러스터에서 모든 데이터베이스에 액세스할 수 있습니다.

공유 데이터베이스에 액세스 하려면 데이터 소비자에 게 자체 Azure 데이터 탐색기 클러스터가 있어야 합니다. 데이터 소비자의 Azure 데이터 탐색기 클러스터는 데이터 공급자의 Azure 데이터 탐색기 클러스터와 동일한 Azure 데이터 센터에서 찾아야 합니다. 공유 관계가 설정 되 면 Azure 데이터 공유는 공급자와 소비자의 Azure 데이터 탐색기 클러스터 사이에 기호화 된 링크를 만듭니다.

Azure 데이터 탐색기는 일괄 처리 및 스트리밍의 두 가지 데이터 수집 모드를 지원 합니다. 공유 데이터베이스의 일괄 처리에서 받은 데이터는 데이터 소비자 측에서 몇 분에서 몇 분 사이에 표시 됩니다. 스트리밍에서 받은 데이터는 데이터 소비자 쪽에 표시 되는 데 최대 24 시간이 걸릴 수 있습니다. 

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요.
