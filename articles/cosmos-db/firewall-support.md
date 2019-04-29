---
title: Azure Cosmos 계정에 대한 IP 방화벽
description: 방화벽을 지원하도록 IP 액세스 제어 정책을 사용하여 Azure Cosmos DB 데이터를 보호하는 법을 알아봅니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: c5c103fcb7f80533f6f34cb51fd661364d77d6ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60891954"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>Azure Cosmos DB의 IP 방화벽

계정에 저장된 데이터를 보호하기 위해 Azure Cosmos DB는 강력한 HMAC(해시 기반 메시지 인증 코드)를 활용하는 비밀 기반 권한 부여 모델을 지원합니다. 또한 Azure Cosmos DB는 인바운드 방화벽 지원을 위해 IP 기반 액세스 제어를 지원합니다. 이 모델은 기존 데이터베이스 시스템의 방화벽 규칙과 유사하며 사용자 계정에 추가 보안 수준을 제공합니다. 방화벽에서는 승인된 머신 및/또는 클라우드 서비스에서만 액세스할 수 있도록 Azure Cosmos 계정을 구성할 수 있습니다. 이러한 승인된 머신 및 서비스에서 Azure Cosmos 데이터베이스에 저장된 데이터에 액세스하려면 여전히 호출자가 유효한 권한 부여 토큰을 제공해야 합니다.

## <a id="ip-access-control-overview"></a>IP 액세스 제어 개요

기본적으로 요청에 유효한 권한 부여 토큰이 포함되어 있으면 인터넷에서 Azure Cosmos 계정에 액세스할 수 있습니다. IP 정책 기반 액세스 제어를 구성하려면 사용자가 지정된 Azure Cosmos 계정에 액세스하도록 허용된 클라이언트 IP 목록으로 포함할 IP 주소 또는 IP 주소 범위 집합을 CIDR(Classless Inter-Domain Routing) 형식으로 제공해야 합니다. 이 구성이 적용되면 이 허용되는 목록 이외의 머신에서 보내는 모든 요청은 403(금지됨) 응답을 받습니다. IP 방화벽을 사용하는 경우 계정에 액세스하려면 Azure Portal을 허용하는 것이 좋습니다. 데이터 탐색기를 사용하고 Azure Portal에 표시되는 계정에 대한 메트릭을 검색하려면 액세스 권한이 필요합니다. Azure 포털의 계정에 액세스할 수 있도록 하는 것 외에도 데이터 탐색기를 사용 하는 경우 방화벽 규칙에 현재 IP 주소를 추가 하 여 방화벽 설정을 업데이트 해야 하는 수도 있습니다. 방화벽 변경 내용을 전파 하는 데 최대 15 분까지 걸릴 수 있음을 note 합니다. 

서브넷 및 VNET 액세스 제어를 사용하여 IP 기반 방화벽을 결합할 수 있습니다. 결합하면 공용 IP가 있는 모든 원본에 대한 액세스 및/또는 VNET 내 특정 서브넷으로부터의 액세스를 제한할 수 있습니다. 서브넷 및 VNET 기반 액세스 제어 사용에 대해 자세히 알아보려면 [가상 네트워크에서 Azure Cosmos DB 리소스에 액세스](vnet-service-endpoint.md)를 참조하세요.

요약하면, Azure Cosmos 계정에 액세스하려면 권한 부여 토큰이 항상 필요합니다. IP 방화벽 및 VNET ACL(액세스 제어 목록)을 설정하지 않은 경우 권한 부여 토큰을 사용하여 Azure Cosmos 계정에 액세스할 수 있습니다. Azure Cosmos 계정에서 IP 방화벽과 VNET ACL 중 하나 또는 모두 설정되면 지정한(및 권한 부여 토큰 사용) 원본에서 발생하는 요청만 유효한 응답을 가져옵니다. 

## <a name="next-steps"></a>다음 단계

다음으로, 다음 문서를 사용하여 계정에 IP 방화벽 또는 VNET 서비스 엔드포인트를 구성할 수 있습니다.

* [Azure Cosmos 계정에 대해 IP 방화벽을 구성하는 방법](how-to-configure-firewall.md)
* [가상 네트워크에서 Azure Cosmos DB 리소스에 액세스](vnet-service-endpoint.md)
* [Azure Cosmos 계정에 대해 가상 네트워크 서비스 엔드포인트를 구성하는 방법](how-to-configure-vnet-service-endpoint.md)




