---
title: Net#을 사용해 사용자 지정 신경망 만들기
titleSuffix: Azure Machine Learning Studio
description: Net# 신경망 사양 언어를 위한 구문 가이드 Azure Machine Learning Studio에서 사용자 지정 신경망 모델을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: c352100392a5bf7b590b27b9448f7f37fb105fbe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751679"
---
# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning-studio"></a>Azure Machine Learning Studio용 Net# 인공신경망 사양 언어에 대한 가이드

Net#은 Microsoft가 개발한 언어로, 심층 신경망 네트워크나 임의 차원의 난해와 같은 복잡한 신경 네트워크 아키텍처를 정의하는 데 사용됩니다. 이미지, 비디오 또는 오디오와 같은 데이터에 대한 학습을 개선하는 데 복잡한 구조체를 사용할 수 있습니다.

Net# 아키텍처 사양은 다음 컨텍스트에서 사용할 수 있습니다.

+ Microsoft Azure Machine Learning Studio의 모든 신경망 모듈: [다중 클래스 신경망](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/multiclass-neural-network), [2클래스 신경망](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/two-class-neural-network) 및 [신경망 회귀](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Microsoft ML Server의 신경망 함수: R 언어의 경우 [NeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/neuralnet) 및 [rxNeuralNet](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxneuralnet), Python의 경우 [rx_neural_network](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/rx-neural-network)


이 문서에서는 Net#을 사용하여 사용자 지정 신경망을 개발하는 데 필요한 기본 개념과 구문을 설명합니다.

+ 신경망 요구 사항 및 기본 구성 요소를 정의하는 방법
+ Net# 사양 언어의 구문 및 키워드
+ Net#을 사용하여 만든 사용자 지정 신경망의 예



## <a name="neural-network-basics"></a>신경망 기본 사항

신경망 구조는 계층으로 이루어진 노드 및 노드 간의 가중 연결(또는 에지)로 구성됩니다. 연결은 방향성이 있고 각 연결에는 원본 노드와 대상 노드가 있습니다.

각 학습 가능한 계층(숨겨진 계층 또는 출력 계층)에는 하나 이상의 **연결 번들**이 있습니다. 연결 번들은 원본 계층 및 해당 원본 계층의 연결 지정으로 구성됩니다. 특정 번들의 모든 연결은 원본 및 대상 계층을 공유합니다. Net#에서 연결 번들은 번들의 대상 계층에 속하는 것으로 간주합니다.

Net#에서는 입력이 숨겨진 계층 및 출력에 매핑되는 방법을 사용자 지정할 수 있는 다양한 종류의 연결 번들을 지원합니다.

기본 또는 표준 번들은 원본 계층의 각 노드가 대상 계층의 모든 노드에 연결된 **전체 번들**입니다.

또한 Net#에서는 다음 네 가지 고급 연결 번들을 지원합니다.

+ **필터링된 번들**. 사용자는 원본 계층 노드와 대상 계층 노드의 위치를 사용하여 조건자를 정의할 수 있습니다. 노드는 조건자가 True일 때마다 연결됩니다.

+ **나선형 번들**. 사용자는 원본 계층에서 작은 노드 환경을 정의할 수 있습니다. 대상 계층의 각 노드는 원본 계층의 노드 환경 하나에 연결됩니다.

+ **풀링 번들** 및 **응답 정규화 번들**. 이러한 번들은 사용자가 원본 계층에서 작은 노드 환경을 정의하는 나선형 번들과 비슷합니다. 차이점은 이러한 번들의 에지 가중치는 학습할 수 없다는 점입니다. 대신 미리 정의된 함수가 원본 노드 값에 적용되어 대상 노드 값을 결정합니다.


## <a name="supported-customizations"></a>지원되는 사용자 지정

Azure Machine Learning Studio에서 만드는 신경망 모델의 아키텍처는 Net#을 사용하여 광범위하게 사용자 지정할 수 있습니다. 다음을 수행할 수 있음:

+ 숨겨진 계층을 만들고 각 계층의 노드 수를 제어합니다.
+ 계층이 서로 연결되는 방법을 지정합니다.
+ 나선 및 가중 공유 번들과 같은 특수 연결 구조를 정의합니다.
+ 여러 가지 활성화 함수를 지정합니다.

사양 언어 구문에 대한 자세한 내용은 [구조 사양](#structure-specifications)을 참조하세요.

일부 일반적인 Machine Learning 태스크에 대한 신경망 정의의 예는 [예제](#examples-of-net-usage)를 참조하세요.

## <a name="general-requirements"></a>일반 요구 사항

+ 정확히 출력 계층 1개, 입력 계층 1개 이상, 숨겨진 계층 0개 이상이 있어야 합니다.
+ 각 계층에는 개념적으로 임의 차원의 사각형 배열로 정렬된 고정된 수의 노드가 있습니다.
+ 입력 계층은 연결된 학습된 매개 변수를 포함하지 않고 인스턴스 데이터가 네트워크에 들어가는 지점을 나타냅니다.
+ 학습 가능 계층(숨겨진 계층 및 출력 계층)에는 가중치 및 편차라는 연결된 학습된 매개 변수가 있습니다.
+ 원본 및 대상 노드는 서로 다른 계층에 있어야 합니다.
+ 연결은 비순환 방식이어야 합니다. 즉, 초기 원본 노드로 돌아가는 연결 체인은 있을 수 없습니다.
+ 출력 계층은 연결 번들의 원본 계층일 수 없습니다.

## <a name="structure-specifications"></a>구조 사양

신경망 구조 사양은 세 가지 섹션인 **상수 선언**, **계층 선언**, **연결 선언**으로 구성됩니다. 또한 선택적 **공유 선언** 섹션도 있습니다. 섹션은 순서와 관계없이 지정할 수 있습니다.

## <a name="constant-declaration"></a>상수 선언

상수 선언은 선택 사항입니다. 신경망 정의에서 사용되는 값을 정의하는 수단을 제공합니다. 선언문은 식별자, 등호, 값 식 순으로 구성됩니다.

예를 들어 다음 명령문은 상수 `x`를 정의합니다.

`Const X = 28;`

상수를 동시에 두 개 이상 정의하려면 식별자 이름과 값을 중괄호로 묶고 세미콜론으로 구분합니다. 예를 들면 다음과 같습니다.

`Const { X = 28; Y = 4; }`

각 대입 식의 오른쪽은 정수, 실수, 부울 값(True/False) 또는 수치 연산 식일 수 있습니다. 예를 들면 다음과 같습니다.

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>계층 선언

계층 선언은 필수 사항입니다. 연결 번들 및 특성을 포함하여 계층의 크기와 원본을 정의합니다. 선언문은 계층 이름(input, hidden 또는 output)으로 시작하고 계층 차원(양의 정수 튜플)이 뒤따릅니다. 예를 들면 다음과 같습니다.

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ 차원의 곱은 계층의 노드 수입니다. 이 예제에 있는 차원 두 개 [5,20]은 계층에 노드 100개가 있음을 의미합니다.
+ 계층은 순서와 관계없이 선언할 수 있지만 한 가지 예외가 있습니다. 입력 계층을 두 개 이상 정의하면 계층 선언 순서가 입력 데이터의 기능 순서와 일치해야 합니다.

계층의 노드 수가 자동으로 결정되도록 지정하려면 `auto` 키워드를 사용합니다. `auto` 키워드는 계층에 따라 다른 영향을 미칩니다.

+ 입력 계층 선언에서 노드 수는 입력 데이터의 기능 수입니다.
+ 숨겨진 계층 선언에서 노드 수는 **숨겨진 노드 수**의 매개 변수 값으로 지정된 수입니다.
+ 출력 계층 선언에서 노드 수는 2클래스 분류에 대해 2, 회귀에 대해 1이고 다중 클래스 분류에 대한 출력 노드 수와 같습니다.

예를 들어 다음 네트워크 정의에서는 모든 계층 크기가 자동으로 결정되도록 허용합니다.

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

학습 가능 계층(숨겨진 계층 또는 출력 계층)에 대한 계층 선언은 선택적으로 활성화 함수라는 출력 함수를 포함할 수 있습니다(기본값: 분류 모델의 경우 **sigmoid**, 회귀 모델의 경우 **linear**). 기본값을 사용하는 경우에도 명확한 설명을 위해 필요한 경우 활성화 함수를 명시적으로 설정할 수 있습니다.

다음 출력 함수가 지원됩니다.

+ sigmoid
+ linear
+ softmax
+ rlinear
+ square
+ sqrt
+ srlinear
+ abs
+ tanh
+ brlinear

예를 들어 다음 선언은 **softmax** 함수를 사용합니다.

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>연결 선언

학습 가능 계층을 정의한 후에 바로 정의한 계층 간의 연결을 선언해야 합니다. 연결 번들 선언은 키워드 `from`으로 시작하고 번들의 원본 계층 이름 및 만들 연결 번들 종류가 뒤따릅니다.

현재 다음 5가지 연결 번들이 지원됩니다.

+ **전체** 번들 - 키워드 `all`로 나타냅니다.
+ **필터링된** 번들 - 키워드 `where`로 나타내고 조건자 식이 뒤따릅니다.
+ **나선형** 번들 - 키워드 `convolve`로 나타내고 나선 특성이 뒤따릅니다.
+ **풀링** 번들 - 키워드 **max pool** 또는 **mean pool**로 나타냅니다.
+ **응답 정규화** 번들 - 키워드 **response norm**으로 나타냅니다.

## <a name="full-bundles"></a>전체 번들

전체 연결 번들은 원본 계층의 각 노드에서 대상 계층의 각 노드로의 연결을 포함합니다. 이 번들이 기본 네트워크 연결 유형입니다.

## <a name="filtered-bundles"></a>필터링된 번들

필터링된 연결 번들 사양은 구문상 C# 람다 식과 훨씬 비슷하게 표현된 조건자를 포함합니다. 다음 예제에서는 필터링된 번들 두 개를 정의합니다.

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ `ByRow`에 대한 조건자에서 `s`는 입력 계층 `Pixels`의 사각형 노드 배열로 인덱스를 표시하는 매개 변수이고 `d`는 숨겨진 계층 `ByRow`의 노드 배열로 인덱스를 표시하는 매개 변수입니다. `s` 및 `d` 유형은 둘 다 길이가 2인 정수의 튜플입니다. 개념적으로 `s`는 `0 <= s[0] < 10` 및 `0 <= s[1] < 20` 조건의 모든 정수 쌍을 포함하고 `d`는 `0 <= d[0] < 10` 및 `0 <= d[1] < 12` 조건의 모든 정수 쌍을 포함합니다.

+ 조건자 식의 오른쪽에는 조건이 있습니다. 이 예제에서는 조건이 True가 되도록 `s` 및 `d`의 모든 값에 해당하는 원본 계층 노드에서 대상 계층 노드로의 에지가 있습니다. 따라서 이 필터 식은 s[0]이 d[0]과 같은 모든 경우에 번들이 `s`로 정의된 노드에서 `d`로 정의된 노드로의 연결을 포함함을 나타냅니다.

선택적으로 필터링된 번들의 가중치 집합을 지정할 수 있습니다. **Weights** 특성 값은 길이가 번들로 정의된 연결 수와 일치하는 부동 소수점 값의 튜플이어야 합니다. 기본적으로 가중치는 임의로 생성됩니다.

가중치 값은 대상 노드 인덱스별로 그룹화됩니다. 즉, 첫 번째 대상 노드가 K 원본 노드에 연결되면 **Weights** 튜플의 첫 번째 `K` 요소는 원본 인덱스 순서에서 첫 번째 대상 노드의 가중치입니다. 나머지 대상 노드에서 같은 원리가 적용됩니다.

가중치를 상수 값으로 직접 지정할 수 있습니다. 예를 들어 이전에 가중치를 알고 있었다면 이 구문을 사용하여 해당 가중치를 상수로 지정할 수 있습니다.

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>나선형 번들

학습 데이터에 같은 유형 구조가 있으면 일반적으로 데이터의 높은 수준 기능을 학습하는 데 나선형 연결이 사용됩니다. 예를 들어 이미지, 오디오 또는 비디오 데이터에서 공간 또는 임시 차원은 상당히 균일할 수 있습니다.

나선형 번들에는 차원을 통해 움직이는 사각형 **커널**이 적용됩니다. 기본적으로 각 커널은 로컬 환경에 적용되는 **커널 애플리케이션**이라는 가중치 집합을 정의합니다. 각 커널 애플리케이션은 **중앙 노드**라는 원본 계층의 노드에 해당합니다. 커널의 가중치는 많은 연결에서 공유됩니다. 나선형 번들에서 각 커널은 사각형이고 모든 커널 애플리케이션은 같은 크기입니다.

나선형 번들은 다음 특성을 지원합니다.

**InputShape**는 이 나선형 번들을 위한 원본 계층의 차원을 정의합니다. 값은 양의 정수 튜플이어야 합니다. 정수의 곱은 원본 계층의 노드 수와 같아야 하지만 원본 계층에 대해 선언된 차원과 일치할 필요는 없습니다. 이 튜플의 길이는 나선형 번들에 대한 **인자 수** 값이 됩니다. 일반적으로 인자 수는 함수가 사용할 수 있는 인수 또는 피연산자 수를 나타냅니다.

커널의 셰이프와 위치를 정의하려면 **KernelShape**, **Stride**, **Padding**, **LowerPad**, **UpperPad** 특성을 사용합니다.

+ **KernelShape**: (필수) 나선형 번들에 대한 각 커널의 차원을 정의합니다. 값은 길이가 번들 인자 수와 같은 양의 정수 튜플이어야 합니다. 이 튜플의 각 구성 요소는 **InputShape**의 해당하는 구성 요소보다 크지 않아야 합니다.

+ **Stride**: (선택 사항) 중앙 노드 간의 거리인 나선의 이동식 단계 크기를 정의합니다(각 차원에 대해 단계 크기 하나). 값은 길이가 번들 인자 수인 양의 정수 튜플이어야 합니다. 이 튜플의 각 구성 요소는 **KernelShape**의 해당하는 구성 요소보다 크지 않아야 합니다. 기본값은 모든 구성 요소가 1과 같은 튜플입니다.

+ **Sharing**: (선택 사항) 나선의 각 차원에 대한 가중치 공유를 정의합니다. 값은 단일 부울 값 또는 길이가 번들 인자 수인 부울 값 튜플일 수 있습니다. 단일 부울 값은 모든 구성 요소가 지정된 값과 같은 올바른 길이의 튜플로 확장됩니다. 기본값은 모든 True 값으로 구성된 튜플입니다.

+ **MapCount**: (선택 사항) 나선형 번들에 대한 기능 맵 수를 정의합니다. 값은 단일 양의 정수이거나 길이가 번들 인자 수인 양의 정수 튜플일 수 있습니다. 단일 정수 값은 첫 번째 구성 요소가 지정된 값과 같고 나머지 모든 구성 요소가 1과 같은 올바른 길이의 튜플로 확장됩니다. 기본값은 1입니다. 총 기능 맵 수는 튜플 구성 요소의 곱입니다. 전체 구성 요소에서 이 총 맵 수를 인수 분해하여 대상 노드에서 기능 맵 값을 그룹화하는 방법을 결정합니다.

+ **Weights**: (선택 사항) 번들에 대한 초기 가중치를 정의합니다. 값은 이 문서의 뒷부분에 정의된 대로 길이가 커널 수에 커널당 가중치 수를 곱한 값인 부동 소수점 값의 튜플이어야 합니다. 기본 가중치는 임의로 생성됩니다.

안쪽 여백을 제어하는 상호 배타적인 두 가지 속성 집합이 있습니다.

+ **Padding**: (선택 사항) **기본 패딩 체계**를 사용하여 입력의 안쪽 여백을 지정해야 할지를 결정합니다. 값은 단일 부울 값 또는 길이가 번들 인자 수인 부울 값 튜플일 수 있습니다.

    단일 부울 값은 모든 구성 요소가 지정된 값과 같은 올바른 길이의 튜플로 확장됩니다.

    차원 값이 True이면 추가 커널 애플리케이션을 지원하도록 해당 차원에서 값이 0인 셀을 사용하여 원본의 안쪽 여백이 논리적으로 지정되므로 해당 차원에 있는 첫 번째 및 마지막 커널의 중앙 노드는 원본 계층에서 해당 차원에 있는 첫 번째 및 마지막 노드입니다. 따라서 각 차원의 "더미" 노드 수는 `(InputShape[d] - 1) / Stride[d] + 1` 커널을 안쪽 여백이 지정된 원본 계층에 정확히 맞추도록 자동으로 결정됩니다.

    차원 값이 False이면 커널은 각 측면에 남아 있는 노드 수가 같도록 정의됩니다(최대 차이 1). 이 특성의 기본값은 모든 구성 요소가 False와 같은 튜플입니다.

+ **UpperPad** 및 **LowerPad**: (선택 사항) 사용할 안쪽 여백 크기를 더 구체적으로 제어합니다. **중요:** 위의 **Padding** 속성이 정의되지 ***않은*** 경우에만 이러한 특성을 정의할 수 있습니다. 값은 길이가 번들 인자 수인 정수 값 튜플이어야 합니다. 이러한 특성을 지정하면 "더미" 노드가 입력 계층에 있는 각 차원의 아래쪽 및 위쪽 끝에 추가됩니다. 각 차원의 아래쪽 및 위쪽 끝에 추가되는 노드 수는 각각 **LowerPad**[i] 및 **UpperPad**[i]를 통해 결정됩니다.

    커널이 "실제" 노드에만 해당하고 "더미" 노드에 해당하지 않도록 하려면 다음 조건을 충족해야 합니다.
  - **LowerPad**의 각 구성 요소는 `KernelShape[d]/2`보다 작아야 합니다.
  - **UpperPad**의 각 구성 요소는 `KernelShape[d]/2`보다 크지 않아야 합니다.
  - 이러한 특성의 기본값은 모든 구성 요소가 0과 같은 튜플입니다.

    **Padding** = true로 설정하면 최대한 많은 안쪽 여백을 사용하여 "실제" 입력 내 커널의 "중심"을 유지할 수 있습니다. 이 경우 출력 크기를 계산하는 수식이 약간 변경됩니다. 일반적으로 출력 크기 *D*는 `D = (I - K) / S + 1`로 계산됩니다. 여기서 `I`는 입력 크기, `K`는 커널 크기, `S`는 진행 속도, `/`는 정수 나누기(0으로 반올림)입니다. UpperPad = [1, 1]로 설정한 경우 입력 크기 `I`는 사실상 29이므로 `D = (29 - 5) / 2 + 1 = 13`입니다. 그러나 **Padding** = true인 경우 기본적으로 `I`는 `K - 1`만큼 증가합니다. 따라서 `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14`입니다. **UpperPad** 및 **LowerPad** 값을 지정하면 **Padding** = true로 설정한 경우보다 안쪽 여백을 훨씬 구체적으로 제어할 수 있습니다.

나선형 네트워크 및 해당 애플리케이션에 대한 자세한 내용은 다음 문서를 참조하세요.

+ [http://deeplearning.net/tutorial/lenet.html](http://deeplearning.net/tutorial/lenet.html)
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>풀링 번들

**풀링 번들**은 나선형 연결과 비슷한 기하 도형에 적용되지만 원본 노드 값에 대한 미리 정의된 함수를 사용하여 대상 노드 값을 도출합니다. 또한 풀링 번들에는 학습 가능 상태가 없습니다(가중치 또는 편차). 풀링 번들은 **Sharing**, **MapCount**, **Weights**를 제외하고 모든 나선형 특성을 지원합니다.

일반적으로 인접 풀링 단위로 요약된 커널은 겹치지 않습니다. 각 차원에서 Stride[d]가 KernelShape[d]와 같으면 얻은 계층은 일반적으로 나선형 신경망에서 적용되는 기존 로컬 풀링 계층입니다. 각 대상 노드에서는 원본 계층에 있는 커널 활동의 최대값이나 평균을 계산합니다.

다음 예제에서는 풀링 번들을 보여 줍니다.

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ 번들의 인자 수는 3입니다. 즉, 튜플인 `InputShape`, `KernelShape` 및 `Stride`의 길이입니다.
+ 원본 계층의 노드 수는 `5 * 24 * 24 = 2880`입니다.
+ **KernelShape** 및 **Stride**가 같으므로 이는 기존 로컬 풀링 계층입니다.
+ 대상 계층의 노드 수는 `5 * 12 * 12 = 1440`입니다.

풀링 계층에 대한 자세한 내용은 다음 문서를 참조하세요.

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf)(섹션 3.4)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>응답 정규화 번들

**응답 정규화**는 Geoffrey Hinton 외 연구자가 [ImageNet Classification with Deep Convolutional Neural Networks](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) 논문에서 처음 도입한 로컬 정규화 체계입니다.

응답 정규화는 신경망에서 일반화를 지원하는 데 사용됩니다. 신경 하나가 매우 높은 활성화 수준에서 실행되면 로컬 응답 정규화 계층에서는 주위 신경의 활성화 수준을 억제합니다. 이 작업에는 매개 변수 3개(`α`, `β` 및 `k`)와 나선형 구조(또는 환경 셰이프)를 사용합니다. 대상 계층 **y**의 모든 신경은 원본 계층의 신경 **x**에 해당합니다. **y**의 활성화 수준은 다음 공식으로 제공됩니다. 여기서 `f`는 신경의 활성화 수준이고 `Nx`는 나선형 구조를 통해 정의된 대로 **x**의 환경에 신경이 포함된 집합 또는 커널입니다.

![나선형 구조에 대한 수식](./media/azure-ml-netsharp-reference-guide/formula_large.png)

응답 정규화 번들은 **Sharing**, **MapCount**, **Weights**를 제외하고 모든 나선형 특성을 지원합니다.

+ 커널에서 ***x***와 같은 맵에 신경이 포함되면 정규화 체계를 **동일 맵 정규화**라고 합니다. 동일 맵 정규화를 정의하려면 **InputShape**의 첫 번째 좌표에 값 1이 있어야 합니다.

+ 커널에서 ***x***와 같은 공간 위치에 신경이 포함되지만 신경이 다른 맵에 있으면 이 정규화 체계를 **맵 간 정규화**라고 합니다. 이 응답 정규화 유형은 실제 신경에 있는 유형에서 영향을 받은 측면 억제 형태를 구현하여 여러 맵에서 계산된 신경 출력 가운데 큰 활성화 수준에 대한 경쟁을 조성합니다. 맵 간 정규화를 정의하려면 첫 번째 좌표가 1보다 크고 맵 수보다 큰 정수여야 하고 나머지 좌표는 값이 1이어야 합니다.

응답 정규화 번들은 미리 정의된 함수를 원본 노드 값에 적용하여 대상 노드 값을 결정하므로 학습 가능 상태(가중치 또는 편차)가 없습니다.

> [!NOTE]
> 대상 계층의 노드는 커널의 중앙 노드인 신경에 해당합니다. 예를 들어 `KernelShape[d]`가 홀수이면 `KernelShape[d]/2`는 중앙 커널 노드에 해당합니다. `KernelShape[d]`가 짝수인 경우에는 중앙 노드가 `KernelShape[d]/2 - 1`에 있습니다. 따라서 `Padding[d]`가 False이면 첫 번째 및 마지막 `KernelShape[d]/2` 노드에 해당하는 노드가 대상 계층에 없습니다. 이 상황을 피하려면 **Padding**을 [true, true, …, true]로 정의합니다.

위에 설명된 네 가지 특성 이외에 응답 정규화 번들은 다음 특성도 지원합니다.

+ **Alpha**: (필수) 위의 공식에서 `α`에 해당하는 부동 소수점 값을 지정합니다.
+ **Beta**: (필수) 위의 공식에서 `β`에 해당하는 부동 소수점 값을 지정합니다.
+ **Offset**: (선택 사항) 위의 공식에서 `k`에 해당하는 부동 소수점 값을 지정합니다. 기본값은 1입니다.

다음 예제에서는 이들 특성을 사용하여 응답 정규화 번들을 정의합니다.

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ 원본 계층에는 맵 5개가 포함되고 각 맵은 총 노드 수가 1440개인 12x12 차원입니다.
+ **KernelShape** 값은 이 계층이 3x3 사각형 환경이 있는 같은 맵 정규화 계층임을 나타냅니다.
+ **Padding**의 기본값은 False이므로 대상 계층의 각 차원에는 노드가 10개만 있습니다. 원본 계층의 각 노드에 해당하는 단일 노드를 대상 계층에 포함하려면 Padding = [true, true, true]를 추가하고 RN1 크기를 [5, 12, 12]로 변경합니다.

## <a name="share-declaration"></a>공유 선언

Net#에서는 선택적으로 공유 가중치를 사용하여 여러 번들을 정의하도록 지원합니다. 구조가 같으면 번들 두 개의 가중치를 공유할 수 있습니다. 다음 구문에서는 공유 가중치를 사용하여 번들을 정의합니다.

```Net#
share-declaration:
  share    {    layer-list    }
  share    {    bundle-list    }
  share    {    bias-list    }

  layer-list:
    layer-name    ,    layer-name
    layer-list    ,    layer-name

  bundle-list:
    bundle-spec    ,    bundle-spec
    bundle-list    ,    bundle-spec

  bundle-spec:
    layer-name    =>     layer-name

  bias-list:
    bias-spec    ,    bias-spec
    bias-list    ,    bias-spec

  bias-spec:
    1    =>    layer-name

  layer-name:
    identifier
```

예를 들어 다음 공유 선언은 계층 이름을 지정하여 가중치 및 편차를 둘 다 공유하도록 지정합니다.

```Net#
Const {
  InputSize = 37;
  HiddenSize = 50;
  }
input {
  Data1 [InputSize];
  Data2 [InputSize];
  }
hidden {
  H1 [HiddenSize] from Data1 all;
  H2 [HiddenSize] from Data2 all;
  }
output Result [2] {
  from H1 all;
  from H2 all;
  }
share { H1, H2 } // share both weights and biases
```

+ 입력 기능은 크기가 같은 입력 계층 두 개로 분할됩니다.
+ 숨겨진 계층에서는 입력 계층 두 개에서 더 높은 수준의 기능을 계산합니다.
+ 공유 선언은 개별 입력에서 같은 방법으로 *H1* 및 *H2*를 계산하도록 지정합니다.

또는 다음과 같이 개별 공유 선언 두 개를 사용하여 이를 지정할 수 있습니다.

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

계층에 단일 번들이 포함되어 있을 때만 약식 양식을 사용할 수 있습니다. 일반적으로 관련 구조가 동일하여 크기, 나선형 기하 도형 등이 같을 때만 공유가 가능합니다.

## <a name="examples-of-net-usage"></a>Net# 사용의 예

이 섹션에서는 Net#을 사용하여 숨겨진 계층을 추가하고 숨겨진 계층이 다른 계층을 조작하는 방법을 정의하고 나선형 네트워크를 빌드하는 방법의 몇 가지 예를 제공합니다.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>단순 사용자 지정 신경망 정의: "Hello World" 예제

이 단순 예제에서는 단일 숨겨진 계층이 있는 신경망 모델을 만드는 방법을 보여 줍니다.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

이 예제에서는 다음과 같은 몇 가지 기본 명령을 보여 줍니다.

+ 첫 번째 줄에서는 이름이 `Data`인 입력 계층을 정의합니다. `auto` 키워드를 사용하는 경우 신경망에 입력 예제의 모든 기능 열이 자동으로 포함됩니다.
+ 두 번째 줄에서는 숨겨진 계층을 만듭니다. 노드 200개가 포함된 숨겨진 계층에 이름 `H`가 할당됩니다. 이 계층은 입력 계층에 완전히 연결됩니다.
+ 세 번째 줄에서는 이름이 `Out`이고 출력 노드 10개가 포함된 출력 계층을 정의합니다. 신경망을 분류에 사용하는 경우 클래스당 하나의 출력 노드가 있습니다. 키워드 **sigmoid**는 출력 계층에 적용된 출력 함수를 나타냅니다.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>여러 숨겨진 계층 정의: 컴퓨터 비전 예제

다음 예제에서는 여러 사용자 지정 숨겨진 계층을 사용하여 약간 더 복잡한 신경망을 정의하는 방법을 보여 줍니다.

```Net#
// Define the input layers
input Pixels [10, 20];
input MetaData [7];

// Define the first two hidden layers, using data only from the Pixels input
hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;

// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
hidden Gather [100]
{
from ByRow all;
from ByCol all;
}

// Define the output layer and its sources
output Result [10]
{
from Gather all;
from MetaData all;
}
```

이 예제에서는 신경망 사양 언어의 여러 가지 기능을 보여 줍니다.

+ 구조에는 2개의 입력 계층인 `Pixels` 및 `MetaData`가 있습니다.
+ `Pixels` 계층은 대상 계층이 `ByRow` 및 `ByCol`인 두 연결 번들에 대한 원본 계층입니다.
+ `Gather` 및 `Result` 계층은 여러 연결 번들의 대상 계층입니다.
+ 출력 계층인 `Result`는 각각 두 번째 수준 숨겨진 계층 `Gather` 및 입력 계층 `MetaData`가 대상 계층인 두 연결 번들의 대상 계층입니다.
+ 숨겨진 계층인 `ByRow` 및 `ByCol`은 조건자 식을 사용하여 필터링된 연결을 지정합니다. 보다 정확하게, [x, y]에 있는 `ByRow`의 노드는 첫 번째 인덱스 좌표가 노드의 첫 번째 좌표인 x와 동일한 `Pixels`의 노드에 연결됩니다. 이와 마찬가지로 [x, y]에 있는 `ByCol`의 노드는 두 번째 인덱스 좌표가 노드의 두 번째 좌표인 y 중 하나 내에 있는 `Pixels`의 노드에 연결됩니다.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>다중 클래스 분류에 대한 나선형 네트워크 정의: 숫자 인식 예제

숫자를 인식하도록 고안된 다음 네트워크 정의에서는 신경망 사용자 지정을 위한 몇 가지 고급 기법을 보여 줍니다.

```Net#
input Image [29, 29];
hidden Conv1 [5, 13, 13] from Image convolve
  {
  InputShape  = [29, 29];
  KernelShape = [ 5,  5];
  Stride      = [ 2,  2];
  MapCount    = 5;
  }
hidden Conv2 [50, 5, 5]
from Conv1 convolve
  {
  InputShape  = [ 5, 13, 13];
  KernelShape = [ 1,  5,  5];
  Stride      = [ 1,  2,  2];
  Sharing     = [false, true, true];
  MapCount    = 10;
  }
hidden Hid3 [100] from Conv2 all;
output Digit [10] from Hid3 all;
```

+ 구조에는 단일 입력 계층인 `Image`가 있습니다.
+ 키워드 `convolve`는 `Conv1` 및 `Conv2`라는 계층이 나선형 계층임을 나타냅니다. 이러한 각 계층 선언 뒤에는 나선 특성 목록이 나옵니다.
+ 네트워크에는 두 번째 숨겨진 계층인 `Conv2`에 완전히 연결된 세 번째 숨겨진 계층인 `Hid3`이 있습니다.
+ `Digit` 출력 계층은 세 번째 계층인 `Hid3`에만 연결됩니다. 키워드 `all`은 출력 계층이 `Hid3`에 완전히 연결되었음을 나타냅니다.
+ 나선 인자는 3: 튜플의 길이 `InputShape`, `KernelShape`를 `Stride`, 및 `Sharing`합니다.
+ 커널당 가중치 수는 `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26`입니다. 또는 `26 * 50 = 1300`입니다.
+ 다음과 같이 각 숨겨진 계층에서 노드를 계산할 수 있습니다.

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ 총 노드 수는 계층의 선언된 차원인 [50, 5, 5]를 사용하여 `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`와 같이 계산할 수 있습니다.
+ `Sharing[d]`가 `d == 0`에 대해서만 False이므로 커널 수는 `MapCount * NodeCount\[0] = 10 * 5 = 50`입니다.

## <a name="acknowledgements"></a>승인

신경망 아키텍처를 사용자 지정하기 위한 Net# 언어는 Microsoft에서 Shon Katzenberger(설계자, Machine Learning) 및 Alexey Kamenev(소프트웨어 엔지니어, Microsoft Research)에 의해 개발되었습니다. 내부적으로 이미지 검색에서 텍스트 분석에 이르기까지 다양한 기계 학습 프로젝트 및 애플리케이션에 사용됩니다. 자세한 내용은 [Azure Machine Learning Studio의 신경망 - Net# 소개](https://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)를 참조하세요.
