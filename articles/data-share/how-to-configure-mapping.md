---
title: Azure 데이터 공유에서 데이터 집합 매핑 구성
description: Azure 데이터 공유를 사용 하 여 수신 된 공유에 대 한 데이터 집합 매핑을 구성 하는 방법에 대해 알아봅니다.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: eed3e8275400a3e677df53b9d62cf0e0bc70271c
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88257844"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Azure 데이터 공유에서 수신 된 공유에 대 한 데이터 집합 매핑을 구성 하는 방법

이 문서에서는 Azure 데이터 공유를 사용 하 여 수신 된 공유에 대 한 데이터 집합 매핑을 구성 하는 방법을 설명 합니다. 데이터를 받으려는 대상 데이터 저장소를 지정 하거나 대상 데이터 저장소를 변경 해야 하는 경우 데이터 집합 매핑을 구성할 수 있습니다.

## <a name="navigate-to-a-received-data-share"></a>받은 데이터 공유로 이동

Azure 데이터 공유 서비스에서 받은 공유로 이동 하 여 **데이터 집합** 탭을 선택 합니다. 

![데이터 집합 매핑](./media/dataset-mapping.png "데이터 집합 매핑") 

대상을 할당 하려는 데이터 집합 옆의 상자를 선택 합니다. **+ 대상으로 매핑** 을 선택 하 여 새 대상 저장소를 선택 합니다. 데이터 집합이 이미 매핑되고 대상 데이터 저장소를 변경 하려면 먼저 **매핑** 해제를 선택 합니다.

![대상에 매핑](./media/dataset-map-target.png "대상에 매핑") 

## <a name="select-a-target-store"></a>대상 저장소 선택

데이터를 가져올 대상 데이터 저장소 유형을 선택 합니다. 스냅숏 기반 공유의 경우 이전에 매핑한 저장소 계정에 이미 있는 모든 데이터는 자동으로 새 대상 저장소로 이동 하지 않습니다. 내부 공유의 경우 지정 된 위치에서 데이터 저장소를 선택 합니다. 위치는 데이터 공급자의 원본 데이터 저장소가 있는 Azure 데이터 센터입니다.

![대상 스토리지 계정](./media/dataset-map-target-sql.png "대상 스토리지") 

## <a name="select-a-file-format-sql-sources-only"></a>파일 형식 선택 (SQL 원본에만 해당)

원본 데이터가 SQL 기반 원본에서 온 것이 고이를 파일로 받으려는 경우 수신 되는 형식을 선택할 수 있습니다. 

![형식 선택](./media/sql-file-formats.png "SQL 파일 형식")

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요.



