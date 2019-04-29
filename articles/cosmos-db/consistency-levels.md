---
title: Azure Cosmos DB의 일관성 수준
description: Azure Cosmos DB에는 최종 일관성, 가용성 및 대기 시간을 절충하여 조정하는 데 유용한 5가지 일관성 수준이 있습니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 836d36cc6f220bb544e0c7723506c624c5f9fc39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896535"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB의 일관성 수준

고가용성이나 짧은 대기 시간 또는 둘 다를 위해 복제에 의존하는 분산 데이터베이스는 읽기 일관성과 가용성, 대기 시간과 처리량을 근본적으로 절충합니다. 대부분의 상업적으로 사용할 수 있는 분산된 된 데이터베이스 요청 개발자가 두 가지 극단적인 일관성 모델을 선택할 수: *강력한* 일관성 및 *최종* 일관성. 합니다  [선형성](https://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) 강력한 일관성 모델은 데이터 프로그래밍 표준을 또는 합니다. 하지만 높은 대기 시간 (정상 상태)의 가격을 추가 하 고 (실패) 하는 동안 가용성을 감소 합니다. 반면, 최종 일관성 높은 가용성과 성능을 향상 시키려면를 제공 하지만 있도록 하드 응용 프로그램을 프로그래밍 합니다. 

Azure Cosmos DB는 두 가지의 극단적인 일관성 모델이 아니라 선택 사항 스펙트럼으로 데이터 일관성에 접근합니다. 강력한 일관성과 결과적인 일관성 스펙트럼의 끝에는 여러 가지 일관성 스펙트럼을 따라. 개발자는 정확 하 게 선택 하 고 높은 수준의 가용성 및 성능에 대해 세부적으로 균형을 이러한 옵션을 사용할 수 있습니다. 

Azure Cosmos DB를 사용하여 개발자는 일관성 스펙트럼에서 5개의 잘 정의된 일관성 모델에서 선택할 수 있습니다. , 완화 더 강한 모델은 포함 *강력한*를 *제한 된 부실*를 *세션*를 *일관 된 접두사*, 및 *최종* 일관성. 잘 정의 되어 있고 직관적인 모델과 특정 실제 시나리오에 사용할 수 있습니다. 각 모델을 제공 [가용성 및 성능 절충](consistency-levels-tradeoffs.md) 있으며는 Sla로 지원 합니다. 다음 이미지에는 스펙트럼이 구현으로 다양 한 일관성 수준을 보여 줍니다.

![스펙트럼으로 일관성](./media/consistency-levels/five-consistency-levels.png)

일관성 수준 지역 중립적 이며 지역 올 읽기 및 쓰기는 처리, Azure Cosmos 계정과 연결 된 지역 수에 관계 없이 모든 작업에 대 한 보장은 단일 계정에 구성 되어 있는지 여부 또는 또는 여러 쓰기 영역을 추가 합니다.

## <a name="scope-of-the-read-consistency"></a>읽기 일관성 범위

읽기 일관성은 파티션 키 범위 또는 논리 파티션 내의 단일 읽기 작업 범위에 적용됩니다. 읽기 작업은 원격 클라이언트 또는 저장 프로시저에서 실행할 수 있습니다.

## <a name="configure-the-default-consistency-level"></a>기본 일관성 수준 구성

언제든지 Azure Cosmos 계정의 기본 일관성 수준을 구성할 수 있습니다. 계정에 구성 된 기본 일관성 수준은 모든 Azure Cosmos 데이터베이스 및 해당 계정의 컨테이너에 적용 됩니다. 컨테이너 또는 데이터베이스에 대해 실행된 모든 읽기 및 쿼리는 기본적으로 지정된 일관성 수준을 사용합니다. 자세히 알아보려면 [기본 일관성 수준을 구성](how-to-manage-consistency.md#configure-the-default-consistency-level)하는 방법을 참조하세요.

## <a name="guarantees-associated-with-consistency-levels"></a>일관성 수준과 연결된 보장

Azure Cosmos DB에서 제공하는 포괄적인 SLA는 읽기 요청의 100%가 선택한 일관성 수준의 일관성 보장을 충족하도록 합니다. 일관성 수준과 연결된 모든 일관성 보증이 충족되면 읽기 요청이 일관성 SLA를 충족합니다. 5 가지 일관성 수준 사용 하 여 Azure Cosmos DB에서의 정확한 정의 [TLA + 사양 언어](https://lamport.azurewebsites.net/tla/tla.html) 에서 제공 되는 [tla-cosmos-azure](https://github.com/Azure/azure-cosmos-tla) GitHub 리포지토리. 

5가지 일관성 수준의 의미 체계는 여기에 설명되어 있습니다.

- **강력**: 강력한 일관성은 [선형화 가능성](https://aphyr.com/posts/313-strong-consistency-models) 보장을 제공합니다. 읽기를 통해 항목의 최신 커밋된 버전 반환이 보장됩니다. 클라이언트는 커밋되지 않은 쓰기 또는 부분 쓰기를 볼 수 없습니다. 사용자는 항상 최신 커밋 쓰기의 읽기가 보장됩니다.

- **제한된 부실** 읽기에서 일관적인 접두사 보장을 적용하도록 합니다. 읽기 쓰기 뒤 최대 지연 될 수 있습니다 *"K"* 버전 (예: "업데이트") 또는 항목의 *"T"* 시간 간격입니다. 즉, 제한 된 부실을 선택 하면 "부실"은 두 가지 방법으로 구성할 수 있습니다. 

  * 버전 번호 (*K*) 항목의
  * 시간 간격 (*T*) 쓰기 뒤 읽기 지연 될 수 있습니다는 여 

  제한된 부실은 "부실 창" 내부를 제외하고 전체 전역 순서를 제공합니다. 부실 창 내부 및 외부 지역에 단조 읽기 보증이 존재합니다. 강력한 일관성 제한 된 부실에서 제공 하는 것과 동일한 의미 체계를 가집니다. 부실 기간은 영(0)입니다. 제한된 부실을 시간 지연 원자성이라고도 합니다. 클라이언트 쓰기를 허용 하는 지역 내에서 읽기 작업을 수행 하는 경우 제한 된 부실 일관성은 보장은 강력한 일관성 보증 해당 동일 합니다.

- **세션**: 읽기에서 일관적인 접두사(단일 “쓰기” 세션 가정), 단조 읽기, 단조 쓰기, 쓰기 읽기 및 읽기 뒤 쓰기 보장을 적용하도록 합니다. 세션 일관성은 클라이언트 세션으로 범위가 지정됩니다.

- **일관적인 접두사**: 반환되는 업데이트는 간격 없이 모든 업데이트의 일부 접두사를 포함합니다. 일관 된 접두사 일관성 수준을 보장 읽기 쓰기 순서가 표시 되지 않습니다.

- **최종**: 읽기에 대한 순서 보장이 없습니다. 추가적인 쓰기가 없는 경우 복제본이 결국 통합됩니다.

## <a name="consistency-levels-explained-through-baseball"></a>야구를 통해 설명하는 일관성 수준

예를 들어 야구 게임 시나리오를 살펴보겠습니다. 야구 게임에서 점수를 나타내는 쓰기의 시퀀스를 가정합니다. 이닝별 실점은 [야구를 통해 복제된 데이터 일관성](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) 문서에 설명되어 있습니다. 이 가상 야구 게임은 현재 일곱 번째 이닝 중에 있습니다. 일곱 번째 이닝 스트레치입니다. 방문자 아래와 같이 뒤 2 ~ 5의 점수를 사용 하 여 됩니다.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **누계** |
| - | - | - | - | - | - | - | - | - | - | - |
| **원정팀** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **홈팀** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Azure Cosmos 컨테이너를 홈 팀 고 방문자에 대 한 실행된 합계를 포함합니다. 게임이 진행되는 동안 여러 가지 읽기 보장으로 인해 클라이언트가 다양한 득점을 읽을 수 있습니다. 다음 표에는 각각 5가지 일관성 보장이 있는 원정팀과 홈팀의 득점을 읽어 반환될 수 있는 전체 득점 집합이 나와 있습니다. 원정팀의 점수가 먼저 나열됩니다. 다른 가능한 반환 값은 쉼표로 구분됩니다.

| **일관성 수준** | **점수 (방문자를 홈)** |
| - | - |
| **강력** | 2-5 |
| **제한된 부실** | 최대 1회 지연된 득점: 2-3, 2-4, 2-5 |
| **세션** | <ul><li>작성자의 경우: 2-5</li><li> 작성자가 아닌 다른 사람의 경우: 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5</li><li>1-3을 읽은 후: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **일관적인 접두사** | 0-0, 0-1, 1-1, 1-2, 1-3, 2-3, 2-4, 2-5 |
| **최종** | 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5 |

## <a name="additional-reading"></a>추가 참조 자료

일관성 개념에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure Cosmos DB에서 제공하는 5가지 일관성 수준에 대한 고급 TLA+ 사양](https://github.com/Azure/azure-cosmos-tla)
- [야구를 통해 설명하는 복제된 데이터 일관성(비디오) 작성자: Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [야구를 통해 설명하는 복제된 데이터 일관성(백서) 작성자: Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [약하게 일관된 복제 데이터에 대한 세션 보장](https://dl.acm.org/citation.cfm?id=383631)
- [최신 분산 데이터베이스 시스템 디자인의 일관성 절충: CAP는 스토리의 일부일 뿐입니다.](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [최종 일관성 - 재고되었습니다.](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB의 일관성 수준에 대한 자세한 내용은 다음 문서를 참조하세요.

* [애플리케이션에 대한 올바른 일관성 수준 선택](consistency-levels-choosing.md)
* [Azure Cosmos DB API 간 일관성 수준](consistency-levels-across-apis.md)
* [다양한 일관성 수준의 가용성 및 성능 절충](consistency-levels-tradeoffs.md)
* [기본 일관성 수준 구성](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [기본 일관성 수준 재정의](how-to-manage-consistency.md#override-the-default-consistency-level)

