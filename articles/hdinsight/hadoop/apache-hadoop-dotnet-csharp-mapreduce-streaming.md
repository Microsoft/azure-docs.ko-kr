---
title: HDInsight의 Hadoop에서 MapReduce와 함께 C# 사용 - Azure
description: Azure HDInsight에서 Apache Hadoop으로 C#을 사용하여 MapReduce 솔루션을 만드는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: b06f19736c4d50ab7d246a5c71da04ada95b6f98
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122050"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>HDInsight의 Apache Hadoop에서 MapReduce 스트리밍으로 C# 사용

HDInsight에서 C#을 사용하여 MapReduce 솔루션을 만드는 방법에 대해 알아보세요.

> [!IMPORTANT]
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 구성 요소 버전 관리](../hdinsight-component-versioning.md)를 참조하세요.

Apache Hadoop 스트리밍은 스크립트 또는 실행 파일을 사용하여 MapReduce 작업을 실행할 수 있는 유틸리티입니다. 이 예제에서는 단어 수 세기 솔루션을 위해 .NET을 사용하여 매퍼와 리듀서를 구현합니다.

## <a name="net-on-hdinsight"></a>HDInsight에서.NET

__Linux 기반 HDInsight__ 클러스터는 [Mono(https://mono-project.com)](https://mono-project.com)를 사용하여 .NET 애플리케이션을 실행합니다. Mono 버전 4.2.1은 HDInsight 버전 3.6에 포함되어 있습니다. HDInsight와 함께 제공되는 Mono 버전에 대한 자세한 내용은 [HDInsight 구성 요소 버전](../hdinsight-component-versioning.md)을 참조하세요. 

.NET 프레임워크 버전과 Mono의 호환성에 대한 자세한 내용은 [Mono 호환성](https://www.mono-project.com/docs/about-mono/compatibility/)을 참조하세요.

## <a name="how-hadoop-streaming-works"></a>Hadoop 스트리밍 작동 방식

이 문서의 스트리밍에 사용된 기본 프로세스는 다음과 같습니다.

1. Hadoop이 STDIN의 매퍼(이 예제의 경우 mapper.exe)로 데이터를 전달합니다.
2. 매퍼가 데이터를 처리하고 탭으로 구분된 키/값 쌍을 STDOUT으로 내보냅니다.
3. Hadoop에서 이 출력을 읽습니다. 그런 다음 STDIN의 리듀서(이 예제의 경우 reducer.exe)로 전달됩니다.
4. 리듀서는 탭으로 구분된 키/값 쌍을 읽고 데이터를 처리한 다음 STDOUT에서 탭으로 구분된 키/값 쌍의 결과를 내보냅니다.
5. Hadoop에서 이 출력을 읽습니다. 그런 다음 출력 디렉터리에 기록됩니다.

스트리밍에 대한 자세한 내용은 [Hadoop 스트리밍](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* .NET Framework 4.5를 대상으로 하는 C# 코드 작성 및 빌드에 대해 잘 알고 있어야 합니다. 이 문서의 단계는 Visual Studio 2017을 사용합니다.

* 클러스터로 .exe 파일을 업로드하는 방법. 이 문서의 단계는 Data Lake Tools for Visual Studio를 사용하여 클러스터의 기본 저장소로 파일을 업로드합니다.

* Azure PowerShell 또는 SSH 클라이언트.

* HDInsight 클러스터의 Hadoop. 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

## <a name="create-the-mapper"></a>매퍼 만들기

Visual Studio에서 __mapper__라는 새 __콘솔 애플리케이션__을 만듭니다. 애플리케이션에 대해 다음 코드를 사용합니다.

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

애플리케이션을 만든 후 빌드하여 프로젝트 디렉터리에 `/bin/Debug/mapper.exe` 파일을 생성합니다.

## <a name="create-the-reducer"></a>리듀서 만들기

Visual Studio에서 __reducer__라는 새 __콘솔 애플리케이션__을 만듭니다. 애플리케이션에 대해 다음 코드를 사용합니다.

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

애플리케이션을 만든 후 빌드하여 프로젝트 디렉터리에 `/bin/Debug/reducer.exe` 파일을 생성합니다.

## <a name="upload-to-storage"></a>저장소에 업로드

1. Visual Studio에서 **서버 탐색기**를 엽니다.

2. **Azure**를 확장한 다음 **HDInsight**를 확장합니다.

3. 메시지가 표시되면 Azure 구독 자격 증명을 입력한 다음 **로그인**을 클릭합니다.

4. 이 애플리케이션을 배포하려는 HDInsight 클러스터를 확장합니다. 텍스트가 포함된 항목 __(기본 Storage 계정)__ 이 목록에 표시됩니다.

    ![클러스터에 대한 저장소 계정을 보여주는 서버 탐색기](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * 이 항목을 확장할 수 있는 경우 클러스터의 기본 스토리지로 __Azure Storage 계정__을 사용하고 있음을 의미합니다. 클러스터의 기본 저장소에서 파일을 보려면 항목을 확장한 다음 __(기본 컨테이너)__ 를 두 번 클릭합니다.

    * 이 항목을 확장할 수 없는 경우 클러스터의 기본 스토리지로 __Azure Data Lake Storage__를 사용하고 있음을 의미합니다. 클러스터의 기본 스토리지에 있는 파일을 보려면 항목을 확장한 다음 __(기본 Storage 계정)__ 을 두 번 클릭합니다.

5. .exe 파일을 업로드하려면 다음 방법 중 하나를 사용합니다.

   * __Azure Storage 계정__을 사용하는 경우 업로드 아이콘을 클릭한 다음 **bin\debug** 폴더로 이동하여 **mapper** 프로젝트를 검색합니다. 마지막으로 **mapper.exe** 파일을 선택한 다음 **확인**을 클릭합니다.

       ![업로드 아이콘](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
   * __Azure Data Lake Storage__를 사용하는 경우 마우스 오른쪽 버튼으로 파일 목록의 빈 영역을 클릭한 다음, __업로드__를 선택합니다. 마지막으로 **mapper.exe** 파일을 선택한 다음 **열기**를 클릭합니다.

     __mapper.exe__ 업로드가 완료되면 __reducer.exe__ 파일의 업로드 프로세스를 반복합니다.

## <a name="run-a-job-using-an-ssh-session"></a>작업 실행: SSH 세션 사용

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. 다음 명령 중 하나를 사용하여 MapReduce 작업을 시작합니다.

   * 기본 스토리지로 __Data Lake Storage Gen2__를 사용하는 경우:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files abfs:///mapper.exe,abfs:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

   * 기본 스토리지로 __Data Lake Storage Gen1__을 사용하는 경우:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```
    
   * 기본 스토리지로 __Azure Storage__를 사용하는 경우

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

     다음 목록은 각 매개 변수가 하는 기능에 대한 설명입니다.

   * `hadoop-streaming.jar`: 스트리밍 MapReduce 기능이 포함된 jar 파일입니다.
   * `-files`: 이 작업에 `mapper.exe` 및 `reducer.exe` 파일을 추가합니다. 각 파일 앞의 `abfs:///`, `adl:///` 및 `wasb:///`는 클러스터의 기본 스토리지 루트 경로입니다.
   * `-mapper`: mapper를 구현하는 파일을 지정합니다.
   * `-reducer`: reducer를 구현하는 파일을 지정합니다.
   * `-input`: 입력 데이터입니다.
   * `-output`: 출력 디렉터리입니다.

3. MapReduce 작업이 완료되면 다음을 사용하여 결과를 확인합니다.

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    다음 텍스트는 이 명령에서 반환된 데이터의 예입니다.

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>작업 실행: PowerShell 사용

다음 PowerShell 스크립트를 사용하여 MapReduce 작업을 실행하고 결과를 다운로드합니다.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

이 스크립트는 클러스터 로그인 계정 이름과 암호와 HDInsight 클러스터 이름을 묻습니다. 작업이 완료되면 출력이 `output.txt` 파일로 다운로드됩니다. 다음 텍스트는 `output.txt` 파일의 데이터 예제입니다.

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>다음 단계

HDInsight에서 MapReduce 사용에 대한 자세한 내용은 [HDInsight에서 MapReduce 사용](hdinsight-use-mapreduce.md)을 참조하세요.

Hive 및 Pig에서 C#을 사용하는 방법에 대한 자세한 내용은 [Apache Hive 및 Apache Pig에서 C# 사용자 정의 함수 사용](apache-hadoop-hive-pig-udf-dotnet-csharp.md)을 참조하세요.

HDInsight의 Storm에서 C# 사용에 대한 자세한 내용은 [HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](../storm/apache-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.
