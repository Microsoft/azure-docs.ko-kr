---
title: Azure Cognitive Search 서비스에 대 한 IP 방화벽 구성
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 서비스에 대 한 액세스를 제한 하도록 IP 제어 정책을 구성 합니다.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 3e8a94b6b9b71d2d71b634edd70ea4150652b143
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932800"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 IP 방화벽 구성

Azure Cognitive Search는 인바운드 방화벽 지원에 대 한 IP 규칙을 지원 합니다. 이 모델은 검색 서비스에 대 한 추가 보안 계층을 제공 합니다 .이는 Azure 가상 네트워크 보안 그룹에서 찾을 수 있는 IP 규칙과 비슷합니다. 이러한 IP 규칙을 사용 하 여 승인 된 컴퓨터 및/또는 클라우드 서비스 에서만 액세스할 수 있도록 검색 서비스를 구성할 수 있습니다. 이러한 승인 된 컴퓨터 및 서비스 집합에서 검색 서비스에 저장 된 데이터에 액세스 하려면 여전히 호출자가 유효한 권한 부여 토큰을 제공 해야 합니다.

> [!Important]
> Azure Portal 또는 [관리 REST API 버전 2020-03-13](/rest/api/searchmanagement/)을 사용 하 여 Azure Cognitive Search 서비스의 IP 규칙을 구성할 수 있습니다.

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Azure Portal를 사용 하 여 IP 방화벽 구성

Azure Portal에서 IP 액세스 제어 정책을 설정 하려면 Azure Cognitive Search 서비스 페이지로 이동 하 여 탐색 메뉴에서 **네트워킹** 을 선택 합니다. 끝점 네트워킹 연결은 **공용**이어야 합니다. 연결이 **개인**으로 설정 된 경우 개인 끝점을 통해서만 검색 서비스에 액세스할 수 있습니다.

![Azure Portal에서 IP 방화벽을 구성 하는 방법을 보여 주는 스크린샷](./media/service-configure-firewall/azure-portal-firewall.png)

Azure Portal은 CIDR 형식으로 IP 주소와 IP 주소 범위를 지정 하는 기능을 제공 합니다. CIDR 표기법의 예로는 8.8.8.0/24가 있습니다 .이는 8.8.8.0에서 8.8.8.255까지 범위의 Ip를 나타냅니다.

> [!NOTE]
> Azure Cognitive Search 서비스에 대해 IP 액세스 제어 정책을 사용 하도록 설정 하면 허용 되는 IP 주소 범위 목록 외부에 있는 컴퓨터에서 데이터 평면에 대 한 모든 요청이 거부 됩니다. IP 규칙을 구성 하면 Azure Portal의 일부 기능을 사용할 수 없습니다. 서비스 수준 정보를 보고 관리할 수 있지만 인덱스, 인덱서 및 기술 정의와 같은 서비스의 다양 한 구성 요소 및 인덱스에 대 한 포털 액세스는 보안상의 이유로 제한 됩니다.

### <a name="requests-from-your-current-ip"></a>현재 IP의 요청

개발을 간소화하기 위해 Azure Portal을 통해 클라이언트 머신의 IP를 식별하여 허용된 목록에 추가할 수 있습니다. 그러면 컴퓨터에서 실행 되는 앱이 Azure Cognitive Search 서비스에 액세스할 수 있습니다.

포털에서 클라이언트 IP 주소를 자동으로 검색 합니다. 컴퓨터 또는 네트워크 게이트웨이의 클라이언트 IP 주소일 수 있습니다. 프로덕션으로 작업을 수행 하기 전에이 IP 주소를 제거 해야 합니다.

현재 IP를 ip 목록에 추가 하려면 **클라이언트 ip 주소 추가**를 선택 합니다. 그런 다음 **저장**을 선택합니다.

![현재 IP를 허용 하도록 IP 방화벽 설정을 구성 하는 방법을 보여 주는 스크린샷](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>IP 액세스 제어 정책 관련 문제 해결

다음 옵션을 사용하여 IP 액세스 제어 정책 관련 문제를 해결할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

Azure Cognitive Search 서비스에 대해 IP 액세스 제어 정책을 사용 하도록 설정 하면 Azure Portal를 포함 하 여 허용 되는 IP 주소 범위 목록 외부에 있는 컴퓨터의 모든 요청이 차단 됩니다.  서비스 수준 정보를 보고 관리할 수 있지만 인덱스, 인덱서 및 기술 정의와 같은 서비스의 다양 한 구성 요소 및 인덱스에 대 한 포털 액세스는 보안상의 이유로 제한 됩니다. 

### <a name="sdks"></a>SDK

허용 목록에 없는 컴퓨터에서 SDK를 사용 하 여 Azure Cognitive Search service에 액세스 하는 경우에는 추가 세부 정보 없이 일반 **403 금지** 된 응답이 반환 됩니다. 계정에 대해 허용 되는 IP 목록을 확인 하 고 검색 서비스에 대 한 올바른 구성이 업데이트 되었는지 확인 하세요.

## <a name="next-steps"></a>다음 단계

개인 링크를 통해 검색 서비스에 액세스 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [Azure Cognitive Search에 대 한 보안 연결을 위한 개인 끝점 만들기](service-create-private-endpoint.md)