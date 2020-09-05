---
title: AKS 클러스터에 영향을 주는 Resource Health 이벤트 확인 (미리 보기)
description: Azure Resource Health를 사용 하 여 AKS 클러스터의 상태를 확인 합니다.
services: container-service
author: yunjchoi
ms.topic: troubleshooting
ms.date: 08/18/2020
ms.author: yunjchoi
ms.openlocfilehash: d14ed7e714eac175dc7a17bbccd2150f9bb01ef9
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752736"
---
# <a name="check-for-resource-health-events-impacting-your-aks-cluster-preview"></a>AKS 클러스터에 영향을 주는 Resource Health 이벤트 확인 (미리 보기)


AKS에서 컨테이너 워크 로드를 실행 하는 경우 작업의 가용성에 대 한 영향을 최소화 하기 위해 발생 하는 문제를 해결 하 고 문제를 해결할 수 있도록 합니다. [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview) 를 사용 하면 AKS 클러스터를 사용할 수 없게 될 수 있는 다양 한 상태 이벤트를 볼 수 있습니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="open-resource-health"></a>Resource Health 열기

### <a name="to-access-resource-health-for-your-aks-cluster"></a>AKS 클러스터에 대 한 Resource Health에 액세스 하려면:

- [Azure Portal](https://portal.azure.com)에서 AKS 클러스터로 이동 합니다.
- 왼쪽 탐색 영역에서 **Resource Health** 를 선택 합니다.

### <a name="to-access-resource-health-for-all-clusters-on-your-subscription"></a>구독의 모든 클러스터에 대 한 Resource Health에 액세스 하려면 다음을 수행 합니다.

- [Azure Portal](https://portal.azure.com) 에서 **Service Health** 을 검색 하 여 이동 합니다.
- 왼쪽 탐색에서 **Resource health** 를 선택 합니다.
- 구독을 선택 하 고 리소스 종류를 Azure Kubernetes Service (AKS)로 설정 합니다.

![리소스 상태-검사](./media/aks-resource-health/resource-health-check.png)

## <a name="check-the-health-status"></a>상태 확인

Azure Resource Health를 통해 Azure 리소스에 영향을 주는 서비스 문제를 진단 하 고 지원을 받을 수 있습니다. Resource Health는 리소스의 현재 및 과거 상태를 보고 하 고 사용자가 시작한 작업 또는 플랫폼 이벤트로 인 한 문제 인지를 확인 하는 데 도움이 됩니다.

Resource Health는 관리 클러스터에 대 한 신호를 수신 하 여 클러스터의 상태를 확인 합니다. AKS 클러스터의 상태를 검사 하 고 각 상태 신호에 필요한 작업을 보고 합니다. 이러한 신호는 자동 해결 문제, 계획 된 업데이트, 계획 되지 않은 상태 이벤트, 사용자가 시작한 작업으로 인 한 사용 불가 등의 범위에 해당 합니다. 이러한 신호는 *사용 가능*, 사용 *불가*, *알 수 없음*및 *저하*됨 상태를 사용 하 Azure Resource Health 여 분류 됩니다.

- **사용 가능**: 클러스터 상태에 영향을 주는 알려진 문제가 없으면 Resource Health에서 클러스터를 *사용 가능한*것으로 보고 합니다.

- **사용할 수 없음**: 클러스터의 상태에 영향을 주는 플랫폼 또는 비 플랫폼 이벤트가 있는 경우 Resource Health에서 클러스터를 *사용할 수 없는*것으로 보고 합니다.

- **알 수 없음**: 클러스터의 상태 메트릭에 대 한 일시적인 연결이 끊어지면 Resource Health에서 클러스터를 *알 수 없음*으로 보고 합니다.

- **저하**됨: 작업을 요구 하는 상태 문제가 있는 경우 Resource Health 클러스터를 *저하*된 것으로 보고 합니다.

각 상태에 표시 되는 사항에 대 한 자세한 내용은 [Resource Health 개요](https://docs.microsoft.com/azure/service-health/resource-health-overview#health-status)를 참조 하세요.

### <a name="view-historical-data"></a>기록 데이터 보기

**상태 기록** 섹션에서 지난 30 일 동안의 기록 Resource Health 정보를 볼 수도 있습니다.

## <a name="next-steps"></a>다음 단계

[AKS 진단을](https://docs.microsoft.com/azure/aks/concepts-diagnostics)사용 하 여 클러스터 문제를 추가로 해결 하려면 클러스터에 대 한 검사를 실행 합니다.
