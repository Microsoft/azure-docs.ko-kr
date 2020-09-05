---
title: Azure API에서 FHIR 용 데이터베이스 설정 구성
description: 이 문서에서는 Azure API에서 FHIR에 대 한 데이터베이스 설정을 구성 하는 방법을 설명 합니다.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: 652445a96acfa0358211d1d97e0fcf288989d6ba
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795782"
---
# <a name="configure-database-settings"></a>데이터베이스 설정 구성 

FHIR 용 Azure API는 데이터베이스를 사용 하 여 데이터를 저장 합니다. 기본 데이터베이스의 성능은 서비스를 프로 비전 하는 동안 선택 된 요청 단위 (작업 단위) 수 또는 서비스가 프로 비전 된 후 데이터베이스 설정에 따라 달라 집니다.

FHIR 용 Azure API는 기본 데이터베이스의 성능을 설정할 때 Cosmos DB의 RUs 개념을 활용 ( [Azure Cosmos DB의 요청 단위](https://docs.microsoft.com/azure/cosmos-db/request-units)참조). 

데이터베이스에 대 한 충분 한 시스템 리소스를 항상 사용할 수 있도록 처리량을 프로 비전 해야 합니다. 응용 프로그램에 필요한 RUs 수는 수행 하는 작업에 따라 달라 집니다. 작업의 범위는 단순 읽기 및 쓰기에서 보다 복잡 한 쿼리에 이르기까지 다양 합니다. 

> [!NOTE]
> 다른 작업은 다른 작업 수를 사용 하므로 응답 헤더의 모든 API 호출에서 사용 된 RUs의 실제 수를 반환 합니다. 이러한 방식으로 응용 프로그램에서 사용 하는 RUs의 수를 프로 파일링 할 수 있습니다.

## <a name="update-throughput"></a>처리량 업데이트

Azure Portal에서이 설정을 변경 하려면 FHIR 용 Azure API로 이동 하 여 데이터베이스 블레이드를 엽니다. 그런 다음, 성능 요구 사항에 따라 프로 비전 된 처리량을 원하는 값으로 변경 합니다. 최대 1만 r u/초까지 값을 변경할 수 있습니다. 더 높은 값이 필요한 경우 Azure 지원에 문의 하세요.

데이터베이스 처리량이 1만 r u/초 보다 크거나 데이터베이스에 저장 된 데이터가 50 GB 보다 큰 경우 클라이언트 응용 프로그램에서 연속 토큰을 처리할 수 있어야 합니다. 1만 r u/초 마다 또는 저장 된 데이터의 양이 50 GB를 초과 하는 경우 데이터베이스에 새 파티션이 만들어집니다. 여러 파티션이 연속 토큰을 사용 하 여 페이지 매김을 구현 하는 다중 페이지 응답을 만듭니다.

> [!NOTE] 
> 값이 높을수록 Azure API의 처리량이 증가 하 고 서비스의 비용이 높아집니다.

![구성 Cosmos DB](media/database/database-settings.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 용 Azure API에 대 한 RUs를 업데이트 하는 방법을 알아보았습니다. 다음으로 완전히 관리 되는 Azure API를 배포 합니다.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-portal-quickstart.md)
