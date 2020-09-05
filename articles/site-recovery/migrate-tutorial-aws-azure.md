---
title: Azure Migrate를 사용하여 AWS VM을 Azure로 마이그레이션
description: 이 문서에서는 AWS 인스턴스를 Azure로 마이그레이션하는 옵션을 설명하고 Azure Migrate를 권장합니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 83d8106b9f206da9095c37b6179bdda8001b439c
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852772"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>AWS(Amazon Web Services)에서 Azure로 VM 마이그레이션

이 문서에서는 AWS(Amazon Web Services) 인스턴스를 Azure로 마이그레이션하는 옵션을 설명합니다.

## <a name="migrate-with-azure-migrate"></a>Azure Migrate로 마이그레이션

[Azure Migrate](../migrate/migrate-services-overview.md) 서비스를 사용하여 AWS EC2 인스턴스를 Azure로 마이그레이션하는 것이 좋습니다. Azure Migrate는 서버 마이그레이션을 위해 특별히 빌드되었습니다. Azure Migrate는 온-프레미스 머신을 Azure로 검색, 평가 및 마이그레이션하기 위한 중앙 허브를 제공합니다.

Azure Migrate를 사용하여 AWS 인스턴스를 마이그레이션하는 [방법을 알아봅니다](../migrate/tutorial-migrate-aws-virtual-machines.md). 


## <a name="migrate-with-site-recovery"></a>Site Recovery로 마이그레이션

Site Recovery는 마이그레이션이 아닌 재해 복구에만 사용해야 합니다.

이미 Azure Site Recovery를 사용하고 있으며 AWS 마이그레이션에 계속 사용하려는 경우에는 [물리적 머신의 재해 복구](physical-azure-disaster-recovery.md)를 설정하는 데 사용하는 것과 동일한 단계를 수행합니다.


> [!NOTE]
> 재해 복구를 위해 장애 조치(failover)를 실행하는 경우 마지막 단계로 장애 조치(failover)를 커밋합니다. AWS 인스턴스를 마이그레이션하는 경우 **커밋** 옵션은 관련이 없습니다. 대신 **마이그레이션 완료** 옵션을 선택합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> Azure Migrate에 대한 [일반적인 질문을 검토](../migrate/resources-faq.md)합니다.
