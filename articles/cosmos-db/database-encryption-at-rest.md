---
title: Azure Cosmos DB의 미사용 암호화
description: Azure Cosmos DB에서 미사용 데이터 암호화를 제공하는 방법 및 구현 방식을 알아봅니다.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 07d5aa752d6613f6733a44c9b34e48f537eb67ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60889587"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Azure Cosmos DB의 데이터 암호화 

미사용 암호화는 일반적으로 반도체 드라이브(SSD) 및 하드 디스크(HDD)와 같은 비휘발성 저장소 디바이스에서 데이터를 암호화하는 것을 말합니다. Cosmos DB는 해당 주 데이터베이스를 SSD에 저장합니다. 해당 미디어 첨부 파일 및 백업은 일반적으로 HDD로 백업되는 Azure Blob Storage에 저장됩니다. Cosmos DB에 대한 미사용 암호화가 릴리스됨에 따라 모든 데이터베이스, 미디어 첨부 파일 및 백업이 암호화됩니다. 이제 데이터는 전송 중(네트워크를 통해)과 미사용 시(비휘발성 저장소) 암호화되므로 종단 간 암호화가 가능합니다.

PaaS 서비스인 Cosmos DB는 사용하기가 매우 간편합니다. Cosmos DB에 저장된 모든 사용자 데이터는 미사용 및 전송 시 암호화되기 때문에 어떤 조치도 취할 필요가 없습니다. 또한 미사용 암호화가 기본적으로 "설정" 상태라는 것도 이러한 노력 중 하나입니다. 설정하거나 해제하는 데 사용되는 컨트롤이 없습니다. Microsoft는 [가용성 및 성능 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)를 지속적으로 충족하면서 이 기능을 제공합니다.

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 미사용 암호화 구현

저장 데이터 암호화는 보안 키 저장소 시스템, 암호화된 네트워크 및 암호화 API를 비롯한 수많은 보안 기술을 사용하여 구현되었습니다. 데이터를 암호 해독하고 처리하는 시스템은 키를 관리하는 시스템과 통신해야 합니다. 다이어그램에서는 암호화된 데이터의 저장소와 키 관리가 어떻게 구분되는지를 보여 줍니다. 

![디자인 다이어그램](./media/database-encryption-at-rest/design-diagram.png)

사용자 요청의 기본 흐름은 다음과 같습니다.
- 사용자 데이터베이스 계정은 이미 준비되어 있고 저장소 키는 관리 서비스 리소스 공급자에 대한 요청을 통해 검색됩니다.
- 사용자는 HTTPS/보안 전송을 통해 Cosmos DB에 대한 연결을 만듭니다(SDK가 세부 정보를 추상화함).
- 사용자는 이전에 만든 보안 연결을 통해 저장할 JSON 문서를 보냅니다.
- JSON 문서는 사용자가 인덱싱을 해제하지 않는다면, 인덱싱됩니다.
- JSON 문서와 인덱스 데이터 모두 보안 저장소에 기록됩니다.
- 데이터는 보안 저장소에서 주기적으로 읽어오고 Azure Encrypted Blob Store에 백업됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>Q: 스토리지 서비스 암호화를 사용하는 경우 Azure Storage 비용은 얼마나 늘어나나요?
A: 추가 비용은 없습니다.

### <a name="q-who-manages-the-encryption-keys"></a>Q: 암호화 키는 누가 관리하나요?
A: 키는 Microsoft에서 관리합니다.

### <a name="q-how-often-are-encryption-keys-rotated"></a>Q: 얼마나 자주 암호화 키가 순환되나요?
A: Microsoft에는 Cosmos DB가 따르는 암호화 키 순환에 대한 일련의 내부 지침이 있습니다. 특정 지침은 게시되지 않습니다. Microsoft는 내부 지침의 하위 집합으로 간주되고 개발자를 위한 유용한 모범 사례가 있는 [SDL(Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx)을 게시합니다.

### <a name="q-can-i-use-my-own-encryption-keys"></a>Q: 나만의 암호화 키를 사용할 수 있나요?
A: Cosmos DB는 PaaS 서비스로, Microsoft는 서비스의 사용 용이성을 높이기 위해 열심히 노력해 왔습니다. 이 질문이 PCI-DSS와 같은 규정 준수 요구 사항을 충족시키기 위한 프록시 질문으로 자주 제기되는 것을 확인했습니다. 이 기능을 구축하는 과정에서 준수 감사자와 협력하여 Cosmos DB를 사용하는 고객이 키를 직접 관리할 필요 없이 요구 사항을 충족할 수 있도록 했습니다.

### <a name="q-what-regions-have-encryption-turned-on"></a>Q: 암호화가 켜져 있는 Azure 지역은 어디인가요?
A: 모든 Azure Cosmos DB 지역에서 모든 사용자 데이터에 대해 암호화가 켜져 있습니다.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>Q: 암호화는 성능 대기 시간 및 처리량 SLA에 영향을 주나요?
A: 이제 기존의 모든 계정과 새 계정에 대해 미사용 암호화가 사용되므로 성능 SLA에 대한 영향이나 변경 내용은 없습니다. 최신 보장 내용을 보려면 [Cosmos DB에 대한 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) 페이지를 참조하세요.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>Q: 로컬 에뮬레이터가 미사용 암호화를 지원하나요?
A: 에뮬레이터는 독립 실행형 개발/테스트 도구이며 관리되는 Cosmos DB 서비스에서 사용되는 키 관리 서비스를 사용하지 않습니다. 중요한 에뮬레이터 테스트 데이터를 저장할 드라이브에서 BitLocker를 사용하도록 설정하는 것이 좋습니다. [에뮬레이터는 기본 데이터 디렉터리 변경을 지원](local-emulator.md)할 뿐만 아니라 잘 알려진 위치 사용을 지원합니다.

## <a name="next-steps"></a>다음 단계

Cosmos DB 보안 및 최신 개선 사항에 대한 개요는 [Azure Cosmos DB 데이터베이스 보안](database-security.md)을 참조하세요.
Microsoft 인증에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/support/trust-center/)를 참조하세요.
