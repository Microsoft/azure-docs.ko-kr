---
title: Azure SQL Database Managed Instance 관리 엔드포인트 검색 | Microsoft Docs
description: Azure SQL Database Managed Instance 관리 엔드포인트 공용 IP 주소를 가져오고 해당 기본 제공 방화벽 보호를 확인하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: b7eb9ecd6b94aad263346ad6b5c45b694e0bd46f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60700010"
---
# <a name="determine-the-management-endpoint-ip-address"></a>관리 엔드포인트 IP 주소 확인

Azure SQL Database Managed Instance 가상 클러스터에는 Microsoft에서 관리 작업에 사용하는 관리 엔드포인트가 포함됩니다. 관리 엔드포인트는 네트워크 수준의 기본 제공 방화벽과 애플리케이션 수준의 상호 인증서 확인으로 보호됩니다. 관리 엔드포인트의 IP 주소를 확인할 수 있지만 이 엔드포인트에 액세스할 수는 없습니다.

## <a name="determine-ip-address"></a>IP 주소 확인

Managed Instance 호스트를 `mi-demo.xxxxxx.database.windows.net`으로 가정해보겠습니다. 호스트 이름을 사용하여 `nslookup`을 실행합니다.

![내부 호스트 이름 확인](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

이제 `.vnet.` 세그먼트를 제거하고 강조된 이름에 대해 다른 `nslookup`을 수행합니다. 이 명령을 실행 하면 공용 IP 주소를 봅니다.

![공용 IP 주소 확인](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="next-steps"></a>다음 단계

Managed Instance 및 연결에 대한 자세한 내용은 [Azure SQL Database Managed Instance 연결 아키텍처](sql-database-managed-instance-connectivity-architecture.md)를 참조하세요.
