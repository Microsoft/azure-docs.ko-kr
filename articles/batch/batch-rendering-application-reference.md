---
title: 렌더링 애플리케이션 사용 - Azure Batch
description: Azure Batch를 렌더링 애플리케이션에 사용하는 방법
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 4c93abdfb5c523d48ce115ed7d3251a346937f5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775392"
---
# <a name="rendering-applications"></a>렌더링 애플리케이션

렌더링 애플리케이션은 Batch 작업 및 태스크를 만드는 데 사용됩니다. task 명령줄 속성은 적합한 명령줄과 매개 변수를 지정합니다.  작업 태스크를 만드는 가장 쉬운 방법은 [이 문서](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer)에서 지정한 대로 Batch Explorer 템플릿을 사용하는 것입니다.  템플릿을 보고, 필요하면 만든 버전을 수정할 수 있습니다. 

이 문서에서는 각각의 렌더링 애플리케이션을 실행하는 방법을 간단히 설명합니다.

## <a name="rendering-with-autodesk-3ds-max"></a>Autodesk 3ds Max로 렌더링

### <a name="renderer-support"></a>렌더러 지원

3ds Max에 내장된 렌더러 외에도 렌더링 VM 이미지에 다음 렌더러를 사용할 수 있고 3ds Max 장면 파일에서 참조할 수 있습니다.

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Task 명령줄

`3dsmaxcmdio.exe` 애플리케이션을 호출하여 풀 노드에서 명령줄 렌더링을 수행합니다.  이 애플리케이션은 작업이 실행될 때 경로에 있습니다. `3dsmaxcmdio.exe` 응용 프로그램은 `3dsmaxcmd.exe` 응용 프로그램과 같은 사용할 수 있는 매개 변수를 가지며 이에 대해서는 [3ds Max 도움말 설명서](https://help.autodesk.com/view/3DSMAX/2018/ENU/)(렌더링 | 명령줄 렌더링 섹션)에서 설명하고 있습니다.

예를 들면 다음과 같습니다.

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

참고:

* 자산 파일을 찾을 수 있게 세심한 주의가 필요합니다.  **자산 추적** 창이나 명령줄에서 `-bitmapPath` 매개 변수를 사용하여 경로가 정확하고 상대적인지 확인합니다.
* 자산 찾기 실패 등, 작업이 실행될 때 3ds Max에서 작성한 `stdout.txt` 파일을 확인하여 렌더링에 문제가 있는지 확인합니다.

### <a name="batch-explorer-templates"></a>Batch Explorer 템플릿

풀 및 작업 템플릿은 Batch Explorer의 **갤러리**에서 액세스할 수 있습니다.  템플릿 원본 파일은 [GitHub의 Batch Explorer 데이터 리포지토리](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax)에서 사용할 수 있습니다.

## <a name="rendering-with-autodesk-maya"></a>Autodesk Maya로 렌더링

### <a name="renderer-support"></a>렌더러 지원

Maya에 내장된 렌더러 외에도 렌더링 VM 이미지에 다음 렌더러를 사용할 수 있고 3ds Max 장면 파일에서 참조할 수 있습니다.

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Task 명령줄

`renderer.exe` 명령줄 렌더러는 task 명령줄에서 사용됩니다. 명령줄 렌더러는 [Maya 도움말](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4)에서 설명합니다.

다음 예제에서는 작업 준비 태스크를 사용하여 장면 파일과 자산을 작업 준비 작업 디렉터리에 복사하고, 출력 폴더를 사용하여 렌더링 이미지를 저장하며, 프레임 10을 렌더링합니다.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

V- Ray 렌더링의 경우 Maya 장면 파일은 일반적으로 V-Ray를 렌더러로 지정합니다.  명령줄에서도 지정할 수 있습니다.

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Arnold 렌더링의 경우 Maya 장면 파일은 일반적으로 Arnold를 렌더러로 지정합니다.  명령줄에서도 지정할 수 있습니다.

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Batch Explorer 템플릿

풀 및 작업 템플릿은 Batch Explorer의 **갤러리**에서 액세스할 수 있습니다.  템플릿 원본 파일은 [GitHub의 Batch Explorer 데이터 리포지토리](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya)에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Batch Explorer를 통해 [GitHub의 데이터 리포지토리](https://github.com/Azure/BatchExplorer-data/tree/master/ncj)의 풀과 작업 템플릿을 사용합니다.  필요한 경우 새 템플릿을 만들거나 제공된 템플릿 중 하나를 수정합니다.