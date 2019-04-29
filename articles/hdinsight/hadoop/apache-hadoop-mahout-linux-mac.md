---
title: Apache Mahout 및 HDInsight(SSH)를 사용하여 추천 생성 - Azure
description: Apache Mahout 기계 학습 라이브러리를 사용하여 HDInsight(Hadoop)에서 영화 추천을 생성하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: 63f1cfbf697f9cb1211e2c4671f64b19f933bc94
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129353"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-apache-hadoop-in-hdinsight-ssh"></a>HDInsight(SSH)의 Linux 기반 Apache Hadoop와 함께 Apache Mahout를 사용하여 영화 추천 생성

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Azure HDInsight에서 [Apache Mahout](https://mahout.apache.org) 기계 학습 라이브러리를 사용하여 영화 추천을 생성하는 방법에 대해 알아봅니다.

Mahout은 Apache Hadoop용 [Machine Learning][ml] 라이브러리입니다. Mahout에는 필터링, 분류 및 클러스터링과 같은 데이터 처리를 위한 알고리즘이 포함됩니다. 이 문서에서는 권장 엔진을 사용하여 친구가 본 영화를 기준으로 영화 권장을 생성합니다.

## <a name="prerequisites"></a>필수 조건

* Linux 기반 HDInsight 클러스터입니다. HDInsight 클러스터 만들기에 대한 자세한 내용은 [HDInsight에서 Linux 기반 Hadoop 사용 시작][getstarted]을 참조하세요.

> [!IMPORTANT]  
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](../hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* SSH 클라이언트. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md) 문서를 참조하세요.

## <a name="apache-mahout-versioning"></a>Apache Mahout 버전 관리

HDInsight 클러스터의 Mahout 버전에 대한 자세한 내용은 [HDInsight 버전 및 Apache Hadoop 구성 요소](../hdinsight-component-versioning.md)를 참조하세요.

## <a name="recommendations"></a>이해 권장 사항

Mahout에서 제공하는 기능 중 하나가 추천 엔진입니다. 이 엔진은 `userID`, `itemId` 및 `prefValue`(항목에 대한 선호도) 형식의 데이터를 허용합니다. 그런 다음 Mahout에서 동시 발생 분석을 수행하여 *특정 항목에 대한 선호도를 가진 사용자가 다른 항목에 대한 선호도도 갖고 있는지*확인할 수 있습니다. Mahout은 좋아하는 항목 선호도를 가진 사용자를 확인하며, 이 선호도는 추천하는 데 사용할 수 있습니다.

다음 워크플로는 영화 데이터를 사용하는 단순한 예제입니다.

* **동시 발생**: Joe, Alice 및 Bob은 모두 *스타워즈*, *제국의 역습* 및 *제다이의 귀환*을 좋아합니다. Mahout은 이러한 영화 중 하나를 좋아하면서 다른 두 개도 좋아하는 사용자를 확인합니다.

* **동시 발생**: Bob 및 Alice는 *보이지 않는 위협*, *클론의 습격* 및 *시스의 복수*도 좋아합니다. Mahout은 이전의 3개 영화를 좋아했던 사용자가 이 3개 영화도 좋아하는지 확인합니다.

* **유사성 추천**: Joe가 첫 3개 영화를 좋아하므로, Mahout은 유사한 선호도를 가진 다른 사람이 좋아하지만 Joe는 본(좋아하거나 평가한) 적이 없는 영화를 검색합니다. 이 경우 Mahout은 *보이지 않는 위협*, *클론의 습격* 및 *시스의 복수*를 추천합니다.

### <a name="understanding-the-data"></a>데이터 이해

편의를 위해 [GroupLens Research][movielens]에서 Mahout과 호환되는 형식으로 영화에 대한 평가 데이터를 제공합니다. 이 데이터는 클러스터의 기본 저장소( `/HdiSamples/HdiSamples/MahoutMovieData`)에서 사용할 수 있습니다.

`moviedb.txt` 및 `user-ratings.txt` 등 두 가지 파일이 있습니다. `user-ratings.txt` 파일은 분석 중 사용됩니다. `moviedb.txt`은 결과를 볼 때 사용자 친화적인 텍스트 정보를 제공하기 위해 사용됩니다.

user-ratings.txt에 포함된 데이터의 구조는 `userID`, `movieID`, `userRating` 및 `timestamp`이며, 각 사용자의 영화 등급 평가를 나타냅니다. 다음은 데이터의 예제입니다.

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>분석 실행

클러스터에 대한 SSH 연결에서 다음 명령을 사용하여 권장 작업을 실행합니다.

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]  
> 작업을 완료하려면 몇 분정도 걸릴 수 있으며 여러 MapReduce 작업을 실행할 수 있습니다.

## <a name="view-the-output"></a>출력 보기

1. 작업이 완료되면 다음 명령을 사용하여 생성된 결과를 봅니다.

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    출력은 다음과 같이 나타납니다.

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    첫 번째 열은 `userID`입니다. '[' 및 ']'에 포함된 값은 `movieId`:`recommendationScore`입니다.

2. moviedb.txt와 함께 출력을 사용하여 권장 사항에 대한 자세한 내용을 제공할 수 있습니다. 먼저, 다음 명령을 사용하여 파일을 로컬로 복사합니다.

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    이 명령은 영화 데이터 파일과 함께 현재 디렉터리에 있는 **recommendations.txt**라는 파일에 출력 데이터를 복사합니다.

3. 다음 명령을 사용하여 권장 사항 출력에서 데이터에 대한 영화 이름을 찾는 Python 스크립트를 만듭니다.

    ```bash
    nano show_recommendations.py
    ```

    편집기가 열리면 파일의 내용으로 다음 텍스트를 사용합니다.

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    **Ctrl-X**, **Y**, **Enter** 키를 차례로 눌러 데이터를 저장합니다.

4. Python 스크립트를 실행합니다. 다음 명령에서는 모든 파일이 다운로드된 디렉터리에 있다고 가정합니다.

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    이 명령은 사용자 ID 4에 대해 생성된 권장을 조사합니다.

   * **user-ratings.txt** 파일은 등급이 평가된 영화를 검색하는 데 사용됩니다.

   * **moviedb.txt** 파일은 영화의 이름을 검색하는 데 사용됩니다.

   * **recommendations.txt** 파일은 이 사용자의 영화 권장을 검색하는데 사용됩니다.

     이 명령의 출력은 다음 텍스트와 비슷합니다.

       Seven Years in Tibet (1997), score=5.0   Indiana Jones and the Last Crusade (1989), score=5.0   Jaws (1975), score=5.0   Sense and Sensibility (1995), score=5.0   Independence Day (ID4) (1996), score=5.0   My Best Friend's Wedding (1997), score=5.0   Jerry Maguire (1996), score=5.0   Scream 2 (1997), score=5.0   Time to Kill, A (1996), score=5.0

## <a name="delete-temporary-data"></a>임시 데이터 삭제

Mahout 작업은 작업을 처리하는 동안 생성된 임시 데이터를 제거하지 않습니다. 이 `--tempDir` 매개 변수는 쉽게 삭제할 수 있도록 임시 파일을 특정 경로로 분리하기 위해 예제 작업에서 지정되었습니다. 임시 파일을 제거하려면 다음 명령을 사용합니다.

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> 명령을 다시 실행하려는 경우 출력 디렉터리도 삭제해야 합니다. 이 디렉터리를 삭제하려면 다음을 사용합니다.
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>다음 단계

이제 Mahout을 사용하는 방법을 배웠으므로 HDInsight에서 데이터로 작업하는 다른 방법을 검색합니다.

* [HDInsight의 Apache Hive](hdinsight-use-hive.md)
* [HDInsight의 Apache Pig](hdinsight-use-pig.md)
* [HDInsight에서 MapReduce 사용](hdinsight-use-mapreduce.md)

[build]: https://mahout.apache.org/developers/buildingmahout.html
[movielens]: https://grouplens.org/datasets/movielens/
[100k]: https://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: https://en.wikipedia.org/wiki/Machine_learning
[forest]: https://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
