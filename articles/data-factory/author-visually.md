---
title: 시각적 작성
description: Azure Data Factory에서 시각적 작성을 사용하는 방법에 알아봅니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 12/19/2019
ms.openlocfilehash: 09d4055ba98da2dd87efc9421402f2827a87ba16
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440926"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory에서 시각적 작성

Azure Data Factory UX(사용자 인터페이스 환경)을 사용하면 코드를 작성할 필요 없이 데이터 팩터리에 대한 리소스를 시각적으로 작성하고 배포할 수 있습니다. 파이프라인 캔버스에 작업을 끌어다 놓고, 테스트 실행을 수행하고, 반복적으로 디버그하고, 파이프라인 실행을 배포 및 모니터링할 수 있습니다.

현재 Azure Data Factory UX는 Microsoft Edge 및 Google Chrome 에서만 지원 됩니다.

## <a name="authoring-canvas"></a>작성 캔버스

**제작 캔버스**를 열려면 연필 아이콘을 클릭 합니다. 

![작성 캔버스](media/author-visually/authoring-canvas.png)

여기서는 팩터리를 구성 하는 파이프라인, 활동, 데이터 집합, 연결 된 서비스, 데이터 흐름, 트리거 및 통합 런타임을 작성 합니다. 제작 캔버스를 사용 하 여 파이프라인 빌드를 시작 하려면 [복사 작업을 사용 하 여 데이터 복사](tutorial-copy-data-portal.md)를 참조 하세요. 

기본 시각적 작성 환경은 Data Factory 서비스를 직접 사용 하는 것입니다. Azure Repos Git 또는 GitHub 통합은 데이터 팩터리 파이프라인에 대한 소스 제어 및 공동 작업을 허용 하는 데도 지원 됩니다. 이러한 제작 환경 간의 차이점에 대한 자세한 내용은 [Azure Data Factory의 소스 제어](source-control.md)를 참조 하세요.

## <a name="expressions-and-functions"></a>식 및 함수

Azure Data Factory에서 많은 속성을 지정 하기 위해 정적 값 대신 식 및 함수를 사용할 수 있습니다.

속성 값에 대한 식을 지정 하려면 **동적 콘텐츠 추가** 를 선택 하거나 필드에 포커스를 둘 때 **Alt + P** 를 클릭 합니다.

![동적 콘텐츠 추가](media/author-visually/dynamic-content-1.png)

그러면 지원 되는 시스템 변수, 작업 출력, 함수 및 사용자가 지정한 변수 또는 매개 변수에서 식을 작성할 수 있는 **Data Factory 식 작성기** 가 열립니다. 

![식 작성기](media/author-visually/dynamic-content-2.png)

식 언어에 대한 자세한 내용은 [Azure Data Factory의 식 및 함수](control-flow-expression-language-functions.md)를 참조 하세요.

## <a name="provide-feedback"></a>피드백 제공하기

기능에 대한 의견을 남기거나 Microsoft에 도구 관련 문제를 알리려면 **피드백**을 선택하세요.

![피드백](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>다음 단계

파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)를 참조하세요.
