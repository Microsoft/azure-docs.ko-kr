---
title: R을 사용하여 실험 확장
titleSuffix: ML Studio (classic) - Azure
description: R 스크립트 실행 모듈을 사용 하 여 R 언어를 통해 Azure Machine Learning Studio (클래식)의 기능을 확장 하는 방법입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: d6174459237399ea370ee9c36ba537391104a917
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168568"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (클래식): R을 사용 하 여 실험 확장 
R [스크립트 실행][execute-r-script] 모듈을 사용 하 여 r 언어를 통해 Azure Machine Learning Studio (클래식)의 기능을 확장할 수 있습니다.

이 모듈에서는 여러 입력 데이터 세트를 허용하고 출력으로 단일 데이터 세트를 생성합니다. R 스크립트 [실행][execute-r-script] 모듈의 **r** 스크립트 매개 변수에 r 스크립트를 입력할 수 있습니다.

다음과 비슷한 코드를 사용하여 모듈의 각 입력 포트에 액세스합니다.

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>현재 설치된 모든 패키지 나열
설치된 패키지 목록을 변경할 수 있습니다. 현재 설치 된 패키지 목록은 [Azure Machine Learning Studio (클래식)에서 지 원하는 R 패키지](https://msdn.microsoft.com/library/azure/mt741980.aspx)에서 찾을 수 있습니다.

[R 스크립트 실행][execute-r-script] 모듈에 다음 코드를 입력 하 여 설치 된 패키지의 완전 한 현재 목록을 가져올 수도 있습니다.

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

[R 스크립트 실행][execute-r-script] 모듈의 출력 포트에 패키지 목록을 보냅니다.
패키지 목록을 보려면 [CSV로 변환][convert-to-csv] 과 같은 변환 모듈을 [R 스크립트 실행][execute-r-script] 모듈의 왼쪽 출력에 연결 하 고 실험을 실행 한 다음 변환 모듈의 출력을 클릭 하 고 **다운로드**를 선택 합니다. 

!["CSV로 변환" 모듈의 출력 다운로드](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>패키지 가져오기
[R 스크립트 실행][execute-r-script] 모듈에서 다음 명령을 사용 하 여 아직 설치 되지 않은 패키지를 가져올 수 있습니다.

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

여기에서 `my_favorite_package.zip` 파일은 패키지를 포함합니다.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
