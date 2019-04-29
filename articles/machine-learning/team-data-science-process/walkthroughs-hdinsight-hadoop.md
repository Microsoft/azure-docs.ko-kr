---
title: Hive를 사용하여 Azure HDInsight Hadoop에서 분석 - Team Data Science Process
description: 예측 분석을 수행하기 위해 Azure HDInsight Hadoop에서 Hive 사용을 보여 주는 Team Data Science Process의 예제입니다.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d3c3278a058162632a6ba7ea9731e5f233190700
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60804664"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Azure에서 Hive를 사용하여 HDInsight Hadoop 데이터 과학 연습 

이 연습에서는 HDInsight Hadoop 클러스터와 Hive를 사용하여 예측 분석을 수행합니다. Team Data Science Process에 설명된 단계를 따릅니다. Team Data Science Process의 개요는 [데이터 과학 프로세스](overview.md)를 참조하세요. Azure HDInsight에 대한 소개는 [Azure HDInsight, Hadoop 기술 스택 및 Hadoop 클러스터에 대한 소개](../../hdinsight/hadoop/apache-hadoop-introduction.md)를 참조하세요.

Team Data Science Process를 실행하는 추가 데이터 과학 연습은 사용하는 **플랫폼**에 따라 그룹화됩니다. 이러한 예제의 항목 목록은 [Team Data Science Process 실행 연습](walkthroughs.md)을 참조하세요.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>HDInsight Hadoop와 함께 Hive를 사용하여 택시 팁 예측

[HDInsight Hadoop 클러스터 사용](hive-walkthrough.md) 연습은 뉴욕 택시에서 데이터를 사용하여 다음을 예측합니다. 

- 팁 지불 여부 
- 팁 금액의 분포

시나리오는 [Azure HDInsight Hadoop 클러스터](https://azure.microsoft.com/services/hdinsight/)와 함께 Hive를 사용하여 구현됩니다. 공개적으로 사용할 수 있는 NYC Taxi Trip 및 요금 데이터 세트에서 데이터를 저장, 탐색 및 기능 엔지니어링하는 방법을 배웁니다. 또한 Azure Machine Learning을 사용하여 모델을 빌드 및 배포할 수 있습니다.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>HDInsight Hadoop와 함께 Hive를 사용하여 광고 클릭 예측

[1TB 데이터 세트에서 Azure HDInsight Hadoop 클러스터 사용](hive-criteo-walkthrough.md) 연습에서는 공개적으로 사용할 수 있는 [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) 클릭 데이터 세트를 사용하여 팁 지불 여부와 예상하는 금액 범위를 예측합니다. 시나리오는 샘플 데이터를 저장, 탐색, 기능 엔지니어링 및 다운하기 위해 [Azure HDInsight Hadoop 클러스터](https://azure.microsoft.com/services/hdinsight/)와 함께 Hive를 사용하여 구현됩니다. Azure Machine Learning을 사용하여 광고에 대한 사용자 클릭 여부를 예측하는 이진 분류 모델을 빌드, 학습 및 점수 매깁니다. 연습은 이러한 모델 중 하나를 웹 서비스로 게시하는 방법을 보여 주는 것으로 마칩니다.


## <a name="next-steps"></a>다음 단계

Team Data Science Process를 구성하는 주요 구성의 논의는 [Team Data Science Process 개요](overview.md)를 참조하세요.

데이터 과학 프로젝트를 구성하는 데 사용할 수 있는 Team Data Science Process 수명 주기의 논의는 [Team Data Science Process 수명 주기](lifecycle.md)를 참조하세요. 수명 주기는 일반적으로 프로젝트가 실행될 때 시작부터 끝까지 따라야 하는 단계를 간략하게 설명합니다. 

