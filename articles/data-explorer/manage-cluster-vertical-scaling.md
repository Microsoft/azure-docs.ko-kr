---
title: 변경 수요에 맞게 Azure 데이터 탐색기에서 클러스터 수직 확장 (강화) 관리
description: 이 문서에서는 수요 변화에 따라 Azure 데이터 탐색기 클러스터를 확장 하 고 축소 하는 단계를 설명 합니다.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 80bbdf3a5d936719b06782cd78d56088b36cb21d
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985475"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>변경 수요에 맞게 Azure 데이터 탐색기에서 클러스터 수직 확장 (강화) 관리

클러스터 크기를 적절하게 조정하는 것은 Azure 데이터 탐색기의 성능에 중요합니다. 정적 클러스터 크기는 사용량 미달 또는 사용량 초과로 이어질 수 있으며, 둘 다 이상적인 경우는 아닙니다.

클러스터에 대한 수요는 절대 정확도로 예측할 수 없기 때문에 클러스터 크기를 *조정* 하 고 수요를 변경 하 여 용량과 CPU 리소스를 추가 하 고 제거 하는 것이 더 나은 방법입니다. 

Azure 데이터 탐색기 클러스터의 크기를 조정 하는 데는 두 가지 워크플로가 있습니다.

* [수평 확장](manage-cluster-horizontal-scaling.md)(확장 및 축소 라고도 함)
* 수직 크기 조정 (확장 및 축소 라고도 함)

이 문서에서는 수직 확장 워크플로를 설명 합니다.

## <a name="configure-vertical-scaling"></a>수직 크기 조정 구성

1. Azure Portal에서 Azure 데이터 탐색기 클러스터 리소스로 이동 합니다. **설정**아래에서 강화 **를 선택 합니다**.

1. **확장** 창에 클러스터에 사용할 수 있는 sku 목록이 표시 됩니다. 예를 들어 다음 그림에서는 4 개의 Sku만 사용할 수 있습니다.

    ![강화](media/manage-cluster-vertical-scaling/scale-up.png)

    Sku는 현재 SKU 이거나 클러스터가 있는 지역에서 사용할 수 없기 때문에 사용할 수 없습니다.

1. SKU를 변경 하려면 새 SKU를 선택 하 고 **선택**을 클릭 합니다.

> [!NOTE]
> * 수직 크기 조정 프로세스는 몇 분 정도 걸릴 수 있으며이 시간 동안 클러스터가 일시 중단 됩니다. 
> * 규모를 축소 하면 클러스터 성능을 저하 시킬 수 있습니다.
> * 가격은 클러스터의 가상 머신 및 Azure 데이터 탐색기 서비스 비용의 예상 비용입니다. 다른 비용은 포함 되지 않습니다. 전체 가격 정보는 Azure 데이터 탐색기 [cost 평가기](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) 페이지에서 예상 및 azure 데이터 탐색기 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-explorer/) 를 참조 하세요.

이제 Azure 데이터 탐색기 클러스터에 대해 수직 크기 조정을 구성 했습니다. 수평 크기 조정을 위한 다른 규칙을 추가 합니다. 클러스터 크기 조정 문제에 대한 지원이 필요한 경우 Azure Portal에서 [지원 요청을 여세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) .

## <a name="next-steps"></a>다음 단계

* [클러스터 수평 확장을 관리](manage-cluster-horizontal-scaling.md) 하 여 지정한 메트릭에 따라 인스턴스 수를 동적으로 확장 합니다.

* 이 문서에 따라 리소스 사용량을 모니터링 합니다. [메트릭을 사용 하 여 Azure 데이터 탐색기 성능, 상태 및 사용량을 모니터링](using-metrics.md)합니다.

