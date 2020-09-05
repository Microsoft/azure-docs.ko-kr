---
title: Azure 센티널에 Azure 방화벽 데이터 연결
description: Azure 방화벽 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 167cfe6b8fb1394291866e51ec14ec91bf9035a1
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89183644"
---
# <a name="connect-data-from-azure-firewall"></a>Azure 방화벽에서 데이터 연결

> [!IMPORTANT]
> Azure 센티널의 Azure 방화벽 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다. 

Azure 방화벽 로그를 Azure 센티널에 연결 하 여 통합 문서에서 로그 데이터를 보고,이를 사용 하 여 사용자 지정 경고를 만들고,이를 통합 하 여 조사를 개선할 수 있습니다.

[Azure 방화벽 로그 모니터링](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics)에 대해 자세히 알아보세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

## <a name="connect-to-azure-firewall"></a>Azure 방화벽에 연결
    
1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터**를 선택 합니다.

1. 데이터 커넥터 갤러리에서 **Azure 방화벽** 을 선택한 다음 미리 보기 창에서 **커넥터 페이지 열기**  를 선택 합니다.

1. 연결 하려는 로그가 있는 모든 방화벽에서 **진단 로그** 를 사용 하도록 설정 합니다.

    1. [Azure 방화벽 리소스 >열기](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Network%2FazureFirewalls) 링크를 선택 합니다.

    1. **방화벽** 탐색 메뉴에서 **진단 설정**을 선택 합니다.

    1. 목록의 맨 아래에 있는 **+ 진단 설정 추가** 를 선택 합니다.

    1. **진단 설정** 화면에서 **진단 설정 이름** 필드에 이름을 입력 합니다.
    
    1. **Log Analytics 보내기** 확인란을 표시 합니다. 그 아래에는 두 개의 새 필드가 표시 됩니다. 관련 **구독** 및 **Log Analytics 작업 영역** 을 선택 합니다 (Azure 센티널이 있는 경우).

    1. 수집 하려는 로그를 포함 하는 규칙 유형의 확인란을 표시 합니다. **AzureFirewallApplicationRule** 및 **AzureFirewallNetworkRule**를 권장 합니다.

    1. 화면 위쪽에서 **저장** 을 선택 합니다.

1. Azure 방화벽 경고에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Azurediagnostics**를 검색 합니다.

> [!NOTE]
>
> 이 특정 데이터 커넥터를 사용 하 여 지난 2 주 동안 데이터가 수집 된 경우에만 연결 상태 표시기 (데이터 커넥터 갤러리의 색 줄무늬 및 데이터 형식 이름 옆의 연결 아이콘)가 *연결* 됨 (녹색)으로 표시 됩니다. 두 주가 데이터 수집 없이 전달 되 면 커넥터는 연결 끊김으로 표시 됩니다. 더 많은 데이터가 제공 되는 순간에 *연결* 된 상태는를 반환 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 azure 방화벽 로그를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
