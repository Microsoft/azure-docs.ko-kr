---
title: Azure Functions 진단 개요
description: Azure Functions 진단을 사용 하 여 함수 앱 문제를 해결 하는 방법에 대해 알아봅니다.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834043"
---
# <a name="azure-functions-diagnostics-overview"></a>Azure Functions 진단 개요

함수 앱을 실행 하는 경우 4xx 오류에서 오류를 트리거하기 때문에 발생할 수 있는 모든 문제에 대해 준비 하려고 합니다. Azure Functions 진단은 구성 또는 추가 비용 없이 함수 앱 문제를 해결 하는 데 도움이 되는 지능적이 고 대화형 환경입니다. 함수 앱에서 문제가 발생 하는 경우 Azure Functions 진단은 문제를 보다 쉽고 빠르게 해결 하 고 해결 하는 데 적합 한 정보를 안내해 주는 문제를 해결 합니다. 이 문서에서는 Azure Functions 진단을 사용 하 여 일반적인 함수 앱 문제를 보다 신속 하 게 진단 하 고 해결 하는 방법의 기본 사항을 보여 줍니다.

## <a name="start-azure-functions-diagnostics"></a>Azure Functions 진단 시작

Azure Functions 진단에 액세스 하려면:

1. [Azure Portal](https://portal.azure.com)의 함수 앱으로 이동 합니다.
2. **플랫폼 기능** 탭을 선택 합니다.
3. **리소스 관리**에서 진단 **및 문제 해결** 을 선택 하면 Azure Functions 진단이 열립니다.
4. 홈페이지 타일의 키워드를 사용 하 여 함수 앱의 문제를 가장 잘 설명 하는 범주를 선택 합니다. 검색 표시줄에서 문제를 가장 잘 설명 하는 키워드를 입력할 수도 있습니다. 예를 들어 `execution`를 입력 하 여 함수 앱 실행과 관련 된 진단 보고서 목록을 확인 하 고 홈 페이지에서 직접 열 수 있습니다.

![홈페이지](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>대화형 인터페이스 사용

함수 앱의 문제에 가장 잘 맞는 홈 페이지 범주를 선택 하면 진단의 대화형 인터페이스인 Genie를 사용 하 여 앱의 문제를 진단 하 고 해결 하는 과정을 안내할 수 있습니다 Azure Functions. Genie에서 제공 하는 타일 바로 가기를 사용 하 여 관심 있는 문제 범주의 전체 진단 보고서를 볼 수 있습니다. 타일 바로 가기를 통해 진단 메트릭에 직접 액세스할 수 있는 방법을 제공 합니다.

![지니는](./media/functions-diagnostics/genie.png)

타일을 선택한 후 타일에 설명 된 문제와 관련 된 항목의 목록을 볼 수 있습니다. 이러한 항목에서는 전체 보고서에서 주목할 만한 정보를 제공 합니다. 이러한 항목 중 하나를 선택 하 여 문제를 자세히 조사할 수 있습니다. 또한 **전체 보고서 보기** 를 선택 하 여 단일 페이지의 모든 항목을 탐색할 수 있습니다.

![진단 보고서 미리 보기](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>진단 보고서 보기

항목을 선택한 후에는 함수 앱과 관련 된 진단 보고서를 볼 수 있습니다. 진단 보고서는 상태 아이콘을 사용 하 여 앱에 특정 문제가 있는지 여부를 표시 합니다. 문제에 대한 자세한 설명, 권장 조치, 관련 메트릭 및 유용한 문서를 볼 수 있습니다. 사용자 지정 된 진단 보고서는 함수 앱에서 실행 되는 일련의 검사를 통해 생성 됩니다. 진단 보고서는 함수 앱에서 문제를 어설션 매크로나 하는 데 유용한 도구 이며 문제를 해결 하는 데 안내 합니다.

## <a name="find-the-problem-code"></a>문제 코드 찾기

스크립트 기반 함수의 경우 **함수 앱 다운 또는 오류 보고** 에서 **함수 실행 및 오류** 를 사용 하 여 예외 또는 오류가 발생 하는 코드 줄의 범위를 좁힐 수 있습니다. 이 기능은 특정 코드 줄에서 근본 원인을 파악 하 고 문제를 해결 하는 데 유용한 도구 일 수 있습니다. 미리 컴파일된 C# 및 Java 함수에는이 옵션을 사용할 수 없습니다.

![함수 실행 오류에 대한 진단 보고서](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![함수 예외](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>다음 단계

[UserVoice](https://feedback.azure.com/forums/355860-azure-functions)에서 Azure Functions 진단에 대해 질문 하거나 피드백을 제공할 수 있습니다. 사용자 의견의 제목에 `[Diag]`을 포함 하세요.

> [!div class="nextstepaction"]
> [함수 앱 모니터링](functions-monitoring.md)
