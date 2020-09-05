---
title: Azure 데이터 공유 가격 이해
description: Azure 데이터 공유 가격은 어떻게 작동 하는지 알아봅니다.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1c2c58e206a70a3801c712df3b5582409712d3c8
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136924"
---
# <a name="understand-azure-data-share-pricing"></a>Azure 데이터 공유 가격 이해

스냅숏 기반 공유의 경우 데이터 집합 스냅숏과 스냅숏 실행에 대 한 Azure 데이터 공유 요금이 청구 됩니다. 이 문서에서는 스냅숏 기록 정보를 사용 하 여 데이터 집합 스냅숏 및 스냅숏 실행을 계산 하는 방법을 설명 합니다. 현재 데이터 공급자는 데이터 집합 스냅숏과 스냅숏 실행에 대해 요금이 청구 됩니다.

## <a name="dataset-snapshot"></a>데이터 집합 스냅숏

데이터 집합 스냅숏은 원본에서 대상으로 데이터 집합을 이동 하는 작업입니다. 공유에 대 한 스냅숏을 만들 때 공유 내 각 데이터 집합의 스냅숏은 데이터 집합 스냅숏 작업입니다. 다음 단계를 수행 하 여 데이터 집합 스냅숏의 목록을 봅니다. 

1. Azure Portal에서 데이터 공유 리소스로 이동 합니다.

1. 보낸 공유 또는 받은 공유에서 공유를 선택 합니다.

1. **기록** 탭을 클릭 합니다.

1. 스냅숏의 시작 시간을 클릭 합니다.
 
    ![스냅숏 기록](./media/concepts/concepts-pricing/pricing-snapshot-history.png "스냅숏 기록") 

1. 데이터 집합 스냅숏 작업의 목록을 표시 합니다. 각 줄 항목은 데이터 집합 스냅숏 작업에 해당 합니다. 이 예제에는 두 개의 데이터 집합 스냅숏이 있습니다.

    ![데이터 집합 스냅숏 작업](./media/concepts/concepts-pricing/pricing-dataset-snapshot.png "데이터 집합 스냅숏 작업")

## <a name="snapshot-execution"></a>스냅숏 실행

스냅숏 실행은 데이터 집합을 원본에서 대상으로 이동 하는 데 필요한 리소스를 포함 합니다. 각 데이터 집합 스냅숏 작업의 경우 스냅숏 실행은 vCores의 수와 스냅숏 기간을 곱하여 계산 됩니다. 요금은 분 단위로 계산 되 고 반올림 됩니다. VCore 수는 원본-대상 쌍 및 데이터 패턴에 따라 동적으로 선택 됩니다. 다음 단계를 수행 하 여 스냅숏 시작 시간, 종료 시간 및 데이터 집합 스냅숏 작업에 사용 되는 vCores를 확인 합니다.

1. Azure Portal에서 데이터 공유 리소스로 이동 합니다.

1. 보낸 공유 또는 받은 공유에서 공유를 선택 합니다.

1. **기록** 탭을 클릭 합니다.

1. 스냅숏의 시작 시간을 클릭 합니다.

    ![스냅숏 기록](./media/concepts/concepts-pricing/pricing-snapshot-history.png "스냅숏 기록") 

1. 데이터 집합 스냅숏 작업의 상태를 클릭 합니다.

    ![데이터 집합 스냅숏 상태](./media/concepts/concepts-pricing/pricing-snapshot-status.png "데이터 집합 스냅숏 상태")

1. 이 데이터 집합 스냅숏 작업에 사용 되는 시작 시간, 종료 시간 및 vCores를 확인 합니다. 

    ![스냅숏 실행](./media/concepts/concepts-pricing/pricing-snapshot-execution.png "스냅숏 실행")

## <a name="next-steps"></a>다음 단계

- 최신 가격 정보 가져오기- [Azure 데이터 공유 가격 책정](https://azure.microsoft.com/pricing/details/data-share/)
- Azure 가격 계산기를 사용 하 여 비용 추정- [azure 가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/)
