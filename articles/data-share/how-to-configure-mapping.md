---
title: Azure 데이터 공유에서 데이터 집합 매핑 구성
description: Azure 데이터 공유를 사용 하 여 수신 된 공유에 대한 데이터 집합 매핑을 구성 하는 방법에 대해 알아봅니다.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964246"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Azure 데이터 공유에서 수신 된 공유에 대한 데이터 집합 매핑을 구성 하는 방법

이 문서에서는 Azure 데이터 공유를 사용 하 여 수신 된 공유에 대한 데이터 집합 매핑을 구성 하는 방법을 설명 합니다. 데이터 공유 초대를 수락 했지만 "나중에 수락 및 구성"으로 설정 하거나 데이터를 현재 위치에서 공유 하는 경우이 작업을 수행 하는 것이 좋습니다. 사용자와 공유 되는 데이터에 대한 대상을 변경 해야 하거나 데이터를 SQL Server으로 받으려는 경우 데이터 집합 매핑을 구성할 수 있습니다. 

## <a name="navigate-to-a-received-data-share"></a>받은 데이터 공유로 이동

Azure 데이터 공유 서비스에서 받은 공유로 이동 하 여 **세부 정보** 탭을 선택 합니다. 

![데이터 집합 매핑](./media/dataset-mapping.png "데이터 집합 매핑") 

대상을 할당 하려는 데이터 집합 옆의 상자를 선택 합니다. 매핑 해제 **를 선택 하 여 기존** 매핑의 매핑을 해제 합니다. **+ 대상으로 매핑** 을 선택 하 여 새 대상 저장소를 선택 합니다. 

![대상에 매핑](./media/dataset-map-target.png "대상에 매핑") 

## <a name="select-a-new-target-store"></a>새 대상 저장소를 선택 합니다.

데이터를 가져올 대상 데이터 형식을 선택 합니다. 스냅숏 기반 공유의 경우 이전에 매핑한 저장소 계정에 이미 있는 모든 데이터는 자동으로 새 대상 저장소로 이동 하지 않습니다. 내부 공유의 경우 지정 된 위치에서 데이터 저장소를 선택 합니다. 위치는 데이터 공급자의 원본 데이터 저장소가 있는 Azure 데이터 센터입니다.

![대상 스토리지 계정](./media/dataset-map-target-sql.png "대상 스토리지") 

## <a name="select-a-file-format-sql-sources-only"></a>파일 형식 선택 (SQL 원본에만 해당)

원본 데이터가 SQL 기반 원본에 있는 경우에서 수신 되는 형식을 선택할 수 있습니다. 

![형식 선택](./media/sql-file-formats.png "SQL 파일 형식")

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요.



