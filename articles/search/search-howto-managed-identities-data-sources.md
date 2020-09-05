---
title: 관리 ID를 사용하여 데이터 원본에 대한 연결 설정(미리 보기)
titleSuffix: Azure Cognitive Search
description: 관리 ID를 사용하여 데이터 원본에 대한 인덱서 연결을 설정(미리 보기)하는 방법 알아보기
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: d303de23a04d183d0ca280c3b3591299d883adf7
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936591"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity-preview"></a>관리 ID를 사용하여 데이터 원본에 대한 인덱서 연결 설정(미리 보기)

> [!IMPORTANT] 
> 관리 id를 사용 하 여 데이터 원본에 대 한 연결을 설정 하는 기능은 현재 공개 미리 보기로 제공 됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다.

Azure Cognitive Search의 [인덱서](search-indexer-overview.md)는 데이터 원본에서 Azure Cognitive Search로 데이터를 가져오는 방법을 제공하는 크롤러입니다. 인덱서는 사용자가 만든 데이터 원본 개체에서의 데이터 원본 연결을 획득합니다. 데이터 원본 개체는 일반적으로 대상 데이터 원본에 대한 자격 증명을 포함합니다. 예를 들어 Blob 스토리지 컨테이너에서 데이터를 인덱싱하는 경우 데이터 원본 개체에 Azure Storage 계정 키가 포함될 수 있습니다.

대부분의 경우 데이터 원본 개체에 직접 자격 증명을 제공하는 것은 문제가 되지 않지만 다음과 같은 몇 가지 문제가 발생할 수 있습니다.
* 데이터 원본 개체를 만드는 코드에서 자격 증명을 안전하게 유지하려면 어떻게 해야 하나요?
* 내 계정 키 또는 암호가 손상되어 변경해야 하는 경우, 이제 내 인덱서가 데이터 원본에 다시 연결할 수 있도록 새 계정 키 또는 암호를 사용하여 데이터 원본 개체를 업데이트해야 합니다.

이러한 문제는 관리 ID를 사용하여 연결을 설정하면 해결할 수 있습니다.

## <a name="using-managed-identities"></a>관리 ID 사용

[관리 ID](../active-directory/managed-identities-azure-resources/overview.md)는 Azure AD(Azure Active Directory)에서 자동으로 관리 ID를 Azure 서비스에 제공하는 기능입니다. Azure Cognitive Search에서 이 기능을 사용하여 자격 증명을 포함하지 않는 연결 문자열을 사용하여 데이터 원본 개체를 만들 수 있습니다. 대신, 검색 서비스에 RBAC(역할 기반 액세스 제어)를 통해 데이터 원본에 대한 액세스 권한이 부여됩니다.

관리 ID를 사용하여 데이터 원본을 설정할 때 데이터 원본 자격 증명을 변경할 수 있으며, 인덱서는 여전히 데이터 원본에 연결할 수 있습니다. 계정 키를 포함하거나 Key Vault를 사용하여 계정 키를 검색하지 않고도 코드에서 데이터 원본 개체를 만들 수도 있습니다.

## <a name="limitations"></a>제한 사항

다음 데이터 원본은 관리 ID를 사용하는 인덱서 연결 설정을 지원합니다. 

* [Azure Blob 스토리지, Azure Data Lake Storage Gen2(미리 보기), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)

다음 기능은 현재 관리 ID를 사용하는 연결 설정을 지원하지 않습니다.
* 지식 저장소
* 사용자 지정 기술
 
## <a name="next-steps"></a>다음 단계

관리 ID를 사용하여 인덱서 연결을 설정하는 방법에 대해 자세히 알아봅니다.

* [Azure Blob 스토리지, Azure Data Lake Storage Gen2(미리 보기), Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)