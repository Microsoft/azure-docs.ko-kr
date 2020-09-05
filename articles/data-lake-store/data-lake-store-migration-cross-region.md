---
title: Azure Data Lake Storage Gen1 지역 간 마이그레이션 | Microsoft Docs
description: 새 지역에서 사용할 수 있게 되는 Azure Data Lake Storage Gen1에 대 한 마이그레이션을 계획 하 고 완료할 때 고려해 야 할 사항에 대해 알아봅니다.
services: data-lake-store
documentationcenter: ''
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: stewu
ms.openlocfilehash: ba28d767c11c15b2dd70eeed2b39e13b084a7500
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88191354"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>지역 간 Azure Data Lake Storage Gen1 마이그레이션

Azure Data Lake Storage Gen1을 새 지역에서 사용할 수 있게 되므로 일회성 마이그레이션을 수행하여 새 지역을 이용할 수 있습니다. 마이그레이션을 계획하고 완료할 때 고려해야 할 사항에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. 자세한 내용은 [지금 무료 Azure 계정 만들기](https://azure.microsoft.com/pricing/free-trial/)를 참조하세요.
* **서로 다른 두 지역의 Data Lake Storage Gen1 계정** 자세한 내용은 [Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)을 참조하세요.
* **Azure Data Factory**. 자세한 내용은 [Azure Data Factory 소개](../data-factory/introduction.md)를 참조하세요.


## <a name="migration-considerations"></a>마이그레이션 고려 사항

먼저 Data Lake Storage Gen1의 데이터를 쓰고, 읽고, 또는 처리하는 애플리케이션에 가장 적합한 마이그레이션 전략을 식별합니다. 전략을 선택할 때 애플리케이션의 가용성 요구 사항 및 마이그레이션 중에 발생하는 작동 중단 시간을 고려합니다. 예를 들어, "리프트 앤 시프트" 클라우드 마이그레이션 모델을 사용하는 방법이 가장 간단할 수 있습니다. 이 방법에서는 모든 데이터를 새 지역에 복사하는 동안 기존 지역에서 애플리케이션을 일시 정지합니다. 복사 프로세스가 완료되면 새 지역에서 애플리케이션을 다시 시작하고 이전 Data Lake Storage Gen1 계정을 삭제합니다. 마이그레이션하는 동안 가동 중지 시간이 필요합니다.

가동 중지 시간을 줄이려면 즉시 새 지역에서 새 데이터를 수집하기 시작할 수 있습니다. 필요한 최소한의 데이터가 있는 경우 새 지역에서 애플리케이션을 실행합니다. 백그라운드에서 기존 Data Lake Storage Gen1 계정의 오래된 데이터를 새 지역의 새 Azure Data Lake Storage Gen1 계정으로 계속 복사할 수 있습니다. 이 방법을 사용하여 가동 중지 시간이 없이 새 지역으로 전환합니다. 오래된 데이터를 모두 복사했다면 이전 Data Lake Storage Gen1 계정을 삭제합니다.

마이그레이션을 계획할 때 고려해야 할 다른 중요한 세부 정보는 다음과 같습니다.

* **데이터 볼륨**. 데이터 볼륨(기가바이트 단위, 파일 및 폴더 수 등)은 마이그레이션에 필요한 시간 및 리소스에 영향을 줍니다.

* **Data Lake Storage Gen1 계정 이름**. 새 지역에 있는 새 계정 이름은 전역적으로 고유해야 합니다. 예를 들어, 미국 동부 2에 있는 이전 Data Lake Storage Gen1 계정 이름은 contosoeastus2.azuredatalakestore.net일 수 있습니다. 북유럽에 있는 Data Lake Storage Gen1 계정의 이름을 contosonortheu.azuredatalakestore.net으로 지정할 수 있습니다.

* **도구**. Azure Data Lake Storage Gen1 파일을 복사하는 데 [Azure Data Factory 복사 작업](../data-factory/connector-azure-data-lake-store.md)을 사용하는 것이 좋습니다. Data Factory는 데이터를 이동할 때 고성능 및 안정성을 지원합니다. Data Factory가 폴더 계층 구조와 파일의 내용만을 복사한다는 점을 기억합니다. 이전 계정에서 사용하는 모든 ACL(액세스 제어 목록)을 새 계정에 수동으로 적용해야 합니다. 최상의 시나리오에 대한 성능 목표를 비롯한 자세한 내용은 [복사 작업 성능 및 튜닝 가이드](../data-factory/copy-activity-performance.md)를 참조하세요. 더 신속하게 데이터를 복사하려면 추가 클라우드 데이터 이동 단위를 사용해야 합니다. AdlCopy와 같은 기타 도구는 지역 간에 데이터를 복사하도록 지원하지 않습니다.  

* **대역폭 요금** Azure 지역 외부에서 데이터를 전송하기 때문에 [대역폭 요금](https://azure.microsoft.com/pricing/details/bandwidth/)이 적용됩니다.

* **데이터에 대한 ACL** 파일 및 폴더에 ACL을 적용하여 새 지역에서 데이터를 보호합니다. 자세한 내용은 [Azure Data Lake Storage Gen1에 저장된 데이터 보안](data-lake-store-secure-data.md)을 참조하세요. 마이그레이션을 사용하여 ACL을 업데이트하고 조정하는 것이 좋습니다. 현재 설정과 유사한 설정을 사용할 수도 있습니다. Azure Portal, [PowerShell cmdlet](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission) 또는 SDK를 사용하여 모든 파일에 적용되는 ACL을 볼 수 있습니다.  

* **분석 서비스의 위치** 최상의 성능을 위해 Azure Data Lake Analytics 또는 Azure HDInsight와 같은 분석 서비스가 데이터와 동일한 지역에 있어야 합니다.  

## <a name="next-steps"></a>다음 단계
* [Azure Data Lake Storage Gen1 개요](data-lake-store-overview.md)
