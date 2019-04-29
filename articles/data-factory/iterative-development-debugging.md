---
title: Azure Data Factory에서 반복적인 개발 및 디버깅 | Microsoft Docs
description: Azure Portal에서 Data Factory 파이프라인을 반복적으로 개발하고 디버깅하는 방법을 알아봅니다.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: a8028fdde93d06f7b25bf9bd8b4ed5a560a35f83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686310"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Azure Data Factory를 사용한 반복적인 개발 및 디버깅

Azure Data Factory를 사용하면 Data Factory 파이프라인을 반복적인 개발하고 디버깅할 수 있습니다.

이 기능에 대한 소개 및 데모는 다음 비디오(8분)를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>반복 디버깅 기능
파이프라인 캔버스에서 **디버그** 기능을 사용하여 코드를 한 줄도 작성하지 않고 파이프라인을 만들고 테스트를 실행합니다.

![파이프라인 캔버스의 디버그 기능](media/iterative-development-debugging/iterative-development-image1.png)

파이프라인 캔버스의 **출력** 창에서 테스트 실행 결과를 봅니다.

![파이프라인 캔버스의 출력 창](media/iterative-development-debugging/iterative-development-image2.png)

테스트 실행이 성공한 후에는 파이프라인에 작업을 더 추가하고 반복적으로 디버깅을 계속합니다. 테스트 실행을 진행 중에도 **취소**할 수 있습니다.

![테스트 실행 취소](media/iterative-development-debugging/iterative-development-image3.png)

테스트 실행을 수행할 때 **디버그**를 선택하기 전에 데이터 팩터리에 변경 사항을 게시할 필요가 없습니다. 이 기능은 데이터 팩터리 워크플로를 업데이트하기 전에 변경 내용이 예상대로 작동하는지 확인하려는 시나리오에서 유용합니다.

> [!IMPORTANT]
> **디버그**를 선택하면 실제로 파이프라인이 실행됩니다. 예를 들어 파이프라인에 복사 작업이 포함된 경우 테스트 실행은 원본에서 대상으로 데이터를 복사합니다. 따라서 디버깅하는 경우 복사 작업 및 다른 작업에 테스트 폴더를 사용하는 것이 좋습니다. 파이프라인을 디버깅한 후 정상 작업에서 사용할 실제 폴더로 전환하십시오.

## <a name="visualizing-debug-runs"></a>디버그 실행 시각화

데이터 팩터리에 대해 진행 중인 모든 디버그 실행을 한 곳에서 시각화할 수 있습니다. 이렇게 하려면 페이지의 오른쪽 위에 있는 **디버그 실행 보기**를 선택합니다. 이 기능은 마스터 파이프라인이 하위 파이프라인에 대해 디버그 실행을 시작하는 상황에서 모든 활성 디버그 실행을 한 보기에서 확인하려는 시나리오에서 유용합니다.

![활성 디버그 실행 보기 아이콘 선택](media/iterative-development-debugging/view-debug-runs-image1.png)

![활성 디버그 실행 샘플 목록](media/iterative-development-debugging/view-debug-runs-image2.png)

## <a name="monitoring-debug-runs"></a>디버그 실행 모니터링

**디버그** 기능으로 시작된 테스트 실행은 **모니터** 탭의 목록에서 사용할 수 없습니다. **모니터** 탭에서 **지금 트리거**, **예약** 또는 **연속 창** 트리거로 트리거된 실행만 볼 수 있습니다. 파이프라인 캔버스의 **출력** 창에서 **디버그** 기능으로 시작된 마지막 테스트 실행을 볼 수 있습니다.

## <a name="setting-breakpoints-for-debugging"></a>디버깅을 위한 중단점 설정

Data Factory를 통해 파이프라인 캔버스의 특정 작업에 도달할 때까지 디버그할 수 있습니다. 테스트하려는 작업까지 중단점을 설정하고 **디버그**를 선택합니다. Data Factory는 파이프라인 캔버스의 중단점 작업까지만 테스트가 실행되도록 합니다. *Debug Until* 기능은 전체 파이프라인을 테스트하지 않고 파이프라인 내부의 일부 작업만 테스트하려는 경우에 유용합니다.

![파이프라인 캔버스의 중단점](media/iterative-development-debugging/iterative-development-image4.png)

중단점을 설정하려면 파이프라인 캔버스에서 요소를 선택합니다. *다음까지 디버그* 옵션이 요소의 오른쪽 위 모서리에 빈 빨간색 원으로 표시됩니다.

![선택된 요소에 중단점을 설정하기 전에](media/iterative-development-debugging/iterative-development-image5.png)

*다음까지 디버그* 옵션을 선택하면 중단점이 설정되었음을 나타내기 위해 채워진 빨간색 원으로 바뀝니다.

![선택된 요소에 중단점을 설정한 후](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>다음 단계
[Azure Data Factory에서 지속적인 통합 및 배포](continuous-integration-deployment.md)
