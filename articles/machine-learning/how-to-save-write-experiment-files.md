---
title: 저장 & 쓰기 실험 파일
titleSuffix: Azure Machine Learning
description: 실험 입력 파일을 저장할 위치와 출력 파일을 작성 하 여 저장소 제한 오류 및 실험 대기 시간을 방지 하는 방법에 대해 알아봅니다.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 526a4f9f5542074107700b54dcf3d2a591b08b70
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144042"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Azure Machine Learning 실험을 위해 파일을 저장 하 고 작성 하는 위치
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 입력 파일을 저장할 위치와 실험에서 출력 파일을 쓸 위치를 학습 하 여 저장소 제한 오류 및 실험 대기 시간을 방지 합니다.

실행 학습은 [계산 대상](concept-compute-target.md)에서 실행 되며 외부 환경에서 격리 됩니다. 이 디자인의 목적은 실험의 재현 가능성 및 이식성을 보장 하는 것입니다. 동일 하거나 다른 계산 대상에서 동일한 스크립트를 두 번 실행 하는 경우 동일한 결과를 받게 됩니다. 이 디자인에서는 계산 대상이 완료 된 후 실행 되는 작업에 대 한 선호도를 갖지 않는 상태 비저장 계산 리소스로 취급할 수 있습니다.

## <a name="where-to-save-input-files"></a>입력 파일을 저장할 위치

계산 대상 또는 로컬 컴퓨터에서 실험을 시작 하려면 먼저 코드를 실행 해야 하는 종속성 파일 및 데이터 파일과 같이 해당 계산 대상에서 필요한 파일을 사용할 수 있는지 확인 해야 합니다.

Azure Machine Learning는 전체 원본 디렉터리를 복사 하 여 학습 스크립트를 실행 합니다. 업로드 하지 않으려는 중요 한 데이터가 있는 경우 [무시 파일](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 을 사용 하거나 원본 디렉터리에이 파일을 포함 하지 마세요. 대신 데이터 [저장소](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)를 사용 하 여 데이터에 액세스 합니다.

실험 스냅샷의 스토리지 용량 한도는 300MB 및/또는 2000개 파일입니다.

이러한 이유로 다음을 수행 하는 것이 좋습니다.

* **Azure Machine Learning [데이터 저장소](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)에 파일을 저장 합니다.** 이로 인해 실험 대기 시간 문제가 발생 하지 않으며 원격 계산 대상에서 데이터에 액세스 하는 이점이 있습니다. 즉, 인증 및 탑재는 Azure Machine Learning에서 관리 됩니다. 데이터 저장소를 원본 디렉터리로 지정 하 고 [데이터를 데이터 저장소에서 데이터 액세스 문서의 데이터](how-to-access-data.md) 저장소에 업로드 하는 방법에 대해 자세히 알아보세요.

* **데이터 파일 및 종속성 스크립트가 몇 개만 필요 하 고 데이터 저장소를 사용할 수 없는 경우** 학습 스크립트와 동일한 폴더 디렉터리에 파일을 저장 합니다. 이 폴더 `source_directory` 를 학습 스크립트나 학습 스크립트를 호출 하는 코드에서 직접 지정 합니다.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>실험 스냅숏의 저장소 제한

실험을 위해 Azure Machine Learning는 실행을 구성할 때 제안 하는 디렉터리를 기준으로 코드의 실험 스냅숏을 자동으로 만듭니다. 총 제한인 300 및/또는 2000 파일이 있습니다. 이 제한을 초과 하면 다음과 같은 오류가 표시 됩니다.

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

이 오류를 해결 하려면 데이터 저장소에 실험 파일을 저장 합니다. 데이터 저장소를 사용할 수 없는 경우 아래 표에서 가능한 대체 솔루션을 제공 합니다.

실험 &nbsp; 설명|저장소 제한 솔루션
---|---
2000 개 미만의 파일 & 데이터 저장소를 사용할 수 없음| 스냅숏 크기 제한 재정의 <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> 파일 수와 크기에 따라 몇 분 정도 걸릴 수 있습니다.
특정 스크립트 디렉터리를 사용 해야 합니다.| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
파이프라인|각 단계에 다른 하위 디렉터리 사용
Jupyter Notebook| 파일을 만들거나 `.amlignore` , 비어 있는 새 하위 디렉터리로 파일을 이동 하 고, 코드를 다시 실행 합니다.

## <a name="where-to-write-files"></a>파일을 쓸 위치

학습 실험의 격리로 인해 실행 중에 발생 하는 파일에 대 한 변경 내용이 사용자 환경 외부에서 지속 되는 것은 아닙니다. 스크립트가 compute에 대해 로컬 파일을 수정 하면 다음 실험 실행에 대해 변경 내용이 유지 되지 않으며 클라이언트 컴퓨터에 자동으로 다시 전파 되지 않습니다. 따라서 첫 번째 실험을 실행 하는 동안 변경 된 내용은 그렇지 않고 두 번째 실험의 변경 내용에 영향을 주지 않습니다.

변경 내용을 작성할 때 파일을 Azure Machine Learning 데이터 저장소에 쓰는 것이 좋습니다. [데이터 저장소에서 데이터 액세스](how-to-access-data.md)를 참조 하세요.

데이터 저장소가 필요 하지 않은 경우 `./outputs` 및/또는 폴더에 파일을 씁니다 `./logs` .

>[!Important]
> 두 개의 폴더, *출력* 및 *로그*는 Azure Machine Learning로 특수 처리를 수신 합니다. 학습 중에 및 폴더에 파일을 쓸 때 `./outputs` `./logs` 파일이 실행 기록에 자동으로 업로드 되므로 실행이 완료 되 면 파일에 액세스할 수 있습니다.

* **상태 메시지 또는 점수 매기기 결과와 같은 출력의** 경우 폴더에 파일을 기록 하 여 `./outputs` 실행 기록의 아티팩트로 유지 됩니다. 콘텐츠를 실행 기록에 업로드 하면 대기 시간이 발생할 수 있으므로이 폴더에 기록 되는 파일 수와 크기를 염두에 둘 수 있습니다. 대기 시간이 중요 한 경우 데이터 저장소에 파일을 기록 하는 것이 좋습니다.

* **기록 된 파일을 실행 기록에 로그로 저장 하려면** 폴더에 파일을 씁니다 `./logs` . 로그는 실시간으로 업로드 되므로이 메서드는 원격 실행에서 라이브 업데이트를 스트리밍하는 데 적합 합니다.

## <a name="next-steps"></a>다음 단계

* [데이터 저장소에서 데이터에 액세스 하](how-to-access-data.md)는 방법에 대해 자세히 알아보세요.

* [PYTHON SDK](how-to-create-attach-compute-sdk.md) 또는 [studio](how-to-create-attach-compute-studio.md)를 사용 하 여 계산 대상을 만드는 방법에 대해 자세히 알아보세요.
