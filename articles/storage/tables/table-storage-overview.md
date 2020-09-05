---
title: Table Storage 소개 - Azure의 개체 스토리지 | Microsoft Docs
description: Azure Table Storage, NoSQL 데이터 스토리지를 사용하여 클라우드에 구조화된 데이터를 저장합니다.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.devlang: dotnet
ms.topic: overview
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 2670d9ce568195fd97350bf678059ac89610422f
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236168"
---
# <a name="what-is-azure-table-storage-"></a>Azure Table 스토리지란? 

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage는 클라우드에 구조화된 NoSQL 데이터를 저장하는 서비스로, 스키마 없이 디자인된 키/특성 저장소를 제공합니다. Table Storage는 스키마가 없기 때문에 애플리케이션의 요구 사항이 변화함에 따라 데이터를 쉽게 적응시킬 수 있습니다. Table Storage 데이터에 대한 액세스는 많은 애플리케이션 유형에 대해 빠르고 비용 효율적이며 비슷한 양의 데이터일 때 일반적으로 전통적인 SQL에 비해 비용이 매우 낮습니다.

Table Storage를 사용하여 웹 애플리케이션의 사용자 데이터, 주소록, 디바이스 정보 및 서비스에 필요한 다른 유형의 메타데이터와 같은 유연한 데이터 세트을 저장할 수 있습니다. 테이블에 저장할 수 있는 엔터티 수에는 제한이 없으며, 스토리지 계정에 포함할 수 있는 테이블의 수에는 스토리지 계정의 최대 용량 한도까지 제한이 없습니다.

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>다음 단계

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.

* [.NET에서 Azure Table Storage 시작](../../cosmos-db/table-storage-how-to-use-dotnet.md)

* 사용 가능한 API에 대한 자세한 내용은 Table service 참조 설명서를 참조하세요.

    * [Storage Client Library for .NET 참조](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [REST API 참조](https://msdn.microsoft.com/library/azure/dd179355)
