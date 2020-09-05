---
title: ParallelRunStep 디버그 및 문제 해결
titleSuffix: Azure Machine Learning
description: Python용 Azure Machine Learning SDK의 기계 학습 파이프라인에서 ParallelRunStep을 디버그하고 문제를 해결합니다. 파이프라인에서 개발 중 발생하는 일반적인 문제와, 원격 실행 전과 도중에 스크립트를 디버그하는 데 도움이 되는 팁에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting
ms.reviewer: jmartens, larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 07/16/2020
ms.openlocfilehash: 0b914059567e9a513b605ce733407465b625a909
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950020"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>ParallelRunStep 디버그 및 문제 해결
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)에서 [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) 클래스의 디버그 및 문제 해결 방법에 대해 알아봅니다.

## <a name="testing-scripts-locally"></a>로컬에서 스크립트 테스트

기계 학습 파이프라인은 [로컬에서 스크립트 테스트 섹션](how-to-debug-pipelines.md#debug-scripts-locally)을 참조하세요. ParallelRunStep은 ML 파이프라인에서 한 단계로 실행되므로 동일한 답변이 모두에게 적용됩니다.

## <a name="debugging-scripts-from-remote-context"></a>원격 컨텍스트에서 스크립트 디버그

채점 스크립트의 로컬 디버깅을 실제 파이프라인에서의 디버깅으로 전환하는 것은 어려운 작업일 수 있습니다. 포털에서 로그 찾기에 대한 자세한 내용은 [원격 컨텍스트에서의 스크립트 디버깅에 관한 기계 학습 파이프라인 섹션](how-to-debug-pipelines.md#finding-and-reading-pipeline-log-files)을 참조하세요. 해당 섹션의 정보는 ParallelRunStep에도 적용됩니다.

예를 들어 로그 파일 `70_driver_log.txt`에는 ParallelRunStep 코드를 시작하는 컨트롤러의 정보가 포함됩니다.

ParallelRunStep 작업의 분산 특성으로 인해 여러 원본의 로그가 있습니다. 그러나 다음과 같이 높은 수준의 정보를 제공하는 두 통합 파일이 생성됩니다.

- `~/logs/overview.txt`: 이 파일은 지금까지 생성된 미니 일괄 처리(작업)의 수와 지금까지 처리된 미니 일괄 처리 수에 대한 높은 수준의 정보를 제공합니다. 이 끝에서는 작업의 결과를 보여 줍니다. 작업이 실패할 경우 오류 메시지와, 문제 해결을 시작할 지점이 표시됩니다.

- `~/logs/sys/master.txt`:이 파일은 실행 중인 작업의 주 노드 (orchestrator 라고도 함)를 제공 합니다. 작업 만들기, 진행률 모니터링, 실행 결과가 포함됩니다.

EntryScript 도우미 및 print 문을 사용하여 항목 스크립트에서 생성된 로그는 다음 파일에 있습니다.

- `~/logs/user/<ip_address>/<node_name>.log.txt`: 이러한 파일은 EntryScript 도우미를 사용 하 여 entry_script에서 작성 한 로그입니다. Entry_script의 print 문(stdout)도 포함합니다.

스크립트의 오류에 대한 간략한 해석은 다음과 같습니다.

- `~/logs/user/error.txt`: 이 파일은 스크립트의 오류를 요약하려 합니다.

스크립트의 오류에 대한 자세한 내용은 다음과 같습니다.

- `~/logs/user/error/`: Throw된 모든 오류 및 노드에서 구성된 전체 스택 추적을 포함합니다.

각 노드가 점수 스크립트를 실행하는 방법을 완전히 해석하려면 각 노드에 대한 개별 프로세스 로그를 확인합니다. 프로세스 로그는 작업자 노드에서 그룹화한 `sys/node` 폴더에 있습니다.

- `~/logs/sys/node/<node_name>.txt`:이 파일은 작업자에서 선택 하거나 완료할 때 각 미니 일괄 처리에 대 한 자세한 정보를 제공 합니다. 각 미니 일괄 처리에 대해 이 파일에는 다음이 포함됩니다.

    - 작업자 프로세스의 IP 주소 및 PID 
    - 전체 항목 수, 성공적으로 처리된 항목 수, 실패한 항목 수
    - 시작 시간, 기간, 처리 시간 및 실행 메서드 시간.

각 작업자에 대한 프로세스의 리소스 사용 정보도 확인할 수 있습니다. 이 정보는 CSV 형식이며 `~/logs/sys/perf/overview.csv`에 있습니다. 각 프로세스에 대 한 정보는에서 확인할 수 있습니다 `~logs/sys/processes.csv` .

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>원격 컨텍스트의 내 사용자 스크립트는 어떻게 기록하나요?
ParallelRunStep는 process_count_per_node 기반으로 한 노드에서 여러 프로세스를 실행할 수 있습니다. 노드의 각 프로세스에서 로그를 구성 하 고 print 및 log 문을 결합 하려면 아래와 같이 ParallelRunStep로 거를 사용 하는 것이 좋습니다. EntryScript에서로 거를 가져오고 로그가 포털의 **로그/사용자** 폴더에 표시 되도록 합니다.

**로거를 사용 하는 샘플 항목 스크립트:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>조회 테이블을 포함하는 하나 이상의 파일 등, 측면 입력을 모든 작업자에게 전달하려면 어떻게 해야 하나요?

사용자는 ParalleRunStep의 side_inputs 매개 변수를 사용 하 여 스크립트에 참조 데이터를 전달할 수 있습니다. Side_inputs로 제공 되는 모든 데이터 집합은 각 작업자 노드에 탑재 됩니다. 사용자는 인수를 전달 하 여 탑재 위치를 가져올 수 있습니다.

참조 데이터를 포함 하는 데이터 [집합](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) 을 생성 하 고 작업 영역에 등록 합니다. `ParallelRunStep`의 `side_inputs` 매개 변수에 전달합니다. 또한 섹션에 해당 경로를 추가 하 여 `arguments` 탑재 된 경로에 쉽게 액세스할 수 있습니다.

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

그런 다음, 다음과 같이 추론 스크립트(예: init () 메서드)에서 액세스할 수 있습니다.

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

## <a name="next-steps"></a>다음 단계

* [Azureml-파이프라인 단계](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) 패키지에 대 한 도움말은 SDK 참조를 참조 하세요. ParallelRunStep 클래스에 대 한 참조 [설명서](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?view=azure-ml-py) 를 봅니다.

* ParallelRunStep와 함께 파이프라인 사용에 대 한 [고급 자습서](tutorial-pipeline-batch-scoring-classification.md) 를 따르세요. 이 자습서에서는 다른 파일을 측면 입력으로 전달 하는 방법을 보여 줍니다. 
