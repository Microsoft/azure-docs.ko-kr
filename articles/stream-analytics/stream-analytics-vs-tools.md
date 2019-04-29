---
title: Visual Studio에서 Azure Stream Analytics 작업 보기
description: 이 문서에서는 Visual Studio에서 Stream Analytics 작업을 보는 방법을 설명합니다.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 1c7133801eb0d95616cacf501162e6cee3da7c80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477909"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Visual Studio를 사용하여 Azure Stream Analytics 작업 보기

Visual Studio용 Azure Stream Analytics 도구를 사용하면 개발자가 IDE에서 직접 Stream Analytics 작업을 간편하게 관리할 수 있습니다. Azure Stream Analytics 도구를 사용하여 다음과 같은 일을 할 수 있습니다.
- [새 작업 만들기](stream-analytics-quick-create-vs.md)
- 작업 시작, 중지 및 [모니터링](stream-analytics-monitor-jobs-use-vs.md)
- 작업 결과 확인
- 기존 작업을 프로젝트로 내보내기
- 입력 및 출력 연결 테스트
- [로컬로 쿼리 실행](stream-analytics-vs-tools-local-run.md)

[Visual Studio용 Azure Stream Analytics 도구 설치](stream-analytics-tools-for-visual-studio-install.md) 방법을 알아보세요.

## <a name="explore-the-job-view"></a>작업 보기 탐색

Visual Studio에서 작업 보기를 사용하여 Azure Stream Analytics 작업과 상호 작용할 수 있습니다.

### <a name="open-the-job-view"></a>작업 보기 열기

1. **서버 탐색기**에서 **Stream Analytics 작업**을 선택한 다음, **새로 고침**을 선택합니다. **Stream Analytics 작업** 아래에 작업이 표시됩니다.

    ![Stream Analytics 서버 탐색기 목록](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)



2. 작업 노드를 펼치고 **작업 보기** 노드를 두 번 클릭하여 작업 보기를 엽니다.
    
   ![확장된 작업 노드](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>작업 시작 및 중지

Visual Studio의 작업 보기에서 Azure Stream Analytics 작업을 완벽하게 관리할 수 있습니다. 컨트롤을 사용하여 작업을 시작, 중지 또는 삭제할 수 있습니다.
    
   ![Stream Analytics 작업 컨트롤](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)


## <a name="check-job-results"></a>작업 결과 확인

Visual Studio용 Stream Analytics 도구는 현재 Azure Data Lake Storage 및 Blob Storage에 대한 출력 미리 보기를 지원합니다. 결과를 보려면 **작업 보기**에서 작업 다이어그램의 출력 노드를 두 번 클릭하고 적절한 자격 증명을 입력하면 됩니다.

   ![Stream Analytics 작업 BLOB 출력](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)


## <a name="export-jobs-to-a-project"></a>작업을 프로젝트로 내보내기

두 가지 방법으로 기존 작업을 프로젝트로 내보낼 수 있습니다.

1. **서버 탐색기**의 Stream Analytics 작업 노드 아래에서 작업 노드를 마우스 오른쪽 단추로 클릭합니다. **새 Stream Analytics 프로젝트로 내보내기**를 선택합니다.
    
   ![작업을 프로젝트로 내보내기](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    **솔루션 탐색기**에서 생성된 프로젝트가 표시됩니다.
    
   ![솔루션 탐색기](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. 작업 보기에서 **프로젝트 생성**을 선택합니다.
    
   ![작업 보기에서 프로젝트 생성](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>연결 테스트

**연결 테스트** 드롭다운에서 옵션을 선택하여 **작업 보기**에서 입력 및 출력 연결을 테스트할 수 있습니다.

   ![연결 테스트 드롭다운](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

**연결 테스트** 결과는 **출력** 창에 표시됩니다.

   ![연결 테스트 결과](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>다음 단계

* [Visual Studio를 사용하여 Azure Stream Analytics 작업 모니터링 및 관리](stream-analytics-monitor-jobs-use-vs.md)
* [빠른 시작: Visual Studio를 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [자습서: Azure Stream Analytics 작업을 Azure 파이프라인을 사용 하 여 CI/CD를 사용 하 여 배포](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Stream Analytics 도구를 사용하여 지속적으로 통합 및 개발](stream-analytics-tools-for-visual-studio-cicd.md)
