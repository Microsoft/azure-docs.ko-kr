---
title: HDInsight의 Apache Hadoop에서 Apache Hive 및 Apache Pig와 함께 C# 사용 - Azure
description: Azure HDInsight에서 Apache Hive 및 Apache Pig 스트림과 함께 C# UDF(사용자 정의 함수)를 사용하는 방법을 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 31738c43756da14ba6c2c92afbcb2882561c8001
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121952"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-streaming-on-apache-hadoop-in-hdinsight"></a>HDInsight의 Apache Hadoop에서 Apache Hive 및 Apache Pig 스트림과 함께 C# 사용자 정의 함수 사용

HDInsight에서 Apache Hive 및 Apache Pig와 함께 C# UDF(사용자 정의 함수)를 사용하는 방법을 알아봅니다.

> [!IMPORTANT]
> 이 문서의 단계는 Linux 기반 및 Windows 기반 HDInsight 클러스터에 적용됩니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 구성 요소 버전 관리](../hdinsight-component-versioning.md)를 참조하세요.

Hive 및 Pig 모두 외부 애플리케이션으로 데이터를 전달해 처리할 수 있습니다. 이 프로세스를 _스트리밍_이라고 합니다. .NET 애플리케이션을 사용하는 경우 데이터가 STDIN의 애플리케이션으로 전달된 다음 애플리케이션이 STDOUT에서 결과를 반환합니다. STDIN 및 STDOUT에서 읽거나 쓰려면 콘솔 애플리케이션에서 `Console.ReadLine()` 및 `Console.WriteLine()`을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* .NET Framework 4.5를 대상으로 하는 C# 코드 작성 및 빌드에 대해 잘 알고 있어야 합니다.

    * 원하는 IDE를 사용합니다. [Visual Studio](https://www.visualstudio.com/vs) 2015, 2017 또는 [Visual Studio Code](https://code.visualstudio.com/)를 권장합니다. 이 문서의 단계는 Visual Studio 2017을 사용합니다.

* .exe 파일을 클러스터로 업로드하고 Pig 및 Hive 작업을 실행하는 방법. Data Lake Tools for Visual Studio, Azure PowerShell 및 Azure 클래식 CLI를 사용하는 것이 좋습니다. 이 문서의 단계는 Data Lake Tools for Visual Studio를 사용하여 파일을 업로드하고 예제 Hive 쿼리를 실행합니다.

    Hive 쿼리 및 Pig 작업을 실행하는 다른 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

    * [HDInsight에서 Apache Hive 사용](hdinsight-use-hive.md)

    * [HDInsight에서 Apache Pig 사용](hdinsight-use-pig.md)

* HDInsight 클러스터의 Hadoop. 클러스터를 만드는 방법에 대한 자세한 내용은 [HDInsight 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

## <a name="net-on-hdinsight"></a>HDInsight에서.NET

* __Linux 기반 HDInsight__ 클러스터는 [Mono(https://mono-project.com)](https://mono-project.com)를 사용하여 .NET 애플리케이션을 실행합니다. Mono 버전 4.2.1은 HDInsight 버전 3.6에 포함되어 있습니다.

    .NET 프레임워크 버전과 Mono의 호환성에 대한 자세한 내용은 [Mono 호환성](https://www.mono-project.com/docs/about-mono/compatibility/)을 참조하세요.

* __Windows 기반 HDInsight__ 클러스터는 Microsoft .NET CLR을 사용하여 .NET 애플리케이션을 실행합니다.

.NET Framework 버전 및 HDInsight 버전과 함께 제공되는 Mono에 대한 자세한 내용은 [HDInsight 구성 요소 버전](../hdinsight-component-versioning.md)을 참조하세요.

## <a name="create-the-c-projects"></a>C\# 프로젝트 만들기

### <a name="apache-hive-udf"></a>Apache Hive UDF

1. Visual Studio를 열고 솔루션을 만듭니다. 프로젝트 형식의 경우, **콘솔 앱(.NET Framework)** 을 선택하고 새 프로젝트의 이름을 **HiveCSharp**로 지정합니다.

    > [!IMPORTANT]
    > Linux 기반 HDInsight 클러스터를 사용하는 경우 __.NET Framework 4.5__를 선택합니다. .NET 프레임워크 버전과 Mono의 호환성에 대한 자세한 내용은 [Mono 호환성](https://www.mono-project.com/docs/about-mono/compatibility/)을 참조하세요.

2. **Program.cs**의 내용을 다음 코드로 바꿉니다.

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. 프로젝트를 빌드합니다.

### <a name="apache-pig-udf"></a>Apache Pig UDF

1. Visual Studio를 열고 솔루션을 만듭니다. 프로젝트 형식의 경우, **콘솔 애플리케이션**을 선택하고 새 프로젝트의 이름을 **PigUDF**로 지정합니다.

2. **Program.cs** 파일의 내용을 다음 코드로 바꿉니다.

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    이 코드는 Pig에서 보낸 줄을 구문 분석하고 `java.lang.Exception`로 시작하는 줄의 형식을 변경합니다.

3. **Program.cs**를 저장한 다음 프로젝트를 빌드합니다.

## <a name="upload-to-storage"></a>저장소에 업로드

1. Visual Studio에서 **서버 탐색기**를 엽니다.

2. **Azure**를 확장한 다음 **HDInsight**를 확장합니다.

3. 메시지가 표시되면 Azure 구독 자격 증명을 입력한 다음 **로그인**을 클릭합니다.

4. 이 애플리케이션을 배포하려는 HDInsight 클러스터를 확장합니다. 텍스트가 포함된 항목 __(기본 Storage 계정)__ 이 목록에 표시됩니다.

    ![클러스터에 대한 저장소 계정을 보여주는 서버 탐색기](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * 이 항목을 확장할 수 있는 경우 클러스터의 기본 스토리지로 __Azure Storage 계정__을 사용하고 있음을 의미합니다. 클러스터의 기본 저장소에서 파일을 보려면 항목을 확장한 다음 __(기본 컨테이너)__ 를 두 번 클릭합니다.

    * 이 항목을 확장할 수 없는 경우 클러스터의 기본 스토리지로 __Azure Data Lake Storage__를 사용하고 있음을 의미합니다. 클러스터의 기본 스토리지에 있는 파일을 보려면 항목을 확장한 다음 __(기본 Storage 계정)__ 을 두 번 클릭합니다.

6. .exe 파일을 업로드하려면 다음 방법 중 하나를 사용합니다.

   * __Azure Storage 계정__을 사용하는 경우 업로드 아이콘을 클릭한 다음 **bin\debug** 폴더로 이동하여 **HiveCSharp** 프로젝트를 검색합니다. 마지막으로 **HiveCSharp.exe** 파일을 선택하고 **확인**을 클릭합니다.

       ![업로드 아이콘](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
   * __Azure Data Lake Storage__를 사용하는 경우 마우스 오른쪽 버튼으로 파일 목록의 빈 영역을 클릭한 다음, __업로드__를 선택합니다. 마지막으로 **HiveCSharp.exe** 파일을 선택하고 **열기**를 클릭합니다.

     __HiveCSharp.exe__ 업로드가 완료되면 __PigUDF.exe__ 파일의 업로드 프로세스를 반복합니다.

## <a name="run-an-apache-hive-query"></a>Apache Hive 쿼리 실행

1. Visual Studio에서 **서버 탐색기**를 엽니다.

2. **Azure**를 확장한 다음 **HDInsight**를 확장합니다.

3. **HiveCSharp** 애플리케이션을 배포한 클러스터를 마우스 오른쪽 단추로 클릭하고 **Hive 쿼리 작성**을 선택합니다.

4. Hive 쿼리로 다음 텍스트를 사용합니다.

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > 클러스터에 사용되는 기본 저장소의 유형과 일치하는 `add file` 문에서 주석 처리를 제거합니다.

    이 쿼리는 `hivesampletable`에서 `clientid`, `devicemake` 및 `devicemodel` 필드를 선택하고 해당 필드를 HiveCSharp.exe 애플리케이션으로 전달합니다. 쿼리는 애플리케이션이 3개의 필드를 반환할 것을 예상하며 `clientid`, `phoneLabel` 및 `phoneHash`로 저장됩니다. 또한 쿼리는 기본 저장소 컨테이너의 루트에서 HiveCSharp.exe를 찾는다고 예상합니다.

5. **제출** 을 클릭하여 HDInsight 클러스터에 작업을 제출합니다. **Hive 작업 요약** 창이 열립니다.

6. **새로 고침**을 클릭하여 **작업 상태**가 **Completed**로 변경될 때까지 요약을 새로 고칩니다. 작업 출력을 보려면 **작업 출력**을 클릭합니다.

## <a name="run-an-apache-pig-job"></a>Apache Pig 작업 실행

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. 예: `ssh sshuser@mycluster-ssh.azurehdinsight.net`. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. Pig 명령줄을 시작하려면 다음 명령을 사용합니다.

        pig

    > [!IMPORTANT]
    > Windows 기반 클러스터를 사용하는 경우 대신 다음 명령을 사용합니다.
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    `grunt>` 프롬프트가 표시됩니다.

3. .NET Framework 애플리케이션을 사용하는 Pig 작업을 실행하려면 다음을 입력합니다.

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    `DEFINE` 문은 pigudf.exe 애플리케이션에 대한 `streamer`의 별칭을 만들고 `CACHE`는 클러스터의 기본 저장소에서 로드합니다. 나중에 `streamer`는 `STREAM` 연산자와 함께 사용되어 로그에 포함된 단일 줄을 처리하고 일련의 열로 데이터를 반환합니다.

    > [!NOTE]
    > 스트리밍에 사용되는 애플리케이션 이름은 별칭이 지정된 경우 \`(기호) 문자로 묶어야 하며 `SHIP`와 함께 사용된 경우 '(작은따옴표)로 묶어야 합니다.

4. 마지막 줄을 입력하면 작업이 시작됩니다. 다음 텍스트와 비슷한 출력이 반환됩니다.

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight의 Hive 및 Pig에서 .NET Framework 애플리케이션을 사용하는 방법에 대해 배웠습니다. Python을 Hive 및 Pig와 함께 사용하는 방법에 대해 알고 싶으면 [HDInsight에서 Apache Hive 및 Apache Pig와 함께 Python 사용](python-udf-hdinsight.md)을 참조하세요.

Pig 및 Hive를 사용하고 MapReduce 사용에 대해 배우는 다른 방법은 다음 문서를 참조하세요.

* [HDInsight에서 Apache Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Apache Pig 사용](hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)
