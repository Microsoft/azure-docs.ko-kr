---
title: 데이터 처리 최적화
titleSuffix: Azure Machine Learning
description: 데이터 처리 속도를 최적화 하기 위한 모범 사례 및 대규모로 데이터 처리를 지 원하는 통합 Azure Machine Learning에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: c73a5c5339403ecd91d45968405682c59f2f23b4
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719277"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>Azure Machine Learning를 사용 하 여 데이터 처리 최적화

이 문서에서는 데이터 처리 속도를 로컬 및 대규모로 최적화 하는 데 도움이 되는 모범 사례에 대해 알아봅니다.

Azure Machine Learning은 데이터 처리를 위한 오픈 소스 패키지 및 프레임 워크와 통합 됩니다. 이러한 통합을 사용 하 고이 문서에서 모범 사례 권장 사항을 적용 하 여 로컬 및 대규모로 데이터 처리 속도를 향상 시킬 수 있습니다.

## <a name="parquet-and-csv-file-formats"></a>Parquet 및 CSV 파일 형식

Csv (쉼표로 구분 된 값) 파일은 데이터 처리를 위한 일반적인 파일 형식입니다. 그러나 기계 학습 작업에는 parquet 파일 형식을 지정 하는 것이 좋습니다.

[Parquet 파일](https://parquet.apache.org/) 은 데이터를 이진 열 형식으로 저장 합니다. 이 형식은 데이터를 여러 파일로 분할 해야 하는 경우에 유용 합니다. 또한이 형식을 사용 하 여 machine learning 실험의 관련 필드를 대상으로 지정할 수 있습니다. 20gb 데이터 파일에서 읽을 필요 없이 ML 모델을 학습 하는 데 필요한 열을 선택 하 여 데이터 로드를 줄일 수 있습니다. Parquet 파일은 처리 능력을 최소화 하 고 공간을 줄이기 위해 압축 될 수도 있습니다.

CSV 파일은 Excel에서 쉽게 편집 하 고 읽을 수 있으므로 일반적으로 데이터를 가져오고 내보내는 데 사용 됩니다. Csv의 데이터는 행 기반 형식으로 문자열로 저장 되며, 파일을 압축 하 여 데이터 전송 로드를 줄일 수 있습니다. 압축 되지 않은 Csv은 약 2-10에 대 한 요인을 확장 하 고 압축 된 Csv을 훨씬 더 늘릴 수 있습니다. 따라서 메모리에 5gb의 CSV가 컴퓨터에 있는 8gb RAM 보다 잘 확장 됩니다. 이 압축 동작은 데이터 전송 대기 시간을 늘릴 수 있으며, 처리할 데이터가 많은 경우에는 적합 하지 않습니다. 

## <a name="pandas-dataframe"></a>pandas 데이터 프레임

[Pandas 데이터 프레임](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) 는 일반적으로 데이터 조작 및 분석에 사용 됩니다. `Pandas` 1gb 미만의 데이터 크기에 적합 하지만 `pandas` 파일 크기가 약 1gb에 도달 하면 데이터 프레임의 처리 시간이 느려집니다. 이 속도는 저장소에 있는 데이터의 크기가 데이터 프레임의 데이터 크기와 동일 하지 않기 때문입니다. 예를 들어 CSV 파일의 데이터는 데이터 프레임에서 최대 10 번 확장 될 수 있으므로 1gb CSV 파일은 데이터 프레임에서 10gb가 될 수 있습니다.

`Pandas` 단일 스레드를 의미 합니다. 즉, 단일 CPU에서 작업이 한 번에 하나씩 수행 됩니다. 분산 백 엔드를 사용 하 여 래핑하는 [modin](https://modin.readthedocs.io/en/latest/) 같은 패키지를 사용 하 여 단일 Azure Machine Learning 계산 인스턴스에서 여러 가상 cpu로 워크 로드를 쉽게 병렬화 할 수 있습니다 `Pandas` .

및를 사용 하 여 작업을 병렬 처리 하려면 `Modin` 이 코드 줄을로 변경 하면 [Dask](https://dask.org) `import pandas as pd` `import modin.pandas as pd` 됩니다.

## <a name="dataframe-out-of-memory-error"></a>데이터 프레임: 메모리 부족 오류가 발생 했습니다. 

일반적으로 데이터 프레임가 컴퓨터에서 사용 가능한 RAM을 초과 하면 *메모리 부족* 오류가 발생 합니다. 이 개념은 또는와 같은 분산 프레임 워크에도 적용 됩니다 `Modin` `Dask` .  즉, 작업에서 클러스터의 각 노드에 있는 메모리에 데이터 프레임를 로드 하려고 시도 하지만이 작업을 수행 하는 데 사용할 수 있는 RAM이 충분 하지 않습니다.

한 가지 해결 방법은 메모리에 데이터 프레임에 맞게 RAM을 늘리는 것입니다. 계산 크기와 처리 능력에는 RAM 크기의 두 배를 포함 하는 것이 좋습니다. 따라서 데이터 프레임가 10gb 인 경우 최소 20gb의 RAM이 있는 계산 대상을 사용 하 여 데이터 프레임가 메모리에 쉽게 맞고 처리 될 수 있도록 합니다. 

여러 가상 Cpu 인 vCPU의 경우 컴퓨터에서 각 vCPU가 가질 수 있는 RAM에 한 파티션이 적합 함을 명심 해야 합니다. 즉, 16gb RAM 4 vCPUs가 있는 경우 각 Vcpus 당 2gb 데이터 프레임 필요 합니다.

### <a name="local-vs-remote"></a>로컬 및 원격

Azure Machine Learning로 프로 비전 한 원격 VM과 로컬 PC에서 작업 하는 경우 특정 pandas 데이터 프레임 명령이 더 빠르게 수행 되는 것을 알 수 있습니다. 일반적으로 로컬 PC에는 사용 하도록 설정 된 페이지 파일이 있습니다 .이 파일을 사용 하면 하드 드라이브가 RAM 확장으로 사용 됩니다. 현재는 페이지 파일 없이 Azure Machine Learning Vm이 실행 되므로 사용 가능한 실제 RAM 만큼 데이터만 로드할 수 있습니다. 

계산을 많이 수행 하는 작업의 경우 더 큰 VM을 선택 하 여 처리 속도를 개선 하는 것이 좋습니다.

[사용 가능한 VM 시리즈 및 Azure Machine Learning 크기](concept-compute-target.md#supported-vm-series-and-sizes) 에 대해 자세히 알아보세요. 

RAM 사양에 대해서는 해당 VM 시리즈 페이지 (예: [Dv2-Dsv2 series](../virtual-machines/dv2-dsv2-series-memory.md) 또는 [NC 시리즈](../virtual-machines/nc-series.md))를 참조 하세요.

### <a name="minimize-cpu-workloads"></a>CPU 작업 최소화

컴퓨터에 RAM을 더 추가할 수 없는 경우 CPU 워크 로드를 최소화 하 고 처리 시간을 최적화 하는 데 도움이 되는 다음 기술을 적용할 수 있습니다. 이러한 권장 사항은 단일 시스템과 분산 시스템 모두에 적용 됩니다.

방법 | Description
----|----
압축 | 더 작은 메모리를 사용 하 고 계산 결과에 큰 영향을 주지 않는 방식으로 데이터에 대해 다른 표현을 사용 합니다.<br><br>*예:* 항목 당 약 10 바이트 이상의 문자열로 항목을 저장 하는 대신 1 바이트로 저장할 수 있는 부울, True 또는 False로 저장 합니다.
청크 | 데이터 집합 (청크)의 메모리에 데이터를 로드 하거나, 한 번에 하나의 하위 집합을 처리 하거나, 여러 하위 집합을 병렬로 처리 합니다. 이 메서드는 모든 데이터를 처리 해야 하지만 한 번에 모든 데이터를 메모리에 로드 하지 않아도 되는 경우에 가장 잘 작동 합니다. <br><br>*예:* 한 번에 1 년 분량의 데이터를 처리 하는 대신 한 달에 한 번에 데이터를 로드 하 고 처리 합니다.
인덱싱 | 관심 있는 데이터를 찾을 위치를 알려 주는 요약 인 인덱스를 적용 하 고 사용 합니다. 인덱싱은 전체 집합 대신 데이터의 하위 집합을 사용 해야 하는 경우에 유용 합니다.<br><br>*예:* 연도별 판매 데이터를 월별로 정렬 한 경우에는 인덱스를 사용 하 여 처리 하려는 월을 신속 하 게 검색할 수 있습니다.

## <a name="scale-data-processing"></a>데이터 처리 크기 조정

이전 권장 사항이 충분 하지 않으며 데이터에 맞는 가상 컴퓨터를 가져올 수 없는 경우 다음을 수행할 수 있습니다. 

* 또는와 같은 프레임 워크를 사용 `Spark` `Dask` 하 여 ' 메모리 부족 ' 데이터를 처리 합니다. 이 옵션에서 데이터 프레임는 파티션 및 처리에 의해 RAM 파티션에 로드 되며 최종 결과가 마지막에 수집 됩니다.  

* 분산 프레임 워크를 사용 하 여 클러스터로 확장 합니다. 이 옵션에서 데이터 처리 로드는 병렬로 작동 하는 여러 Cpu에서 분할 되 고 처리 되며 최종 결과가 마지막에 수집 됩니다.

### <a name="recommended-distributed-frameworks"></a>권장 되는 분산 프레임 워크

다음 표에서는 코드 기본 설정 또는 데이터 크기에 따라 Azure Machine Learning와 통합 된 분산 프레임 워크를 권장 합니다.

환경 또는 데이터 크기 | 권장
------|------
잘 알고 있는 경우 `Pandas`| `Modin` 또는 `Dask` 데이터 프레임
선호 하는 경우 `Spark` | `PySpark`
1gb 미만의 데이터 | `Pandas` 로컬 **또는** 원격 Azure Machine Learning 계산 인스턴스
10gb 보다 큰 데이터의 경우| `Ray`, 또는를 사용 하 여 클러스터로 이동 `Dask``Spark`

`Dask`AZURE ML 계산 클러스터에서 패키지를 사용 하 여 클러스터 [dask-cloudprovider](https://cloudprovider.dask.org/en/latest/#azure) 를 만들 수 있습니다. 또는 `Dask` 계산 인스턴스에서 로컬로 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning를 사용 하는 데이터 수집 옵션](concept-data-ingestion.md)입니다.
* [데이터 집합을 만들고 등록](how-to-create-register-datasets.md)합니다.
