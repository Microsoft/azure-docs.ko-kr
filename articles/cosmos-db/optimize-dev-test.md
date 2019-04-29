---
title: Azure Cosmos DB에서 개발 및 테스트 최적화
description: 이 문서에서는 Azure Cosmos DB에서 서비스의 개발 및 테스트를 위해 여러 가지 옵션을 무료로 제공하는 방법을 설명합니다.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: rimman
ms.openlocfilehash: 3bb39fe99e612fb19f12fd049ebb8f600e48373e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60930220"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Azure Cosmos DB에서 개발 및 테스트 비용 최적화

이 문서에서는 개발 및 테스트를 위해 Azure Cosmos DB를 무료로 사용하는 다양한 옵션에 대해 설명합니다.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB 에뮬레이터(로컬로 다운로드 가능한 버전)

[Azure Cosmos DB 에뮬레이터](local-emulator.md)는 Azure Cosmos DB 클라우드 서비스를 모방하는, 로컬로 다운로드 가능한 버전입니다. 네트워크 연결이 없어도 비용 발생 없이 Azure Cosmos DB API를 사용하는 코드를 작성하고 테스트할 수 있습니다. Azure Cosmos DB 에뮬레이터는 클라우드 서비스와 유사한, 개발 목적의 로컬 환경을 제공합니다. Azure 구독을 만들지 않고 로컬에서 애플리케이션을 개발하고 테스트할 수 있습니다. 애플리케이션을 클라우드에 배포할 준비가 되면 클라우드의 Azure Cosmos DB 엔드포인트에 연결하도록 연결 문자열을 업데이트하면 됩니다. 다른 수정은 필요하지 않습니다. 테스트 실행을 위해 Azure DevOps에서 [Azure Cosmos DB 에뮬레이터 빌드 작업을 사용하여 CI/CD 파이프라인을 설정](tutorial-setup-ci-cd.md)할 수도 있습니다. [Azure Cosmos DB 에뮬레이터](local-emulator.md) 문서를 방문하여 시작할 수 있습니다.

## <a name="try-azure-cosmos-db-for-free"></a>무료로 Azure Cosmos DB 사용해 보기

[Azure Cosmos DB 체험해 보기](https://azure.microsoft.com/try/cosmosdb/)는 데이터베이스 및 컬렉션을 만들고 클라우드에서 Azure Cosmos DB를 실험해 볼 수 있는 무료 환경입니다. Azure에 가입하거나 비용을 지불할 필요가 없습니다. Azure Cosmos DB 체험 계정은 제한된 시간(현재 30일) 동안 사용할 수 있습니다. 언제든지 계정을 갱신할 수 있습니다. Azure Cosmos DB 계정을 사용하면 쉽게 Azure Cosmos DB를 평가하고, 빠른 시작 또는 자습서를 통해 애플리케이션을 빌드 및 테스트할 수 있습니다. 비용 발생 없이 데모를 만들거나 단위 테스트를 수행할 수 있습니다. Azure Cosmos DB 체험 계정을 사용하면 턴키 전역 분산, SLA 및 일관성 모델을 비롯한 Azure Cosmos DB의 프리미엄 기능을 무료로 평가할 수 있습니다. 최대 25개의 Azure Cosmos 컨테이너와 10,000RU/s의 처리량을 사용하여 데이터베이스를 만들 수 있습니다. Azure 계정에 가입하거나 신용 카드를 사용하지 않고 애플리케이션 예제를 실행할 수 있습니다. Azure Cosmos DB 체험하기를 통해 몇 분 이내에 다중 지역 Azure Cosmos 계정을 만들고 해당 계정으로 앱을 실행할 수 있습니다. 시작하려면 [Azure Cosmos DB 체험해 보기](https://azure.microsoft.com/try/cosmosdb/)를 참조하세요.

## <a name="azure-free-account"></a>Azure 체험 계정

Azure Cosmos DB는 Azure 크레딧과 리소스를 일정 기간 동안 무료로 제공하는 [Azure 체험 계정](https://azure.microsoft.com/free)에 포함됩니다. Azure Cosmos DB와 관련해서 이 체험 계정은 1년 동안 5GB의 스토리지와 400RU의 프로비전된 처리량을 제공합니다. 이 환경을 통해 개발자는 비용 없이 Azure Cosmos DB의 기능을 쉽게 테스트하거나 다른 Azure 서비스와 통합할 수 있습니다. Azure 체험 계정과 함께 처음 30일 동안 사용할 수 있는 $200 크레딧을 받게 됩니다. 서비스 사용이 시작된 후에도 업그레이드를 선택하기 전에는 요금이 청구되지 않습니다. 시작하려면 [Azure 체험 계정](https://azure.microsoft.com/free) 페이지를 방문하세요.

## <a name="next-steps"></a>다음 단계

다음 문서를 사용하여 에뮬레이터 또는 Azure Cosmos DB 체험 계정 사용을 시작할 수 있습니다.

* [개발 및 테스트용으로 최적화](optimize-dev-test.md)에 대한 자세한 정보
* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [스토리지 비용 최적화](optimize-cost-storage.md)에 대한 자세한 정보
* [읽기 및 쓰기 비용 최적화](optimize-cost-reads-writes.md)에 대한 자세한 정보
* [쿼리 비용 최적화](optimize-cost-queries.md)에 대한 자세한 정보
* [다중 지역 Azure Cosmos 계정 비용 최적화](optimize-cost-regions.md)에 대한 자세한 정보

