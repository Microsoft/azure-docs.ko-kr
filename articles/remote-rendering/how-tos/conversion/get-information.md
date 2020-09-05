---
title: 변환에 대 한 정보 가져오기
description: 변환에 대 한 정보 가져오기
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 529bfb61b3af7040f3656c04071683841f5abe86
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870292"
---
# <a name="get-information-about-conversions"></a>변환에 대 한 정보 가져오기

## <a name="information-about-a-conversion-the-result-file"></a>변환에 대 한 정보: 결과 파일

변환 서비스는 자산을 변환할 때 모든 문제에 대 한 요약을 "결과 파일"에 기록 합니다. 예를 들어 파일을 변환 하는 경우 `buggy.gltf` 출력 컨테이너에는 라는 파일이 포함 됩니다 `buggy.result.json` .

결과 파일은 변환 중에 발생 한 오류 및 경고를 나열 하 고 결과 요약 (또는 중 하나)을 제공 합니다 `succeeded` `failed` `succeeded with warnings` .
결과 파일은 개체의 JSON 배열로 구성 되며, 각 개체는,,, 및 중 하나인 문자열 속성을 포함 `warning` 합니다 `error` `internal warning` `internal error` `result` . 최대 한 개의 오류 ( `error` 또는 `internal error` )가 있으며 항상 하나는 있습니다 `result` .

## <a name="example-result-file"></a>예제 *결과* 파일

다음 예제에서는 arrAsset를 성공적으로 생성 한 변환을 설명 합니다. 그러나 누락 된 질감이 있으므로 결과 arrAsset가 의도 된 것이 아닐 수 있습니다.

```JSON
[
  {"warning":"4004","title":"Missing texture","details":{"texture":"buggy_baseColor.png","material":"buggy_col"}},
  {"result":"succeeded with warnings"}
]
```

## <a name="information-about-a-converted-model-the-info-file"></a>변환 된 모델에 대 한 정보: 정보 파일

변환 서비스에 의해 생성 된 arrAsset 파일은 렌더링 서비스에서 사용 하기 위한 것입니다. 그러나 렌더링 세션을 시작 하지 않고 모델에 대 한 정보에 액세스 하려는 경우가 있을 수 있습니다. 이 워크플로를 지원 하기 위해 변환 서비스는 출력 컨테이너의 arrAsset 파일 옆에 JSON 파일을 배치 합니다. 예를 들어 파일을 변환 하는 경우 `buggy.gltf` 출력 컨테이너에는 변환 된 자산 옆에 이라는 파일이 포함 됩니다 `buggy.info.json` `buggy.arrAsset` . 원본 모델, 변환 된 모델 및 변환 자체에 대 한 정보를 포함 합니다.

## <a name="example-info-file"></a>예제 *정보* 파일

다음은 라는 파일을 변환 하 여 생성 된 *정보* 파일의 예입니다 `buggy.gltf` .

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>정보 파일의 정보

### <a name="the-files-section"></a>*파일* 섹션

이 섹션에는 제공 된 파일 이름이 포함 되어 있습니다.

* `input`: 소스 파일의 이름입니다.
* `output`: 사용자가 기본이 아닌 이름을 지정 하는 경우 출력 파일의 이름입니다.

### <a name="the-conversionsettings-section"></a>*ConversionSettings* 섹션

이 섹션에서는 모델을 변환할 때 지정 된 [ConversionSettings](configure-model-conversion.md#settings-file) 복사본을 보관 합니다.

### <a name="the-inputinfo-section"></a>*Inputinfo* 섹션

이 섹션에서는 원본 파일 형식에 대 한 정보를 기록 합니다.

* `sourceAssetExtension`: 소스 파일의 파일 확장명입니다.
* `sourceAssetFormat`: 원본 파일 형식에 대 한 설명입니다.
* `sourceAssetFormatVersion`: 소스 파일 형식의 버전입니다.
* `sourceAssetGenerator`: 사용할 수 있는 경우 소스 파일을 생성 한 도구의 이름입니다.

### <a name="the-inputstatistics-section"></a>*Inputstatistics* 섹션

이 섹션에서는 원본 장면에 대 한 정보를 제공 합니다. 이 섹션의 값과 원본 모델을 만든 도구의 해당 값 사이에는 종종 차이가 있습니다. 이러한 차이는 내보내기 및 변환 단계에서 모델이 수정 되기 때문에 예상 됩니다.

* `numMeshes`: 각 파트가 단일 자료를 참조할 수 있는 메시 파트의 수입니다.
* `numFaces`: 전체 모델에 있는 _삼각형_ 의 총 수입니다. 변환 하는 동안 메시를 삼각 측정 합니다. 이 숫자는 [표준 렌더링 서버 크기](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons)의 polygon 한도에 기여 합니다.
* `numVertices`: 전체 모델의 총 꼭 짓 점 수입니다.
* `numMaterial`: 전체 모델에 있는 총 자료 수입니다.
* `numFacesSmallestMesh`: 모델의 가장 작은 메쉬의 삼각형의 수입니다.
* `numFacesBiggestMesh`: 모델의 가장 큰 메시에 있는 삼각형의 수입니다.
* `numNodes`: 모델 장면 그래프의 노드 수입니다.
* `numMeshUsagesInScene`: 노드가 메시를 참조 하는 횟수입니다. 둘 이상의 노드가 동일한 메시를 참조할 수 있습니다.
* `maxNodeDepth`: 장면 그래프 내 노드의 최대 깊이입니다.

### <a name="the-outputinfo-section"></a>*Outputinfo* 섹션

이 섹션에서는 생성 된 출력에 대 한 일반 정보를 기록 합니다.

* `conversionToolVersion`: 모델 변환기의 버전입니다.
* `conversionHash`: 렌더링에 기여할 수 있는 arrAsset 내의 데이터에 대 한 해시입니다. 를 사용 하 여 동일한 파일에 대해 변환 서비스를 다시 실행할 때 다른 결과가 생성 되었는지 여부를 파악할 수 있습니다.

### <a name="the-outputstatistics-section"></a>*Outputstatistics* 섹션

이 섹션에서는 변환 된 자산에서 계산 된 정보를 기록 합니다.

* `numMeshPartsCreated`: ArrAsset의 메시 수입니다. `numMeshes` `inputStatistics` 인스턴스는 변환 프로세스의 영향을 받기 때문에 섹션과 다를 수 있습니다.
* `numMeshPartsInstanced`: ArrAsset에서 재사용 되는 메시의 수입니다.
* `recenteringOffset`: `recenterToOrigin` [ConversionSettings](configure-model-conversion.md) 의 옵션을 사용 하는 경우이 값은 변환 된 모델을 원래 위치로 다시 이동 하는 변환입니다.
* `boundingBox`: 모델의 범위입니다.

## <a name="deprecated-features"></a>사용되지 않는 기능

변환 서비스는 파일 및를 `stdout.txt` `stderr.txt` 출력 컨테이너에 기록 하며이는 경고 및 오류의 유일한 소스입니다.
이러한 파일은 이제 사용 되지 않습니다. 대신이 목적을 위해 [결과 파일](#information-about-a-conversion-the-result-file) 을 사용 하세요.

## <a name="next-steps"></a>다음 단계

* [모델 변환](model-conversion.md)
* [모델 변환 구성](configure-model-conversion.md)
