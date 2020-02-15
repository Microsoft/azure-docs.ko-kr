---
title: TTL(Time To Live)을 사용한 Azure Cosmos DB의 데이터 만료
description: TTL을 사용하여 Microsoft Azure Cosmos DB는 일정 기간 후에 시스템에서 문서를 자동으로 삭제하는 기능을 제공합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: 5407c38f33d167ff5114cd55878e3470e7248d71
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188710"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Azure Cosmos DB의 TTL (Time to Live) 

TTL ( **time To Live** ) 또는 TTL을 사용 하 Azure Cosmos DB는 특정 기간이 끝난 후 컨테이너에서 항목을 자동으로 삭제 하는 기능을 제공 합니다. 기본적으로 컨테이너 수준에서 TTL(Time to Live)을 설정하거나 항목별 기준으로 값을 재정의할 수 있습니다. 컨테이너 또는 항목 수준에서 TTL을 설정하면 Azure Cosmos DB는 마지막으로 수정한 시간으로부터 해당 시간이 지나면 이러한 항목이 자동으로 제거합니다. TTL(Time to Live) 값은 초 단위로 구성됩니다. TTL을 구성할 때 시스템은 클라이언트 응용 프로그램에서 명시적으로 실행 되는 삭제 작업이 없어도 TTL 값을 기준으로 만료 된 항목을 자동으로 삭제 합니다.

만료 된 항목 삭제는 사용자 요청에서 사용 되지 않은 요청 단위 인 왼쪽 위에 있는 [요청 단위](request-units.md)를 사용 하는 백그라운드 작업입니다. TTL이 만료 된 후에도 컨테이너가 요청으로 오버 로드 되 고 충분 한 사용 가능 공간이 없는 경우 데이터 삭제가 지연 됩니다. 삭제 작업을 수행 하는 데 사용할 수 있는 충분 한 RUs가 있는 경우 데이터가 삭제 됩니다. 데이터 삭제는 지연 되지만 TTL이 만료 된 후에는 모든 API에 의해 데이터가 반환 되지 않습니다.

## <a name="time-to-live-for-containers-and-items"></a>컨테이너 및 항목에 대한 TTL(Time to live)

Time to live 값은 초 단위로 설정 되며 항목이 마지막으로 수정 된 시간부터 델타로 해석 됩니다. TTL(Time to Live)은 컨테이너 또는 컨테이너 내 항목에서 설정할 수 있습니다.

1. **컨테이너에서 TTL(Time to Live)** (`DefaultTimeToLive` 사용하여 설정):

   - 누락(또는 null로 설정)되는 경우 항목이 자동으로 삭제되지 않습니다.

   - 있는 경우 값이 "-1"로 설정 되 고, 무한대와 같으며, 기본적으로 항목이 만료 되지 않습니다.

   - 있는 경우 값이 특정 숫자 *"n"* 으로 설정 되 고, 마지막으로 수정한 시간 이후 항목은 *"n"* 초 후에 만료 됩니다.

2. **항목에서 TTL(Time to Live)** (`ttl` 사용하여 설정):

   - 이 속성은 `DefaultTimeToLive`가 표시되고 부모 컨테이너에 대해 null로 설정되지 않은 경우에만 적용할 수 있습니다.

   - 표시되면 부모 컨테이너의 `DefaultTimeToLive` 값을 재정의합니다.

## <a name="time-to-live-configurations"></a>TTL(Time to Live) 구성

* 컨테이너에서 TTL을 *"n"* 으로 설정 하면 해당 컨테이너의 항목은 *n* 초 후에 만료 됩니다.  동일한 컨테이너에 고유한 ttl (time to live)을 지정 하는 항목이 있거나,-1로 설정 하 여 (만료 되지 않음), 일부 항목이 다른 숫자로 설정 된 TTL (time to live) 설정을 재정의 하는 경우 이러한 항목은 구성 된 TTL 값에 따라 만료 됩니다. 

* 컨테이너에서 TTL을 설정하지 않으면, 이 컨테이너의 항목에서 TTL(Time to Live)은 효과가 없습니다. 

* 컨테이너에서 TTL이 -1로 설정된 경우 TTL(Time to Live)이 n으로 설정된 이 컨테이너의 항목은 n초 후에 만료되고 나머지 항목은 만료되지 않습니다.

## <a name="examples"></a>예

이 섹션에서는 컨테이너와 항목에 할당 된 ttl (time to live) 값이 다른 몇 가지 예를 보여 줍니다.

### <a name="example-1"></a>예 1

컨테이너의 TTL이 null (DefaultTimeToLive = null)로 설정 되어 있습니다.

|항목에 대 한 TTL| 결과|
|---|---|
|ttl = null|    TTL을 사용할 수 없습니다. 항목이 만료 되지 않습니다 (기본값).|
|ttl = -1   |TTL을 사용할 수 없습니다. 항목이 만료 되지 않습니다.|
|ttl = 2000 |TTL을 사용할 수 없습니다. 항목이 만료 되지 않습니다.|


### <a name="example-2"></a>예제 2

컨테이너의 TTL이-1 (DefaultTimeToLive =-1)로 설정 되어 있습니다.

|항목에 대 한 TTL| 결과|
|---|---|
|ttl = null |TTL이 사용 됩니다. 항목이 만료 되지 않습니다 (기본값).|
|ttl = -1   |TTL이 사용 됩니다. 항목이 만료 되지 않습니다.|
|ttl = 2000 |TTL이 사용 됩니다. 항목은 2000 초 후에 만료 됩니다.|


### <a name="example-3"></a>예제 3

컨테이너의 TTL이 1000 (DefaultTimeToLive = 1000)로 설정 되어 있습니다.

|항목에 대 한 TTL| 결과|
|---|---|
|ttl = null|    TTL이 사용 됩니다. 항목은 1000 초 (기본값) 후에 만료 됩니다.|
|ttl = -1   |TTL이 사용 됩니다. 항목이 만료 되지 않습니다.|
|ttl = 2000 |TTL이 사용 됩니다. 항목은 2000 초 후에 만료 됩니다.|

## <a name="next-steps"></a>다음 단계

다음 문서에서 Ttl (Time to Live)을 구성 하는 방법에 대해 알아봅니다.

* [TTL(Time to Live) 구성 방법](how-to-time-to-live.md)
