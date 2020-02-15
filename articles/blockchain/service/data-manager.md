---
title: Azure Blockchain Service용 Blockchain Data Manager란?
description: Blockchain Data Manager는 블록체인 데이터를 캡처하고, 변환하고, Event Grid 항목으로 전송합니다.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: chroyal
ms.openlocfilehash: 78c93880007259267b26cf53e93de722be1c7323
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209446"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Azure Blockchain Service용 Blockchain Data Manager란?

Blockchain Data Manager는 Azure Blockchain Service 트랜잭션 데이터를 캡처, 변환 및 Azure Event Grid 항목으로 전송하여 안정적이고 확장 가능한 블록체인 원장을 Azure 서비스와 통합합니다.

대부분의 엔터프라이즈 블록체인 시나리오에서 블록체인 원장은 솔루션의 일부입니다. 예를 들어 자산을 한 엔터티에서 다른 엔터티로 전송하려면 트랜잭션을 제출하는 메커니즘이 필요합니다. 그런 다음, 트랜잭션이 발생하고 수락되었는지 확인하기 위해 원장 데이터를 읽는 메커니즘이 필요하며, 이에 따라 결과적인 상태 변경이 엔드투엔드 솔루션에 통합됩니다. 이 예에서 자산을 전송하기 위한 스마트 계약을 작성하는 경우 Blockchain Data Manager를 사용하여 오프체인 애플리케이션과 데이터 저장소를 통합할 수 있습니다. 자산 전송 예에서 자산이 블록체인에서 전송되면 Blockchain Data Manager에서 Event Grid를 통해 이벤트 및 속성 상태 변경을 전송합니다. 그런 다음, 여러 개의 가능한 이벤트 처리기를 Event Grid에 사용하여 블록체인 데이터를 오프체인으로 저장하거나 실시간으로 상태 변경에 대응할 수 있습니다.

Blockchain Data Manager는 캡처, 변환 및 전송이라는 세 가지 주요 기능을 수행합니다.

![Blockchain Data Manager 기능](./media/data-manager/functions.png)

## <a name="capture"></a>캡처

각 Blockchain Data Manager 인스턴스는 하나의 Azure Blockchain Service 멤버 트랜잭션 노드에 연결됩니다. 트랜잭션 노드에 액세스할 수 있는 사용자만 고객 데이터에 대한 적절한 액세스 제어를 보장하는 연결을 만들 수 있습니다. Blockchain Data Manager 인스턴스는 트랜잭션 노드에서 모든 원시 블록 및 원시 트랜잭션 데이터를 안정적으로 캡처하고 엔터프라이즈 워크로드를 지원하도록 크기를 조정할 수 있습니다.

## <a name="transform"></a>변환

Blockchain Data Manager를 사용하여 Blockchain Data Manager 내에서 스마트 계약 애플리케이션을 구성하여 이벤트 및 속성 상태를 디코딩할 수 있습니다. 스마트 계약을 추가하려면 ABI 계약과 바이트 코드를 제공합니다. Blockchain Data Manager는 스마트 계약 아티팩트를 사용하여 계약 주소를 디코딩하고 검색합니다. 블록체인 애플리케이션이 인스턴스에 추가되면 Blockchain Data Manager에서 스마트 계약을 컨소시엄에 배포할 때 스마트 계약 주소를 동적으로 검색하여 디코딩된 이벤트 및 속성 상태를 구성된 대상으로 보냅니다.

## <a name="deliver"></a>제공

Blockchain Data Manager는 지정된 Blockchain Data Manager 인스턴스에 대한 여러 개의 Event Grid 항목 아웃바운드 연결을 지원합니다. 블록체인 데이터는 단일 대상 또는 여러 대상으로 보낼 수 있습니다. Blockchain Data Manager를 사용하면 모든 블록체인 배포를 위한 확장 가능한 이벤트 기반 데이터 게시 솔루션을 빌드할 수 있습니다.

## <a name="configuration-options"></a>구성 옵션

Blockchain Data Manager는 솔루션의 요구 사항에 맞게 구성할 수 있습니다. 예를 들어 다음을 프로비저닝할 수 있습니다.

* Azure Blockchain Service 멤버에 대한 단일 Blockchain Data Manager 인스턴스
* Azure Blockchain Service 트랜잭션 노드당 Blockchain Data Manager 인스턴스. 예를 들어 프라이빗 트랜잭션 노드에는 기밀성을 유지하기 위해 자체의 Blockchain Data Manager 인스턴스가 있을 수 있습니다.
* 여러 출력 연결을 지원할 수 있는 Blockchain Data Manager 인스턴스. 하나의 Blockchain Data Manager 인스턴스를 사용하여 Azure Blockchain Service 멤버에 대한 모든 데이터 게시 통합 지점을 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Blockchain Service 멤버에 대한 [Blockchain Data Manager 인스턴스를 만들어 보세요](data-manager-portal.md).
