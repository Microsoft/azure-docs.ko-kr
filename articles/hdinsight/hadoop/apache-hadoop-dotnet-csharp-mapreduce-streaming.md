---
title: HDInsight의 Hadoop에서 MapReduce와 함께 C# 사용 - Azure
description: Azure HDInsight에서 Apache Hadoop으로 C#을 사용하여 MapReduce 솔루션을 만드는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, seoapr2020, devx-track-csharp
ms.date: 04/28/2020
ms.openlocfilehash: 58f807a5a036f14af9baede604debe9d577cdbf6
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89000688"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>HDInsight의 Apache Hadoop에서 MapReduce 스트리밍으로 C# 사용

HDInsight에서 C#을 사용하여 MapReduce 솔루션을 만드는 방법에 대해 알아보세요.

Apache Hadoop 스트리밍을 사용 하면 스크립트나 실행 파일을 사용 하 여 MapReduce 작업을 실행할 수 있습니다. 여기서 .NET은 단어 개수 솔루션에 대 한 매퍼 및 리 듀 서을 구현 하는 데 사용 됩니다.

## <a name="net-on-hdinsight"></a>HDInsight에서.NET

HDInsight 클러스터는 [Mono https://mono-project.com) ](https://mono-project.com) 를 사용 하 여 .net 응용 프로그램을 실행 합니다. Mono 버전 4.2.1은 HDInsight 버전 3.6에 포함되어 있습니다. HDInsight에 포함 된 Mono 버전에 대 한 자세한 내용은 [다른 hdinsight 버전에서 사용할 수 있는 Apache Hadoop 구성 요소](../hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions)를 참조 하세요.

.NET 프레임워크 버전과 Mono의 호환성에 대한 자세한 내용은 [Mono 호환성](https://www.mono-project.com/docs/about-mono/compatibility/)을 참조하세요.

## <a name="how-hadoop-streaming-works"></a>Hadoop 스트리밍 작동 방식

이 문서의 스트리밍에 사용된 기본 프로세스는 다음과 같습니다.

1. Hadoop은 STDIN의 매퍼 (이 예제에서는*mapper.exe* )에 데이터를 전달 합니다.
2. 매퍼가 데이터를 처리하고 탭으로 구분된 키/값 쌍을 STDOUT으로 내보냅니다.
3. Hadoop에서 출력을 읽은 다음 STDIN의 리 듀 서 (이 예제에서는*reducer.exe* )에 전달 합니다.
4. 리듀서는 탭으로 구분된 키/값 쌍을 읽고 데이터를 처리한 다음 STDOUT에서 탭으로 구분된 키/값 쌍의 결과를 내보냅니다.
5. Hadoop에서 이 출력을 읽습니다. 그런 다음 출력 디렉터리에 기록됩니다.

스트리밍에 대한 자세한 내용은 [Hadoop 스트리밍](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Visual Studio.

* .NET Framework 4.5를 대상으로 하는 C# 코드 작성 및 빌드에 대해 잘 알고 있어야 합니다.

* 클러스터로 .exe 파일을 업로드하는 방법. 이 문서의 단계는 Data Lake Tools for Visual Studio를 사용하여 클러스터의 기본 스토리지로 파일을 업로드합니다.

* PowerShell을 사용하는 경우 [Az Module](https://docs.microsoft.com/powershell/azure/)이 필요합니다.

* HDInsight의 Apache Hadoop 클러스터. [Linux에서 HDInsight 시작](../hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.

* 클러스터 기본 스토리지에 대한 URI 체계입니다. 이 체계는 `wasb://` Azure Storage `abfs://` Azure Data Lake Storage Gen2 또는 Azure Data Lake Storage Gen1에 대 한 것입니다 `adl://` . Azure Storage 또는 Data Lake Storage Gen2에 대해 보안 전송이 사용 되는 경우 URI는 `wasbs://` 각각 또는 `abfss://` 입니다.

## <a name="create-the-mapper"></a>매퍼 만들기

Visual Studio에서 *매퍼*라는 새 .NET Framework 콘솔 응용 프로그램을 만듭니다. 애플리케이션에 대해 다음 코드를 사용합니다.

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

응용 프로그램을 만든 후 빌드하여 프로젝트 디렉터리에 */bin/Debug/mapper.exe* 파일을 생성 합니다.

## <a name="create-the-reducer"></a>리듀서 만들기

Visual Studio에서 *리 듀 서*이라는 새 .NET Framework 콘솔 응용 프로그램을 만듭니다. 애플리케이션에 대해 다음 코드를 사용합니다.

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

응용 프로그램을 만든 후 빌드하여 프로젝트 디렉터리에 */bin/Debug/reducer.exe* 파일을 생성 합니다.

## <a name="upload-to-storage"></a>스토리지에 업로드

다음으로, *매퍼* 및 *리 듀 서* 응용 프로그램을 HDInsight 저장소에 업로드 해야 합니다.

1. Visual Studio에서 **뷰**  >  **서버 탐색기**를 선택 합니다.

1. **Azure**를 마우스 오른쪽 단추로 클릭 하 고 **Microsoft Azure 구독에 연결**...을 선택 하 여 로그인 프로세스를 완료 합니다.

1. 이 애플리케이션을 배포하려는 HDInsight 클러스터를 확장합니다. 텍스트가 포함된 항목 **(기본 Storage 계정)** 이 목록에 표시됩니다.

   ![Storage 계정, HDInsight 클러스터, 서버 탐색기, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * **(기본 저장소 계정)** 항목을 확장할 수 있는 경우 **Azure Storage 계정을** 클러스터의 기본 저장소로 사용 하 고 있습니다. 클러스터의 기본 저장소에 있는 파일을 보려면 해당 항목을 확장 한 다음 **(기본 컨테이너)** 를 두 번 클릭 합니다.

   * **(기본 저장소 계정)** 항목을 확장할 수 없는 경우 클러스터의 기본 저장소로 **Azure Data Lake Storage** 를 사용 하 게 됩니다. 클러스터의 기본 스토리지에 있는 파일을 보려면 항목을 확장한 다음 **(기본 Storage 계정)** 을 두 번 클릭합니다.

1. .exe 파일을 업로드하려면 다음 방법 중 하나를 사용합니다.

    * **Azure Storage 계정을**사용 하는 경우 **Blob 업로드** 아이콘을 선택 합니다.

        ![맵 편집기에 대 한 HDInsight 업로드 아이콘, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        **새 파일 업로드** 대화 상자에서 **파일 이름**아래에 있는 **찾아보기**를 선택 합니다. **Blob 업로드** 대화 상자에서 *매퍼* 프로젝트의 *bin\debug* 폴더로 이동한 다음 *mapper.exe* 파일을 선택 합니다. 마지막으로 **열기** 를 선택 하 고 **확인** 을 선택 하 여 업로드를 완료 합니다.

    * **Azure Data Lake Storage**의 경우 파일 목록에서 빈 영역을 마우스 오른쪽 단추로 클릭 한 다음 **업로드**를 선택 합니다. 마지막으로 *mapper.exe* 파일을 선택한 다음 **열기**를 선택 합니다.

    *mapper.exe* 업로드가 완료되면 *reducer.exe* 파일의 업로드 프로세스를 반복합니다.

## <a name="run-a-job-using-an-ssh-session"></a>작업 실행: SSH 세션 사용

다음 절차에서는 SSH 세션을 사용 하 여 MapReduce 작업을 실행 하는 방법을 설명 합니다.

1. [ssh command](../hdinsight-hadoop-linux-use-ssh-unix.md) 명령을 사용하여 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿔서 아래 명령을 편집하고, 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 다음 명령 중 하나를 사용하여 MapReduce 작업을 시작합니다.

   * 기본 저장소가 **Azure Storage**경우:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * 기본 저장소가 **Data Lake Storage Gen1**경우:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * 기본 저장소가 **Data Lake Storage Gen2**경우:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   다음 목록에서는 각 매개 변수 및 옵션이 나타내는 내용에 대해 설명 합니다.

   |매개 변수 | 설명 |
   |---|---|
   |hadoop-streaming.jar|스트리밍 MapReduce 기능을 포함 하는 jar 파일을 지정 합니다.|
   |-파일|이 작업에 대 한 *mapper.exe* 및 *reducer.exe* 파일을 지정 합니다. `wasbs:///` `adl:///` `abfs:///` 각 파일 앞의, 또는 프로토콜 선언은 클러스터의 기본 저장소 루트에 대 한 경로입니다.|
   |-매퍼|매퍼를 구현 하는 파일을 지정 합니다.|
   |-리 듀 서|리 듀 서을 구현 하는 파일을 지정 합니다.|
   |-입력|입력 데이터를 지정 합니다.|
   |-출력|출력 디렉터리를 지정 합니다.|

1. MapReduce 작업이 완료 되 면 다음 명령을 사용 하 여 결과를 확인 합니다.

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   다음 텍스트는 이 명령에서 반환된 데이터의 예입니다.

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>작업 실행: PowerShell 사용

다음 PowerShell 스크립트를 사용하여 MapReduce 작업을 실행하고 결과를 다운로드합니다.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

이 스크립트는 클러스터 로그인 계정 이름과 암호와 HDInsight 클러스터 이름을 묻습니다. 작업이 완료 되 면 출력은 *output.txt*라는 파일로 다운로드 됩니다. 다음 텍스트는 `output.txt` 파일의 데이터 예제입니다.

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>다음 단계

* [HDInsight의 Apache Hadoop에서 MapReduce를 사용](hdinsight-use-mapreduce.md)합니다.
* [Apache Hive 및 Apache Pig와 함께 c # 사용자 정의 함수를 사용](apache-hadoop-hive-pig-udf-dotnet-csharp.md)합니다.
* [Java MapReduce 프로그램 개발](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
