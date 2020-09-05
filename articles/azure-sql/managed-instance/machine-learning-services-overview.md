---
title: Azure SQL Managed Instance (미리 보기)의 Machine Learning Services
description: 이 문서에서는 Azure SQL Managed Instance에서 개요 또는 Machine Learning Services을 제공 합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 06/03/2020
ms.openlocfilehash: ad25a9a62757e1e031ce8b93a44e6f2ada4d9964
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689510"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Azure SQL Managed Instance (미리 보기)의 Machine Learning Services

Machine Learning Services은 Python 및 R 스크립트를 모두 지 원하는 데이터베이스 내 기계 학습 기능을 제공 하는 Azure SQL Managed Instance (미리 보기)의 기능입니다. 이 기능에는 고성능 예측 분석 및 기계 학습을 위한 Microsoft Python 및 R 패키지가 포함 됩니다. 관계형 데이터는 저장 프로시저, Python 또는 R 문을 포함 하는 T-sql 스크립트, T-sql을 포함 하는 Python 또는 R 코드를 통해 스크립트에서 사용할 수 있습니다.

> [!IMPORTANT]
> Machine Learning Services는 현재 공개 미리 보기로 제공되는 Azure SQL Managed Instance의 기능입니다.
> 이 미리 보기 기능은 미국, 아시아 유럽 및 오스트레일리아에서 제한 된 수의 지역에서 나중에 추가 되는 추가 지역을 제공 합니다.
>
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> 아래에서 [미리 보기에 등록](#signup)하세요.

## <a name="what-is-machine-learning-services"></a>Machine Learning Services란?

Azure SQL Managed Instance에서 Machine Learning Services를 사용 하 여 데이터베이스 내에서 Python 및 R 스크립트를 실행할 수 있습니다. 이를 사용하여 데이터를 준비 및 정리하고, 기능 엔지니어링을 수행하고, 데이터베이스 내에서 기계 학습 모델을 학습, 평가 및 배포할 수 있습니다. 이 기능은 데이터가 상주하는 스크립트를 실행하고 네트워크를 통해 다른 서버에 데이터를 전송하는 작업을 제거합니다.

Azure SQL Managed Instance에서 R/Python 지원과 함께 Machine Learning Services를 사용 하 여 다음을 수행 합니다.

- **R 및 python 스크립트를 실행 하 여 데이터 준비 및 범용 데이터 처리를 수행** 합니다. 이제 r 및 python 스크립트를 실행 하기 위해 데이터를 다른 서버로 이동 하는 대신 데이터를 저장 하는 Azure SQL Managed Instance로 r/python 스크립트를 가져올 수 있습니다. 데이터 이동 및 대기 시간, 보안 및 규정 준수와 관련 된 문제에 대 한 필요성을 없앨 수 있습니다.

- **데이터베이스에서 기계 학습 모델 학습** -모든 오픈 소스 알고리즘을 사용 하 여 모델을 학습 시킬 수 있습니다. 데이터베이스에서 가져온 샘플 데이터 집합에 의존 하지 않고 전체 데이터 집합에 대 한 학습을 쉽게 확장할 수 있습니다.

- **저장 프로시저에서 프로덕션에 모델 및 스크립트 배포** -스크립트 및 학습 된 모델은 단순히 t-sql 저장 프로시저에 포함 하 여 조작 가능한 수 있습니다. Azure SQL Managed Instance에 연결 하는 앱은 저장 프로시저를 호출 하 여 이러한 모델에서 예측 및 인텔리전스의 이점을 누릴 수 있습니다. 또한 네이티브 T-sql PREDICT 함수를 사용 하 여 매우 동시 실시간 점수 매기기 시나리오에서 빠른 점수 매기기를 위한 모델을 운영 수 있습니다.

Python 및 R의 기본 배포판은 Machine Learning Services에 포함되어 있습니다. Microsoft 패키지 [revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-revoscalepy) 및 [microsoftml](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-microsoftml)(Python용), [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler), [MicrosoftML](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml), [olapR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-olapr) 및 [sqlrutils](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-sqlrutils)(R용) 외에 PyTorch, TensorFlow 및 scikit-learn과 같은 오픈 소스 패키지 및 프레임워크를 설치하고 사용할 수 있습니다.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>미리 보기 등록

이 제한 된 공개 미리 보기는 [Azure 미리 보기 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)의 적용을 받습니다. 

미리 보기 프로그램에 참여 하 고 이러한 조건에 동의 하는 경우에서 Azure 지원 티켓을 만들어 등록을 요청할 수 있습니다 [**https://azure.microsoft.com/support/create-ticket/**](https://azure.microsoft.com/support/create-ticket/) . 

1. 다음 옵션을 선택합니다.
   - 문제 유형- **기술**
   - 구독- *구독 선택*
   - 서비스- **SQL Database Managed Instance**
   - 요약-요청에 대 한 *간단한 설명을 입력 합니다* .
   - 문제 유형- **SQL Managed Instance에 대 한 Machine Learning Services (미리 보기)**
   - 문제 하위 유형- **기타 문제 또는 "방법" 질문**

1. **다음: 솔루션**을 클릭 합니다.

1. 미리 보기에 대 한 정보를 읽고 **자세히**를 클릭 합니다.

1. 설명에 미리 보기에 등록할 논리 서버 이름, 지역 및 구독 ID를 포함 하 여 요청에 대 한 세부 **정보**를 입력 합니다. 다른 세부 정보를 적절 하 게 입력 합니다.

1. 작업이 완료 되 면 **다음: 검토 + 만들기**를 클릭 한 다음 **만들기**를 클릭 합니다.

프로그램에 등록되면 Microsoft에서 사용자가 공개 미리 보기에 온보딩되고 기존 또는 새 데이터베이스에 대해 Machine Learning Services를 사용하도록 설정됩니다.

공개 미리 보기 동안에는 프로덕션 워크로드에 SQL Managed Instance의 Machine Learning Services를 사용하지 않는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- [SQL Server Machine Learning Services의 주요 차이점](machine-learning-services-differences.md)을 참조 하세요.
- Machine Learning Services에서 Python을 사용 하는 방법에 대 한 자세한 내용은 [python 스크립트 실행](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)을 참조 하세요.
- Machine Learning Services에서 R을 사용 하는 방법에 대 한 자세한 내용은 [r 스크립트 실행](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)을 참조 하세요.
- 다른 SQL 플랫폼의 기계 학습에 대 한 자세한 내용은 [sql machine learning 설명서](https://docs.microsoft.com/sql/machine-learning/)를 참조 하세요.
