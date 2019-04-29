---
title: Azure App Service - 동기화 네트워크 구성 | Microsoft Docs
description: 이 아티클에서는 Azure App Service 호스팅 계획에 대한 네트워크 구성을 동기화하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: sstein, bonova, carlrab
manager: digimobile
origin.date: 12/13/2018
ms.date: 04/29/2019
ms.openlocfilehash: 0d7920080fd61389741fbe785f5141003bef5251
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61314723"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Azure App Service 호스팅 계획에 대한 네트워킹 구성 동기화

[Azure Virtual Network와 앱을 통합](../app-service/web-sites-integrate-with-vnet.md)했더라도 발생할 수 있습니다. Managed Instance에 연결을 설정할 수 없습니다. 시도할 수 있는 방법은 서비스 계획에 대한 네트워킹 구성을 새로 고치는 것입니다.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>App Service 호스팅 계획에 대한 네트워킹 구성 동기화

이렇게 하려면 다음 단계를 수행하세요.  

1. 웹앱 App Service 계획으로 이동합니다.

   ![App Service 계획](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. **네트워킹**을 클릭한 다음, **관리하려면 여기 클릭**을 클릭합니다.

   ![서비스 계획 관리](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. **VNet**을 선택하고 **네트워크 동기화**를 클릭합니다.

   ![네트워크 동기화](./media/sql-database-managed-instance-sync-networking/sync.png)

4. 동기화가 완료될 때까지 대기합니다.
  
   ![동기화 완료](./media/sql-database-managed-instance-sync-networking/sync-done.png)

이제 Managed Instance에 연결을 다시 설정할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

- Managed Instance에 대해 VNet을 구성하는 방법에 대한 자세한 내용은 [Managed Instance VNet 아키텍처](sql-database-managed-instance-connectivity-architecture.md) 및 [기존 VNet을 구성하는 방법](sql-database-managed-instance-configure-vnet-subnet.md)을 참조하세요.
