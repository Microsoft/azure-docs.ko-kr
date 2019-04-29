---
title: Azure Automation 계정에서 감시자 작업 만들기
description: 폴더에 만들어진 새 파일을 감시하도록 Azure Automation 계정에 감시자 작업을 만드는 방법을 알아봅니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: bee414ada61e2cfcf7609b02ef1da7323a0fe0e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304642"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>로컬 컴퓨터에서 파일 변경 내용을 추적하는 Azure Automation 감시자 태스크 만들기

Azure Automation은 감시자 태스크를 사용하여 이벤트를 감시하고 PowerShell Runbook을 사용하여 작업을 트리거합니다. 이 자습서에서는 새 파일이 디렉터리에 추가되는 시기를 모니터링하기 위한 감시자 태스크를 만드는 과정을 안내합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 감시자 Runbook 가져오기
> * Automation 변수 만들기
> * 작업 Runbook 만들기
> * 감시자 태스크 만들기
> * 감시자 트리거
> * 출력 검사

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 항목이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 감시자 및 작업 Runbook과 감시자 태스크를 포함하는 [Automation 계정](automation-offering-get-started.md)
* 감시자 작업이 실행되는 [하이브리드 Runbook 작업자](automation-hybrid-runbook-worker.md)

> [!NOTE]
> 감시자 태스크 Azure 중국에서 지원 되지 않습니다.

## <a name="import-a-watcher-runbook"></a>감시자 Runbook 가져오기

이 자습서에서는 **Watch-NewFile**이라는 감시자 Runbook을 사용하여 디렉터리에서 새 파일을 찾습니다. 감시자 Runbook은 폴더의 파일에 마지막으로 쓴 것으로 알려진 시간을 검색하고 해당 워터마크보다 최근 버전의 파일을 찾습니다. 이 단계에서는 해당 Runbook을 Automation 계정으로 가져옵니다.

1. Automation 계정을 열고 **Runbook** 페이지를 클릭합니다.
2. **갤러리 찾아보기** 단추를 클릭합니다.
3. "감시자 Runbook"을 검색하고 **디렉터리에서 새 파일을 찾는 감시자 Runbook**을 선택한 후에 **가져오기**를 선택합니다.
  ![UI에서 Automation Runbook 가져오기](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Runbook 이름과 설명을 입력하고 **확인**을 선택하여 Runbook을 Automation 계정으로 가져옵니다.
1. **편집**을 선택하고 **게시**를 클릭합니다. 메시지가 표시되면 **예**를 선택하여 Runbook을 게시합니다.

## <a name="create-an-automation-variable"></a>Automation 변수 만들기

[Automation 변수](automation-variables.md)는 위의 Runbook이 각 파일에서 읽고 저장하는 타임스탬프를 저장하는 데 사용됩니다.

1. **공유 리소스** 아래에서 **변수**를 선택하고 **+ 변수 추가**를 선택합니다.
1. 이름으로 "Watch-NewFileTimestamp"를 입력합니다.
1. 형식으로 날짜/시간을 선택합니다.
1. **만들기** 단추를 클릭합니다. 그러면 Automation 변수가 만들어집니다.

## <a name="create-an-action-runbook"></a>작업 Runbook 만들기

작업 Runbook은 감시자 Runbook에서 전달된 데이터에 대한 작업을 수행하기 위해 감시자 태스크에서 사용됩니다. PowerShell 워크플로 Runbook은 감시자 태스크에서 지원되지 않습니다. PowerShell Runbook을 사용해야 합니다. 이 단계에서는 "Process-NewFile"이라는 미리 정의된 작업 Runbook을 업데이트하고 가져옵니다.

1. Automation 계정으로 이동하여 **프로세스 자동화** 범주 아래에서 **Runbook**을 선택합니다.
1. **갤러리 찾아보기** 단추를 클릭합니다.
1. "감시자 작업"을 검색한 후 **감시자 Runbook에 의해 트리거되는 이벤트를 처리할 감시자 작업**을 선택하고 **가져오기**를 선택합니다.
  ![UI에서 작업 Runbook 가져오기](media/automation-watchers-tutorial/importsourceaction.png)
1. Runbook 이름과 설명을 입력하고 **확인**을 선택하여 Runbook을 Automation 계정으로 가져옵니다.
1. **편집**을 선택하고 **게시**를 클릭합니다. 메시지가 표시되면 **예**를 선택하여 Runbook을 게시합니다.

## <a name="create-a-watcher-task"></a>감시자 태스크 만들기

감시자 태스크에는 감시자와 작업의 두 부분이 포함됩니다. 감시자는 감시자 태스크에 정의된 간격에 맞춰 실행됩니다. 감시자 Runbook의 데이터는 작업 Runbook으로 전달됩니다. 이 단계에서는 이전 단계에서 정의한 감시자 및 작업 Runbook을 참조하는 감시자 태스크를 구성합니다.

1. Automation 계정으로 이동하여 **프로세스 자동화** 범주 아래에서 **감시자 태스크**를 선택합니다.
1. 감시자 태스크 페이지를 선택하고 **+ 감시자 태스크 추가** 단추를 클릭합니다.
1. 이름으로 "WatchMyFolder"를 입력합니다.

1. **감시자 구성**을 선택하고 **Watch-NewFile** Runbook을 선택합니다.

1. 매개 변수에 대해 다음 값을 입력합니다.

   * **FOLDERPATH** - 새 파일을 만들 Hybrid Worker의 폴더입니다. d:\examplefiles
   * **EXTENSION** - 모든 파일 확장명을 처리하려면 비워 둡니다.
   * **RECURSE** - 이 값은 기본값으로 유지합니다.
   * **RUN SETTINGS** - Hybrid Worker를 선택합니다.

1. 확인을 클릭하고 선택을 클릭하여 감시자 페이지로 돌아갑니다.
1. **작업 구성**을 선택하고 "Process-NewFile" Runbook을 선택합니다.
1. 매개 변수에 대해 다음 값을 입력합니다.

   * **EVENTDATA** - 비워 둡니다. 데이터는 감시자 Runbook에서 제공됩니다.  
   * **실행 설정** - 이 Runbook은 Automation 서비스에서 실행되므로 Azure로 유지합니다.

1. **확인**을 클릭하고 선택을 클릭하여 감시자 페이지로 돌아갑니다.
1. **확인**을 클릭하여 감시자 태스크를 만듭니다.

![UI에서 감시자 작업 구성](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>감시자 트리거

감시자가 정상적으로 작동하는지 테스트하려면 테스트 파일을 만들어야 합니다.

Hybrid Worker에 원격으로 연결합니다. **PowerShell**을 열고 폴더에 테스트 파일을 만듭니다.
  
```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

다음 예제에는 테스트가 정상적으로 완료된 경우의 결과가 나와 있습니다.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>출력 검사

1. Automation 계정으로 이동하여 **프로세스 자동화** 범주 아래에서 **감시자 태스크**를 선택합니다.
1. 감시자 태스크 "WatchMyFolder"를 선택합니다.
1. **스트림** 아래에서 **감시자 스트림 보기**를 클릭하여 감시자가 새 파일을 찾았으며 작업 Runbook을 시작했음을 확인합니다.
1. 작업 Runbook 작업을 확인하려면 **감시자 작업 보기**를 클릭합니다. 각 작업을 선택하면 작업 세부 정보를 확인할 수 있습니다.

   ![UI의 감시자 작업](media/automation-watchers-tutorial/WatcherActionJobs.png)

다음 예에는 새 파일을 찾은 경우 표시되는 출력이 나와 있습니다.

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 감시자 Runbook 가져오기
> * Automation 변수 만들기
> * 작업 Runbook 만들기
> * 감시자 태스크 만들기
> * 감시자 트리거
> * 출력 검사

Runbook을 직접 작성하는 방법을 자세히 알아보려면 아래 링크로 이동하세요.

> [!div class="nextstepaction"]
> [내 첫 번째 PowerShell Runbook](automation-first-runbook-textual-powershell.md)

