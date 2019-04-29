---
title: Visual Studio 템플릿을 사용하여 솔루션 빌드 - Azure Batch | Microsoft Docs
description: Visual Studio 프로젝트 템플릿을 통해 Azure Batch에서 계산 집약적인 워크로드를 어떻게 구현하고 실행할 수 있는지 알아봅니다.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 085bfa582b676f34a02e4c1c5ae7e69c49e5cb4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550077"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Visual Studio 프로젝트 템플릿을 사용하여 Batch 솔루션 빠르게 시작

Batch용 **작업 관리자** 및 **태스크 프로세서 Visual Studio 템플릿**은 최소한의 노력으로 Batch에서 계산 집약적 워크로드를 구현 및 실행하는 데 도움이 되는 코드를 제공합니다. 이 문서에서는 이러한 템플릿을 설명하고 템플릿을 사용하는 방법에 대한 지침을 제공합니다.

> [!IMPORTANT]
> 이 문서에서는 이러한 두 템플릿에 해당하는 정보만 다루며 사용자가 Batch 서비스 및 이와 관련된 주요 개념(풀, 계산 노드, 작업 및 태스크, 작업 관리자 태스크, 환경 변수 및 기타 관련 정보)에 익숙하다고 가정합니다. 자세한 내용은 [Azure Batch의 기본 사항](batch-technical-overview.md) 및 [개발자를 위한 Batch 기능 개요](batch-api-basics.md)를 참조하세요.
> 
> 

## <a name="high-level-overview"></a>대략적인 개요
작업 관리자 및 태스크 프로세서 템플릿은 다음 두 가지 유용한 구성 요소를 만드는 데 사용할 수 있습니다.

* 작업을 독립적으로 병렬 실행 가능한 여러 태스크로 나눌 수 있는 작업 분할자를 구현하는 작업 관리자 태스크.
* 애플리케이션 명령줄에 대한 전처리 및 후처리를 수행하는 데 사용할 수 있는 태스크 프로세서.

예를 들어 동영상 렌더링 시나리오에서 경우 작업 분할자는 하나의 동영상 작업을 수백 또는 수천 개의 태스크로 전환하여 개별 프레임을 별도로 처리하도록 합니다. 마찬가지로, 태스크 프로세서는 각 프레임을 렌더링하는 데 필요한 렌더링 애플리케이션과 모든 종속 프로세스를 호출하고 추가 작업(예: 렌더링된 프레임을 스토리지 위치에 복사)을 수행합니다.

> [!NOTE]
> 작업 관리자 및 태스크 프로세서 템플릿은 서로 독립적이므로 기본 설정에서 계산 작업의 요구 사항에 따라 둘 다 또는 둘 중 하나만 사용하도록 선택할 수 있습니다.
> 
> 

아래 다이어그램에 표시된 것처럼 이러한 템플릿을 사용하는 계산 작업은 세 가지 단계를 거칩니다.

1. 클라이언트 코드(예: 애플리케이션, 웹 서비스 등)가 작업을 Azure의 Batch 서비스에 제출합니다. 이때 작업 관리자가 프로그래밍하는 작업 관리자 태스크로 지정합니다.
2. Batch 서비스는 계산 노드에서 작업 관리자 태스크를 실행하고 작업 분할자는 작업 분할자 코드에 있는 매개 변수 및 사양에 따라 필요한 만큼의 계산 노드에서 지정된 수의 태스크 프로세서 태스크를 실행합니다.
3. 태스크 프로세서 태스크는 입력 데이터를 처리하고 출력 데이터를 생성하기 위해 독립적으로 병렬 실행됩니다.

![클라이언트 코드가 Batch 서비스와 상호 작용하는 방법을 보여 주는 다이어그램][diagram01]

## <a name="prerequisites"></a>필수 조건
Batch 템플릿을 사용하려면 다음이 필요합니다.

* Visual Studio 2015가 설치된 컴퓨터. 일괄 처리 템플릿은 현재 Visual Studio 2015에 대해서만 지원됩니다.
* [Visual Studio 갤러리][vs_gallery]에서 Visual Studio 확장으로 제공되는 Batch 템플릿. 템플릿을 얻는 방법은 두 가지입니다.
  
  * Visual Studio에서 **확장 및 업데이트** 대화 상자를 사용하여 템플릿을 설치합니다(자세한 내용은 [Visual Studio 확장 찾기 및 사용][vs_find_use_ext] 참조). **확장 및 업데이트** 대화 상자에서 다음 두 확장을 검색하여 다운로드합니다.
    
    * Azure Batch 작업 관리자(작업 분할자 포함)
    * Azure Batch 태스크 프로세서
  * Visual Studio의 온라인 갤러리에서 템플릿을 다운로드합니다. [Microsoft Azure Batch 프로젝트 템플릿][vs_gallery_templates]
* [애플리케이션 패키지](batch-application-packages.md) 기능을 사용하여 작업 관리자 및 태스크 프로세서를 Batch 계산 노드에 배포할 계획인 경우 스토리지 계정을 Batch 계정에 연결해야 합니다.

## <a name="preparation"></a>준비
작업 관리자 및 태스크 프로세서 프로그램 간에 코드를 쉽게 공유할 수 있으므로 작업 관리자 및 태스크 프로세서를 포함할 수 있는 솔루션을 만드는 것이 좋습니다. 이 솔루션을 만들려면 다음 단계를 따르세요.

1. Visual Studio를 열고 **파일** > **새로 만들기** > **프로젝트**를 선택합니다.
2. **템플릿** 아래에서 **기타 프로젝트 형식**을 확장하고 **Visual Studio 솔루션**을 클릭한 후 **빈 솔루션**을 선택합니다.
3. 애플리케이션 및 이 솔루션의 용도를 설명하는 이름을 입력합니다(예: "LitwareBatchTaskPrograms").
4. 새 솔루션을 만들려면 **확인**을 클릭합니다.

## <a name="job-manager-template"></a>작업 관리자 템플릿
작업 관리자 템플릿을 통해 다음 작업을 수행할 수 있는 작업 관리자 태스크를 구현할 수 있습니다.

* 작업을 여러 태스크로 분할합니다.
* Batch에서 실행하려면 이러한 태스크를 제출합니다.

> [!NOTE]
> 작업 관리자 태스크에 대한 자세한 내용은 [개발자를 위한 Batch 기능 개요](batch-api-basics.md#job-manager-task)를 참조하세요.
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>템플릿을 사용하여 작업 관리자 만들기
이전에 만든 솔루션에 작업 관리자를 추가하려면 다음 단계를 수행합니다.

1. Visual Studio에서 기존 솔루션을 엽니다.
2. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 프로젝트**를 클릭합니다.
3. **Visual C#** 아래에서 **클라우드**를 클릭한 후 **Azure Batch 작업 관리자 및 작업 분할자**을 클릭합니다.
4. 애플리케이션을 설명하고 이 프로젝트를 작업 관리자로 식별하는 이름을 입력합니다(예: "LitwareJobManager").
5. 프로젝트를 만들려면 **확인**을 클릭합니다.
6. 마지막으로, 프로젝트를 빌드하여 Visual Studio에서 모든 참조된 NuGet 패키지를 로드하고 수정을 시작하기 전에 해당 프로젝트가 유효한지 확인하도록 합니다.

### <a name="job-manager-template-files-and-their-purpose"></a>작업 관리자 템플릿 파일 및 그 용도
작업 관리자 템플릿을 사용하여 프로젝트를 만들면 세 가지 코드 파일 그룹이 생성됩니다.

* 메인 프로그램 파일(Program.cs). 여기에는 프로그램 진입점과 최상위 예외 처리가 포함됩니다. 일반적으로 이 값을 수정할 필요가 없습니다.
* 프레임워크 디렉터리. 여기에는 매개 변수 압축 해제, Batch 작업에 태스크 추가 등 작업 관리자 프로그램이 수행했던 '상용구' 작업을 담당하는 파일이 포함됩니다. 일반적으로 이러한 파일을 수정할 필요가 없습니다.
* 작업 분할자 파일(JobSplitter.cs). 작업을 태스크로 분할하기 위해 애플리케이션 관련 논리를 배치할 위치입니다.

물론 작업 분할 논리의 복잡성에 따라 작업 분할자 코드를 지원하는 데 필요하다면 파일을 더 추가할 수 있습니다.

템플릿에서는 .csproj 파일, app.config, packages.config 등과 같은 표준 .NET 프로젝트 파일도 생성합니다.

이 섹션의 나머지에서는 다양한 파일 및 해당 코드 구조를 설명하고 각 클래스에서 어떤 작업을 수행하는지 설명합니다.

![작업 관리자 템플릿 솔루션을 보여 주는 Visual Studio 솔루션 탐색기][solution_explorer01]

**프레임워크 파일**

* `Configuration.cs`: 배치 계정 정보, 연결된 스토리지 계정 자격 증명, 작업 및 태스크 정보 및 작업 매개 변수와 같은 작업 구성 데이터의 로드를 캡슐화합니다. 또한 Configuration.EnvironmentVariable 클래스를 통해 Batch 정의된 환경 변수(Batch 설명서에서 태스크에 대한 환경 설정 참조)에 대한 액세스도 제공합니다.
* `IConfiguration.cs`: 모조 또는 모의 구성 개체를 사용하여 작업 분할자의 단위 테스트를 수행할 수 있도록 구성 클래스의 구현을 추상화합니다.
* `JobManager.cs`: 작업 관리자 프로그램의 구성 요소를 오케스트레이션합니다. 작업 분할자의 초기화, 작업 분할자 호출 및 작업 분할자에서 태스크 제출자로 반환되는 태스크 디스패치를 담당합니다.
* `JobManagerException.cs`: 작업 관리자를 종료해야 하는 오류를 나타냅니다. JobManagerException은 종료의 일부분으로 특정 진단 정보를 제공할 수 있는 '예상된' 오류를 래핑하는 데 사용됩니다.
* `TaskSubmitter.cs`: 이 클래스는 작업 분할자가 반환한 태스크를 Batch 작업에 추가하는 것을 담당합니다. JobManager 클래스는 태스크 시퀀스를 배치로 효율적으로 집계하여 작업에 시기 적절하게 추가한 후 각 배치에 대한 백그라운드 스레드에서 TaskSubmitter.SubmitTasks를 호출합니다.

**작업 분할자**

`JobSplitter.cs`: 이 클래스는 작업을 태스크로 분할하기 위한 애플리케이션 관련 논리를 포함합니다. 프레임워크는 태스크 시퀀스를 가져오기 위해 JobSplitter.Split 메서드를 호출하며 메서드가 시퀀스를 반환하면 이를 작업에 추가합니다. 작업의 논리를 삽입할 클래스입니다. 작업을 파티션할 태스크를 나타내는 CloudTask 인스턴스 시퀀스를 반환할 Split 메서드를 구현합니다.

**표준 .NET 명령줄 프로젝트 파일**

* `App.config`: 표준 .NET 애플리케이션 구성 파일입니다.
* `Packages.config`: 표준 NuGet 패키지 종속성 파일입니다.
* `Program.cs`: 프로그램 진입점과 최상위 예외 처리가 포함됩니다.

### <a name="implementing-the-job-splitter"></a>작업 분할자 구현
작업 관리자 템플릿 프로젝트를 열면 프로젝트에 기본적으로 JobSplitter.cs 파일이 열려 있습니다. 아래 표시된 Split() 메서드를 사용하여 워크로드에서 태스크에 대한 분할 논리를 구현할 수 있습니다.

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> `Split()` 메서드에서 주석이 지정된 섹션은 작업을 서로 다른 태스크로 분리하는 논리를 추가하여 수정하려는 작업 관리자 템플릿 코드의 유일한 섹션입니다. 템플릿의 다른 섹션을 수정하려는 경우 Batch가 작동하는 방식을 파악하고 몇 가지 [Batch 코드 샘플][github_samples]을 사용해 보세요.
> 
> 

Split() 구현에서는 다음에 액세스할 수 있습니다.

* `_parameters` 필드를 통해 작업 매개 변수에 액세스합니다.
* `_job` 필드를 통해 작업을 나타내는 CloudJob 개체에 액세스합니다.
* `_jobManagerTask` 필드를 통해 작업 관리자 태스크를 나타내는 CloudTask 개체에 액세스합니다.

`Split()` 구현에서는 태스크를 작업에 직접 추가하지 않아도 됩니다. 대신, 코드가 CloudTask 개체의 시퀀스를 반환하고 이러한 개체는 작업 분할자를 호출하는 프레임워크 클래스에 의해 작업에 자동으로 추가됩니다. 일반적으로 C#의 반복기(`yield return`) 기능을 사용하여 모든 태스크가 계산될 때까지 기다리지 않고 가능한 빨리 실행을 시작할 수 있도록 작업 분할자를 구현합니다.

**작업 분할자 실패**

작업 분할자에 오류가 발생하는 경우 다음 중 하나를 수행합니다.

* C# `yield break` 문을 사용하여 시퀀스를 종료합니다. 이 경우 작업 관리자가 성공으로 처리됩니다. 또는
* 예외를 Throw합니다. 이 경우 작업 관리자는 실패로 처리되며 클라이언트가 구성된 방식에 따라 재시도될 수 있습니다.

두 경우 모두, 작업 분할자에 의해 이미 반환되어 Batch 작업에 추가된 모든 태스크를 실행할 수 있게 됩니다. 이러한 경우가 발생하지 않도록 하려면 다음을 수행할 수 있습니다.

* 작업 분할자에서 반환하기 전에 작업 종료
* 반환하기 전에 전체 태스크 컬렉션 작성(즉, C# 반복기를 사용하여 작업 분할자를 구현하는 대신 `ICollection<CloudTask>` 하나에 `IList<CloudTask>` instead of implementing your job splitter using a C# iterat하나에)
* 태스크 종속성을 사용하여 모든 태스크가 작업 관리자의 성공적인 완료에 종속되도록 함

**작업 관리자 재시도**

작업 관리자가 실패하는 경우 클라이언트 재시도 설정에 따라 Batch 서비스에 의해 재시도될 수 있습니다. 일반적으로 이 방법은 프레임워크가 태스크를 작업에 추가할 경우 이미 존재하는 모든 태스크를 무시하므로 안전합니다. 그러나 태스크 계산 비용이 많이 드는 경우 작업에 이미 추가했던 태스크를 다시 계산하는 비용이 발생하는 것을 원하지 않을 수 있으며 반대로, 다시 실행한다고 해도 동일한 태스크 ID가 생성된다는 보장이 없는 경우 '중복 무시' 동작이 시작되지 않습니다. 이러한 경우 예를 들어 태스크가 생성되기 시작하기 전에 CloudJob.ListTasks를 수행하여 이미 수행되었고 반복되지 않는 작업을 검색하도록 작업 분할자를 설계해야 합니다.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>작업 관리자 템플릿에서 종료 코드 및 예외
종료 코드 및 예외는 프로그램 실행 결과를 확인하는 메커니즘을 제공하며 프로그램의 실행 시 발생하는 문제를 식별하는 데 도움이 될 수 있습니다. 작업 관리자 템플릿은 이 섹션에 설명된 종료 코드 및 예외를 구현합니다.

작업 관리자 템플릿으로 구현된 작업 관리자 태스크는 세 가지 가능한 종료 코드를 반환할 수 있습니다.

| 코드 | 설명 |
| --- | --- |
| 0 |작업 관리자가 성공적으로 완료되었습니다. 작업 분할자 코드가 완료될 때까지 실행되고 모든 태스크가 작업에 추가되었습니다. |
| 1 |작업 관리자 태스크가 프로그램의 '예상된' 부분에서 예외로 인해 실패했습니다. 예외는 진단 정보 및 가능한 경우, 오류를 해결하기 위한 제안과 함께 JobManagerException으로 해석됩니다. |
| 2 |작업 관리자 태스크가 '예기치 않은' 예외로 인해 실패했습니다. 예외는 표준 출력에 기록되었지만 작업 관리자가 추가 진단 또는 재구성 정보를 추가할 수 없습니다. |

작업 관리자 태스크가 실패하는 경우 일부 태스크는 오류가 발생하기 전에 서비스에 추가되었을 수 있습니다. 이러한 태스크는 정상적으로 실행됩니다. 이 코드 경로에 대한 설명은 위의 "작업 분할자 오류"를 참조하세요.

예외에서 반환된 모든 정보는 stdout.txt 및 stderr.txt 파일에 기록됩니다. 자세한 내용은 [오류 처리](batch-api-basics.md#error-handling)를 참조하세요.

### <a name="client-considerations"></a>클라이언트 고려 사항
이 섹션에서는 이 템플릿을 기반으로 작업 관리자를 호출할 때 일부 클라이언트 구현 요구 사항에 대해 설명합니다. 매개 변수 및 환경 설정 전달에 대한 자세한 내용은 [클라이언트 코드에서 매개 변수 및 환경 변수를 전달하는 방법](#pass-environment-settings) 을 참조하세요.

**필수 자격 증명**

Azure Batch 작업에 태스크를 추가하려면 작업 관리자 태스크에 Azure Batch 계정 URL과 키가 필요합니다. YOUR_BATCH_URL 및 YOUR_BATCH_KEY라는 환경 변수에 이를 전달해야 합니다. 작업 관리자 태스크 환경 설정에서 이러한 내용을 설정할 수 있습니다. 예를 들어 C# 클라이언트에서 다음과 같이 설정합니다.

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Storage 자격 증명**

일반적으로 (a) 대부분의 작업 관리자는 연결된 저장소 계정에 명시적으로 액세스할 필요가 없으므로 클라이언트는 작업 관리자 태스크에 연결된 저장소 계정을 제공하지 않아도 되며 (b) 대체로 연결된 저장소 계정이 작업에 대한 일반 환경 설정으로 모든 태스크에 제공됩니다. 일반 환경 설정을 통해 연결된 저장소 계정을 제공하지 않는 경우 작업 관리자는 연결된 저장소에 액세스해야 하며 다음과 같이 연결된 저장소 자격 증명을 제공해야 합니다.

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**작업 관리자 태스크 설정**

클라이언트는 작업 관리자 *killJobOnCompletion* 플래그를 **false**로 설정해야 합니다.

클라이언트가 *runExclusive* 를 **false**로 설정하는 것이 일반적으로 안전합니다.

클라이언트는 *resourceFiles* 또는 *applicationPackageReferences* 컬렉션을 사용하여 작업 관리자 실행 파일(및 필요한 DLL)이 계산 노드에 배포되도록 해야 합니다.

기본적으로 작업 관리자는 실패한 경우 재시도되지 않습니다. 사용자의 작업 논리자 논리에 따라 클라이언트가 *constraints*/*maxTaskRetryCount*를 통해 재시도를 활성화하려고 할 수 있습니다.

**작업 설정**

작업 분할자가 종속성과 함께 태스크를 내보내는 경우 클라이언트는 작업의 usesTaskDependencies를 true로 설정해야 합니다.

작업 분할자 모델에서는 클라이언트가 작업 분할자가 생성한 것 이상으로 작업에 태스크를 추가하려는 것은 정상적이지 않습니다. 따라서 클라이언트는 일반적으로 작업의 *onAllTasksComplete* 를 **terminatejob**으로 설정해야 합니다.

## <a name="task-processor-template"></a>태스크 프로세서 템플릿
태스크 프로세서 템플릿을 통해 다음 작업을 수행할 수 있는 태스크 프로세서를 구현할 수 있습니다.

* 각 Batch 태스크를 실행하는 데 필요한 정보를 설정합니다.
* 각 Batch 태스크에 필요한 모든 작업을 실행합니다.
* 태스크 출력을 영구 저장소에 저장합니다.

Batch에서 태스크를 실행하는 데 태스크 프로세서가 반드시 필요하지는 않지만 태스크 프로세서를 사용할 경우 주요 장점은 한 위치에서 모든 태스크 실행 작업을 구현하는 래퍼를 제공한다는 것입니다. 예를 들어 각 태스크의 컨텍스트에서 여러 애플리케이션을 실행해야 하는 경우 또는 각 태스크를 완료한 후에 데이터를 영구 스토리지에 복사해야 하는 경우가 있습니다.

태스크 프로세서가 수행하는 작업은 단순 또는 복잡할 수 있으며 워크로드의 필요에 따라 많거나 적을 수 있습니다. 또한 모든 태스크 작업을 하나의 태스크 프로세서로 구현하여 애플리케이션 또는 워크로드 요구 사항의 변경에 따라 작업을 쉽게 업데이트 또는 추가할 수 있습니다. 그러나 예를 들어 간단한 명령줄에서 신속하게 시작할 수 있는 작업을 실행하는 경우처럼 일부 경우에는 태스크 프로세서가 불필요한 복잡성을 추가할 수 있으므로 사용자 구현에 대해 최적의 솔루션이 아닐 수 있습니다.

### <a name="create-a-task-processor-using-the-template"></a>템플릿을 사용하여 태스크 프로세서 만들기
이전에 만든 솔루션에 태스크 프로세서를 추가하려면 다음 단계를 수행합니다.

1. Visual Studio에서 기존 솔루션을 엽니다.
2. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **새 프로젝트**를 클릭합니다.
3. **Visual C#** 아래에서 **클라우드**를 클릭한 후 **Azure Batch 태스크 프로세서**를 클릭합니다.
4. 애플리케이션을 설명하고 이 프로젝트를 태스크 프로세서로 식별하는 이름을 입력합니다(예: "LitwareTaskProcessor").
5. 프로젝트를 만들려면 **확인**을 클릭합니다.
6. 마지막으로, 프로젝트를 빌드하여 Visual Studio에서 모든 참조된 NuGet 패키지를 로드하고 수정을 시작하기 전에 해당 프로젝트가 유효한지 확인하도록 합니다.

### <a name="task-processor-template-files-and-their-purpose"></a>태스크 프로세서 템플릿 파일 및 그 용도
태스크 프로세서 템플릿을 사용하여 프로젝트를 만들면 세 가지 코드 파일 그룹이 생성됩니다.

* 메인 프로그램 파일(Program.cs). 여기에는 프로그램 진입점과 최상위 예외 처리가 포함됩니다. 일반적으로 이 값을 수정할 필요가 없습니다.
* 프레임워크 디렉터리. 여기에는 매개 변수 압축 해제, Batch 작업에 태스크 추가 등 작업 관리자 프로그램이 수행했던 '상용구' 작업을 담당하는 파일이 포함됩니다. 일반적으로 이러한 파일을 수정할 필요가 없습니다.
* 태스크 프로세서 파일(TaskProcessor.cs). 태스크를 실행(일반적으로 기존 실행 파일을 호출)하기 위해 애플리케이션 관련 논리를 배치할 위치입니다. 전처리 및 후처리 코드(예: 추가 데이터 다운로드 또는 결과 파일 업로드 등)도 여기에 포함됩니다.

물론 작업 분할 논리의 복잡성에 따라 태스크 프로세서 코드를 지원하는 데 필요하다면 파일을 더 추가할 수 있습니다.

템플릿에서는 .csproj 파일, app.config, packages.config 등과 같은 표준 .NET 프로젝트 파일도 생성합니다.

이 섹션의 나머지에서는 다양한 파일 및 해당 코드 구조를 설명하고 각 클래스에서 어떤 작업을 수행하는지 설명합니다.

![태스크 프로세서 템플릿 솔루션을 보여 주는 Visual Studio 솔루션 탐색기][solution_explorer02]

**프레임워크 파일**

* `Configuration.cs`: 배치 계정 정보, 연결된 스토리지 계정 자격 증명, 작업 및 태스크 정보 및 작업 매개 변수와 같은 작업 구성 데이터의 로드를 캡슐화합니다. 또한 Configuration.EnvironmentVariable 클래스를 통해 Batch 정의된 환경 변수(Batch 설명서에서 태스크에 대한 환경 설정 참조)에 대한 액세스도 제공합니다.
* `IConfiguration.cs`: 모조 또는 모의 구성 개체를 사용하여 작업 분할자의 단위 테스트를 수행할 수 있도록 구성 클래스의 구현을 추상화합니다.
* `TaskProcessorException.cs`: 작업 관리자를 종료해야 하는 오류를 나타냅니다. TaskProcessorException은 종료의 일부분으로 특정 진단 정보를 제공할 수 있는 '예상된' 오류를 래핑하는 데 사용됩니다.

**태스크 프로세서**

* `TaskProcessor.cs`: 작업을 실행합니다. 프레임워크는 TaskProcessor.Run 메서드를 호출합니다. 태스크의 애플리케이션 관련 논리를 삽입할 클래스입니다. 다음을 수행하는 Run 메서드를 구현합니다.
  * 태스크 매개 변수 구문 분석 및 유효성 검사
  * 호출하려는 모든 외부 프로그램에 대한 명령줄 작성
  * 디버깅 용도로 필요할 수 있는 모든 진단 정보 기록
  * 해당 명령줄을 사용하는 프로세스 시작
  * 프로세스가 종료될 때까지 대기
  * 성공 또는 실패를 확인하기 위해 프로세스의 종료 코드 캡처
  * 영구 저장소에 보관하려는 모든 출력 파일 저장

**표준 .NET 명령줄 프로젝트 파일**

* `App.config`: 표준 .NET 애플리케이션 구성 파일입니다.
* `Packages.config`: 표준 NuGet 패키지 종속성 파일입니다.
* `Program.cs`: 프로그램 진입점과 최상위 예외 처리가 포함됩니다.

## <a name="implementing-the-task-processor"></a>태스크 프로세서 구현
태스크 프로세서 템플릿 프로젝트를 열면 프로젝트에 기본적으로 TaskProcessor.cs 파일이 열려 있습니다. 아래 표시된 Run() 메서드를 사용하여 워크로드에서 태스크에 대한 실행 논리를 구현할 수 있습니다.

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> Run() 메서드에서 주석이 지정된 섹션은 워크로드에 태스크에 대한 실행 논리를 추가하여 수정하려는 태스크 프로세서 템플릿 코드의 유일한 섹션입니다. 템플릿의 다른 섹션을 수정하려는 경우 Batch 설명서를 검토하고 몇 가지 Batch 코드 샘플을 사용해 본 후 Batch가 작동하는 방식을 직접 파악해 보세요.
> 
> 

Run() 메서드는 명령줄을 실행하고 하나 이상의 프로세스를 시작하며 모든 프로세스가 완료될 때까지 기다리고 결과를 저장하며 마지막으로는 종료 코드와 함께 반환하는 일을 담당합니다. Run() 메서드에서 태스크에 대한 처리 논리를 구현합니다. 태스크 프로세서 프레임워크가 사용자 대신 Run() 메서드를 호출하므로 사용자가 직접 호출하지 않아도 됩니다.

Run() 구현에서는 다음에 액세스할 수 있습니다.

* `_parameters` 필드를 통해 태스크 매개 변수에 액세스합니다.
* `_jobId`과 `_taskId` 필드를 통해 작업 및 태스크 ID에 액세스합니다.
* `_configuration` 필드를 통해 태스크 구성에 액세스합니다.

**태스크 실패**

오류가 발생한 경우 예외를 throw하여 Run() 메서드를 종료할 수 있지만 이렇게 하면 최상위 예외 처리기가 태스크 종료 코드 제어 하에 있게 됩니다. 예를 들어 진단 목적을 위해 다양한 오류 유형을 구분할 수 있도록 종료 코드를 제어해야 하는 경우 또는 일부 오류 모드에서 특정 작업만 종료해야 하므로 0이 아닌 종료 코드를 반환하여 Run() 메서드를 종료해야 합니다. 이것이 태스크 종료 코드가 됩니다.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>태스크 프로세서 템플릿에서 종료 코드 및 예외
종료 코드 및 예외는 프로그램 실행 결과를 확인하는 메커니즘을 제공하며 프로그램의 실행 시 발생하는 문제를 식별하는 데 도움이 될 수 있습니다. 태스크 프로세서 템플릿은 이 섹션에 설명된 종료 코드 및 예외를 구현합니다.

태스크 프로세서 템플릿으로 구현된 태스크 프로세서 태스크는 세 가지 가능한 종료 코드를 반환할 수 있습니다.

| 코드 | 설명 |
| --- | --- |
| [Process.ExitCode][process_exitcode] |태스크 프로세서가 완료될 때까지 실행됩니다. 사용자가 호출한 프로그램이 성공했음을 의미하는 것은 아니며 태스크 프로세서가 이를 성공적으로 호출했고 예외 없이 후처리를 수행했음만 의미합니다. 종료 코드의 의미는 호출한 프로그램에 따라 다르며 일반적으로 종료 코드 0은 프로그램이 성공했음을 의미하고 그 외의 종료 코드는 프로그램이 실패했음을 의미합니다. |
| 1 |태스크 프로세서가 프로그램의 '예상된' 부분에서 예외로 인해 실패했습니다. 예외는 진단 정보 및 가능한 경우, 오류를 해결하기 위한 제안과 함께 `TaskProcessorException` 으로 해석됩니다. |
| 2 |태스크 프로세서가 '예기치 않은' 예외로 인해 실패했습니다. 예외는 표준 출력에 기록되었지만 태스크 프로세서가 추가 진단 또는 재구성 정보를 추가할 수 없습니다. |

> [!NOTE]
> 호출한 프로그램에서 특정 오류 모드를 나타내기 위해 종료 코드 1 및 2를 사용하는 경우 태스크 프로세서 오류에 대해 종료 코드 1 및 2를 사용하는 것이 모호합니다. Program.cs 파일에서 예외 사례를 편집하여 이러한 태스크 프로세서 오류 코드를 고유한 종료 코드로 변경할 수 있습니다.
> 
> 

예외에서 반환된 모든 정보는 stdout.txt 및 stderr.txt 파일에 기록됩니다. 자세한 내용은 Batch 설명서의 오류 처리를 참조하세요.

### <a name="client-considerations"></a>클라이언트 고려 사항
**Storage 자격 증명**

태스크 프로세서에서 출력을 유지하기 위해 Azure Blob Storage를 사용하는 경우(예를 들어 파일 규약 도우미 라이브러리를 사용하는 경우) 클라우드 스토리지 계정 자격 증명 *또는* 공유 액세스 서명(SAS)을 포함하는 Blob 컨테이너 URL 중 *하나에* 액세스해야 합니다. 템플릿에는 일반적인 환경 변수를 통해 자격 증명을 제공하기 위한 지원이 포함됩니다. 클라이언트는 다음과 같이 저장소 자격 증명을 전달할 수 있습니다.

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

그러면 저장소 계정을 `_configuration.StorageAccount` 속성을 통해 TaskProcessor 클래스에서 사용할 수 있습니다.

SAS와 함께 컨테이너 URL을 사용하는 것을 선호하는 경우 작업의 일반적인 환경 설정을 통해 이를 전달할 수도 있지만 현재 태스크 프로세서 템플릿은 이에 대한 기본 제공 지원을 포함하지 않습니다.

**Storage 설정**

클라이언트 또는 작업 관리자 태스크는 태스크를 작업에 추가하기 전에 태스크에 필요한 모든 컨테이너를 생성합니다. SAS와 함께 컨테이너 URL을 사용하는 경우 이러한 URL에는 컨테이너를 생성할 권한이 없으므로 필수입니다. 컨테이너에서 CloudBlobContainer.CreateIfNotExistsAsync를 호출해야 하는 모든 태스크를 저장하므로 저장소 계정 자격 증명을 전달하는 경우에도 권장됩니다.

## <a name="pass-parameters-and-environment-variables"></a>매개 변수 및 환경 변수 전달
### <a name="pass-environment-settings"></a>환경 설정 전달
클라이언트는 환경 설정 형태로 작업 관리자 태스크에 정보를 전달할 수 있습니다. 그러면 계산 작업의 일부로 실행할 태스크 프로세서 태스크를 생성할 때 작업 관리자 태스크가 이 정보를 사용할 수 있습니다. 환경 설정으로 전달할 수 있는 정보의 예는 다음과 같습니다.

* Storage 계정 이름 및 계정 키
* Batch 계정 URL
* Batch 계정 키

배치 서비스에는 [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask]에서 `EnvironmentSettings` 속성을 사용하여 환경 설정을 작업 관리자 태스크로 전달하는 간단한 메커니즘이 있습니다.

예를 들어 Batch 계정에 대한 `BatchClient` 인스턴스를 가져오려면 클라이언트 코드에서 Batch 계정에 대한 URL 및 공유 키 자격 증명을 환경 변수로 전달할 수 있습니다. 마찬가지로 Batch 계정에 연결되는 저장소 계정에 액세스하려면 저장소 계정 이름 및 저장소 계정 키를 환경 변수로 전달할 수 있습니다.

### <a name="pass-parameters-to-the-job-manager-template"></a>매개 변수를 작업 관리자 템플릿으로 전달
대부분의 경우 작업 분할 프로세스를 제어하거나 작업에 대한 태스크를 구성하기 위해 작업당 매개 변수를 작업 관리자 태스크로 전달하는 데 유용합니다. 작업 관리자 태스크에 대한 리소스 파일로 parameters.json이라는 JSON 파일을 업로드하여 이 작업을 수행할 수 있습니다. 그러면 매개 변수를 작업 관리자 템플릿의 `JobSplitter._parameters` 필드에서 사용할 수 있게 됩니다.

> [!NOTE]
> 기본 제공 매개 변수 처리기는 문자열-문자열 사전만 지원합니다. 복잡한 JSON 값을 매개 변수 값으로 전달하려는 경우 이러한 값을 문자열로 전달하고 작업 분할자에서 구문 분석하거나 프레임워크의 `Configuration.GetJobParameters` 메서드를 수정해야 합니다.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>매개 변수를 태스크 프로세서 템플릿으로 전달
매개 변수를 태스크 프로세서 템플릿을 사용하여 구현된 개별 태스크로 전달할 수도 있습니다. 작업 관리자 템플릿처럼 태스크 프로세서 템플릿은

parameters.json이라는 리소스 파일을 찾고 있는 경우 이를 매개 변수 사전으로 로드합니다. 매개 변수를 태스크 프로세서 태스크로 전달하는 방법에 대한 몇 가지 옵션이 있습니다.

* 작업 매개 변수 JSON을 재사용합니다. 유일한 매개 변수가 작업 수준 매개 변수(예를 들어 렌더링 높이 및 너비)인 경우 잘 작동합니다. 이렇게 하려면 작업 분할자에서 CloudTask를 만들 때 parameters.json 리소스 파일 개체에 대한 참조를 작업 관리자 태스크의 ResourceFiles(`JobSplitter._jobManagerTask.ResourceFiles`)에서 CloudTask의 ResourceFiles 컬렉션으로 추가합니다.
* 태스크 관련 parameters.json 문서를 작업 분할자 실행의 일부로 생성 및 업로드하고 태스크 리소스 파일 컬렉션에서 해당 Blob를 참조합니다. 다양한 태스크에 서로 다른 매개 변수가 있는 경우 필요합니다. 프레임 인덱스가 태스크에 매개 변수로 전달되는 3D 렌더링 시나리오를 예로 들 수 있습니다.

> [!NOTE]
> 기본 제공 매개 변수 처리기는 문자열-문자열 사전만 지원합니다. 복잡한 JSON 값을 매개 변수 값으로 전달하려는 경우 이러한 값을 문자열로 전달하고 태스크 프로세서에서 구문 분석하거나 프레임워크의 `Configuration.GetTaskParameters` 메서드를 수정해야 합니다.
> 
> 

## <a name="next-steps"></a>다음 단계
### <a name="persist-job-and-task-output-to-azure-storage"></a>작업 유지 및 Azure Storage에 태스크 출력
Batch 솔루션 개발 시 다른 유용한 도구는 [Azure Batch 파일 규칙][nuget_package]입니다. Batch .NET 애플리케이션에서 .NET 클래스 라이브러리(현재 미리 보기 상태)를 사용하면 Azure Storage 간에 태스크 출력을 쉽게 저장하고 검색할 수 있습니다. [Azure Batch 작업 및 태스크 출력 보관](batch-task-output.md) 에는 라이브러리 및 사용법에 대한 자세한 내용이 포함되어 있습니다.


[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
