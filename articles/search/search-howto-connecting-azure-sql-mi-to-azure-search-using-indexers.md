---
title: 검색 인덱싱을 위한 Azure SQL Managed Instance 연결
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 인덱서에서 SQL 관리 되는 인스턴스에 대 한 연결을 허용 하도록 공용 엔드포인트을 설정 합니다.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 65e483fd772e20daa73b465ea17dfa6ecde42233
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964892"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Azure Cognitive Search 인덱서에 SQL Managed Instance에 대 한 연결 구성

[인덱서를 사용 하 여 azure Cognitive Search에 Azure SQL Database 연결](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)에 설명 된 것 처럼 **SQL 관리** 되는 인스턴스에 대 한 인덱서 생성은 azure Cognitive Search에서 공용 엔드포인트을 통해 지원 됩니다.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>공용 엔드포인트을 사용 하 여 Azure SQL Managed Instance 만들기
**공용 엔드포인트 사용** 옵션을 선택 하 여 SQL Managed Instance를 만듭니다.

   ![공용 엔드포인트 사용](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "공용 엔드포인트 사용")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Azure SQL Managed Instance 공용 엔드포인트 사용
**보안** > **가상 네트워크** > **공용 엔드포인트** > **사용을 설정**하 여 기존 SQL Managed Instance에서 공용 엔드포인트을 사용 하도록 설정할 수도 있습니다.

   ![공용 엔드포인트 사용](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "공용 엔드포인트 사용")

## <a name="verify-nsg-rules"></a>NSG 규칙 확인
네트워크 보안 그룹에 Azure 서비스 로부터의 연결을 허용 하는 올바른 **인바운드 보안 규칙이** 있는지 확인 합니다.

   ![NSG 인바운드 보안 규칙](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "NSG 인바운드 보안 규칙")

> [!NOTE]
> 인덱서는 데이터를 읽기 위해 SQL Managed Instance를 공용 엔드포인트으로 구성 해야 합니다.
> 그러나 현재 규칙 (`public_endpoint_inbound`)을 다음 2 개의 규칙으로 바꿔 해당 공용 엔드포인트에 대 한 인바운드 액세스를 제한 하도록 선택할 수 있습니다.
>
> * `AzureCognitiveSearch` [service 태그](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) ("SOURCE" = `AzureCognitiveSearch`, "NAME" = `cognitive_search_inbound`)에서 인바운드 액세스를 허용 합니다.
>
> * 정규화 된 도메인 이름 (예: `<your-search-service-name>.search.windows.net`)을 ping 하 여 가져올 수 있는 검색 서비스의 IP 주소에서 인바운드 액세스를 허용 합니다. ("SOURCE" = `IP address`, "NAME" = `search_service_inbound`)
>
> 이러한 두 규칙 각각에 대해 "PORT" = `3342`, "PROTOCOL" = `TCP`, "DESTINATION" = `Any`, "ACTION" =를 설정 `Allow`

## <a name="get-public-endpoint-connection-string"></a>공용 엔드포인트 연결 문자열 가져오기
**공용 엔드포인트** 에 대 한 연결 문자열을 사용 해야 합니다 (포트 1433이 아닌 포트 3342).

   ![공용 엔드포인트 연결 문자열](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "공용 엔드포인트 연결 문자열")

## <a name="next-steps"></a>다음 단계
이제 구성에서 포털 또는 REST API를 사용 하 여 SQL Managed Instance를 Azure Cognitive Search 인덱서의 데이터 원본으로 지정할 수 있습니다. 자세한 내용은 [인덱서를 사용 하 여 Azure Cognitive Search에 Azure SQL Database 연결을](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) 참조 하세요.
