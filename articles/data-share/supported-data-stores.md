---
title: Azure 데이터 공유에서 지원 되는 데이터 저장소
description: Azure 데이터 공유 사용에 대해 지원 되는 데이터 저장소에 대해 알아봅니다.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: bb8b13e1141a8cb4610e15ed693e28042dd20d72
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89259016"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure 데이터 공유에서 지원 되는 데이터 저장소

Azure 데이터 공유는 다양 한 데이터 저장소에서 공유 하는 기능을 포함 하 여 유연한 개방형 데이터 공유 기능을 제공 합니다. 데이터 공급자는 특정 유형의 데이터 저장소에서 데이터를 공유할 수 있으며 데이터 소비자는 데이터를 받을 데이터 저장소를 선택할 수 있습니다. 

이 문서에서는 Azure 데이터 공유에서 지원 되는 다양 한 Azure 데이터 저장소 집합에 대해 알아봅니다. 데이터 공급자 및 데이터 소비자가 활용할 수 있는 데이터 저장소의 조합에 대 한 정보를 찾을 수도 있습니다. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Azure 데이터 공유에서 지원 되는 데이터 저장소는 무엇입니까? 

아래 표에서는 Azure 데이터 공유에 대해 지원 되는 데이터 원본을 자세히 설명 합니다. 

| 데이터 저장소 | 스냅숏 기반 공유 | 내부 공유 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |공개 미리 보기 | |
| Azure Synapse Analytics (이전의 Azure SQL DW) |공개 미리 보기 | |
| Azure Data Explorer | |✓ |

## <a name="data-store-support-matrix"></a>데이터 저장소 지원 매트릭스

Azure 데이터 공유는 데이터 저장소를 결정할 때 데이터 소비자에 게 데이터를 허용할 수 있는 유연성을 제공 합니다. 예를 들어 Azure SQL Database에서 공유 되는 데이터를 Azure Data Lake Store Gen2, Azure SQL Database 또는 Azure Synapse Analytics로 받을 수 있습니다. 고객은 수신 된 데이터 공유를 구성할 때 데이터를 받을 형식을 선택할 수 있습니다. 

아래 표에서는 데이터 소비자가 데이터 공유를 수락 하 고 구성할 때 제공 하는 다양 한 조합 및 선택 사항을 자세히 설명 합니다. 데이터 집합 매핑을 구성 하는 방법에 대 한 자세한 내용은 [데이터 집합 매핑을 구성 하는 방법](how-to-configure-mapping.md)을 참조 하세요.

| 데이터 저장소 | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics | Azure Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage | ✓ || ✓ ||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ ||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ ||
| Azure SQL Database | ✓ | | ✓ | ✓ | ✓ ||
| Azure Synapse Analytics (이전의 Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ ||
| Azure Data Explorer |||||| ✓ |

## <a name="share-from-a-storage-account"></a>저장소 계정에서 공유
Azure 데이터 공유는 Azure Data Lake Gen1 및 Azure Data Lake Gen2에서 파일, 폴더 및 파일 시스템을 공유할 수 있도록 지원 합니다. 또한 Azure Blob Storage에서 blob, 폴더 및 컨테이너를 공유할 수 있도록 지원 합니다. 블록 blob만 현재 지원 됩니다. 스냅숏 기반 공유에서 파일 시스템, 컨테이너 또는 폴더를 공유 하는 경우 데이터 소비자는 공유 데이터의 전체 복사본을 만들도록 선택 하거나 증분 스냅숏 기능을 활용 하 여 새 파일이 나 업데이트 된 파일만 복사할 수 있습니다. 증분 스냅숏은 파일의 마지막 수정 시간을 기반으로 합니다. 동일한 이름을 가진 기존 파일을 덮어씁니다.

자세한 내용은 [Azure Blob Storage의 데이터 공유 및 수신 및 Azure Data Lake Storage](how-to-share-from-storage.md) 를 참조 하세요.

## <a name="share-from-a-sql-based-source"></a>SQL 기반 원본에서 공유
Azure 데이터 공유는 Azure SQL Database 및 Azure Synapse Analytics (이전의 Azure SQL DW)에서 테이블 또는 뷰를 공유할 수 있도록 지원 합니다. 데이터 소비자는 데이터를 Azure Data Lake Storage Gen2 또는 Azure Blob Storage, csv 또는 parquet 파일, Azure SQL Database 및 Azure Synapse Analytics를 테이블로 수락 하도록 선택할 수 있습니다.

Azure Data Lake Store Gen2 또는 Azure Blob Storage에 데이터를 수락 하는 경우 전체 스냅숏은 이미 있는 경우 대상 파일의 내용을 덮어씁니다.
테이블에 데이터를 수신 하 고 대상 테이블이 아직 없는 경우 Azure 데이터 공유는 원본 스키마를 사용 하 여 SQL 테이블을 만듭니다. 동일한 이름을 가진 대상 테이블이 이미 있는 경우 삭제 되 고 최신 전체 스냅숏으로 덮어쓰여집니다. 증분 스냅숏은 현재 지원 되지 않습니다.

자세한 내용은 [Azure SQL Database 및 Azure Synapse Analytics에서 데이터 공유 및 수신](how-to-share-from-sql.md) 을 참조 하세요.

## <a name="share-from-azure-data-explorer"></a>Azure Data Explorer에서 공유
Azure 데이터 공유는 Azure 데이터 탐색기 클러스터에서 전체 데이터베이스를 공유 하는 기능을 지원 합니다. 데이터 공급자는 데이터베이스 또는 클러스터 수준에서 공유할 수 있습니다. 데이터베이스 수준에서 공유 하는 경우 데이터 소비자는 데이터 공급자가 공유 하는 특정 데이터베이스에만 액세스할 수 있습니다. 클러스터 수준에서 공유 하는 경우 데이터 소비자는 데이터 공급자가 만든 미래의 모든 데이터베이스를 포함 하 여 공급자의 클러스터에서 모든 데이터베이스에 액세스할 수 있습니다.

공유 데이터베이스에 액세스 하려면 데이터 소비자에 게 자체 Azure 데이터 탐색기 클러스터가 있어야 합니다. 데이터 소비자의 Azure 데이터 탐색기 클러스터는 데이터 공급자의 Azure 데이터 탐색기 클러스터와 동일한 Azure 데이터 센터에서 찾아야 합니다. 공유 관계가 설정 되 면 Azure 데이터 공유는 공급자와 소비자의 Azure 데이터 탐색기 클러스터 사이에 기호화 된 링크를 만듭니다. 원본 Azure 데이터 탐색기 클러스터에 batch 모드를 사용 하는 데이터 수집 몇 분 내에 대상 클러스터에 표시 됩니다.

자세한 내용은 [Azure에서 데이터 공유 및 수신 데이터 탐색기](/azure/data-explorer/data-share) 을 참조 하세요. 

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작 하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서를 계속 진행 합니다.
