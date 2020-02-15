---
title: 'CSV로 변환: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 CSV로 변환 모듈을 사용 하 여 R 또는 Python 스크립트 모듈에서 다운로드, 내보내기 또는 공유할 수 있는 CSV 형식으로 데이터 집합을 변환 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 8206a88695c89d04eabe89e79a5aff8469cc6862
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152434"
---
# <a name="convert-to-csv-module"></a>CSV 모듈로 변환

이 문서에서는 Azure Machine Learning designer (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 R 또는 Python 스크립트 모듈에서 다운로드, 내보내기 또는 공유할 수 있는 CSV 형식으로 데이터 집합을 변환할 수 있습니다.

### <a name="more-about-the-csv-format"></a>CSV 형식에 대 한 자세한 정보 

"쉼표로 구분 된 값"을 의미 하는 CSV 형식은 여러 외부 기계 학습 도구에서 사용 하는 파일 형식입니다. CSV는 R 또는 Python과 같은 오픈 소스 언어로 작업할 때 일반적인 교환 형식입니다.

Azure Machine Learning에서 대부분의 작업을 수행 하는 경우에도 외부 도구에서 사용 하기 위해 데이터 집합을 CSV로 변환 하는 것이 유용할 수 있습니다. 예를 들면 다음과 같습니다.

+ CSV 파일을 다운로드 하 여 Excel에서 열거나 관계형 데이터베이스로 가져옵니다.  
+ CSV 파일을 클라우드 저장소에 저장 하 고 Power BI에서 연결 하 여 시각화를 만듭니다.  
+ CSV 형식을 사용 하 여 R 및 Python에서 사용할 데이터를 준비 합니다. 

데이터 집합을 CSV로 변환 하면 csv가 Azure ML 작업 영역에 저장 됩니다. Azure storage 유틸리티를 사용 하 여 파일을 직접 열고 사용할 수 있습니다. **Csv로 변환** 모듈을 선택한 다음 오른쪽 패널의 **출력** 탭에서 히스토그램 아이콘을 선택 하 여 출력을 볼 수도 있습니다. 결과 폴더에서 로컬 디렉터리로 CSV를 다운로드할 수 있습니다.  

## <a name="how-to-configure-convert-to-csv"></a>CSV로 변환 구성 방법


1.  CSV로 변환 모듈을 파이프라인에 추가 합니다. 이 모듈은 디자이너의 **데이터 변환** 그룹에서 찾을 수 있습니다. 

2. 데이터 집합을 출력 하는 모듈에 연결 합니다.   
  
3.  파이프라인을 실행합니다.

### <a name="results"></a>결과
  

**CSV로 변환**의 오른쪽 패널에서 **출력** 탭을 선택 하 고 **포트 출력**아래에서 이러한 아이콘 중 하나를 선택 합니다.  

+ **데이터 집합 등록**: 아이콘을 선택 하 고 CSV 파일을 별도의 데이터 집합으로 Azure ML 작업 영역에 다시 저장 합니다. **내 데이터** 집합 섹션의 모듈 트리에서 데이터 집합을 모듈로 찾을 수 있습니다.

 + **출력 보기**: 눈동자 아이콘을 선택 하 고 지침에 따라 **Results_dataset** 폴더를 찾아보고 데이터 .csv 파일을 다운로드 합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 