---
title: 'Apache Hadoop에서 c #, Apache Hive & Apache Pig-Azure HDInsight'
description: Azure HDInsight에서 Apache Hive 및 Apache Pig 스트림과 함께 C# UDF(사용자 정의 함수)를 사용하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/06/2019
ms.openlocfilehash: 5e32e269a36196d774bb1163a1c70c5d818ddcbb
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89000620"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>HDInsight에서 Apache Hadoop의 Apache Hive 및 Apache Pig에서 c # 사용자 정의 함수 사용

HDInsight의 [Apache Hive](https://hive.apache.org) 및 [Apache Pig](https://pig.apache.org) 와 함께 c # UDF (사용자 정의 함수)를 사용 하는 방법에 대해 알아봅니다.

> [!IMPORTANT]
> 이 문서의 단계는 Linux 기반 HDInsight 클러스터에서 작동 합니다. Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 구성 요소 버전 관리](../hdinsight-component-versioning.md)를 참조하세요.

Hive 및 Pig 모두 외부 애플리케이션으로 데이터를 전달해 처리할 수 있습니다. 이 프로세스를 _스트리밍_이라고 합니다. .NET 애플리케이션을 사용하는 경우 데이터가 STDIN의 애플리케이션으로 전달된 다음 애플리케이션이 STDOUT에서 결과를 반환합니다. STDIN 및 STDOUT에서 읽거나 쓰려면 콘솔 애플리케이션에서 `Console.ReadLine()` 및 `Console.WriteLine()`을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* .NET Framework 4.5를 대상으로 하는 C# 코드 작성 및 빌드에 대해 잘 알고 있어야 합니다.

    원하는 IDE를 사용합니다. [Visual Studio](https://www.visualstudio.com/vs) 또는 [Visual Studio Code](https://code.visualstudio.com/)을 권장 합니다. 이 문서의 단계는 Visual Studio 2019을 사용 합니다.

* .exe 파일을 클러스터로 업로드하고 Pig 및 Hive 작업을 실행하는 방법. Visual Studio, [Azure PowerShell](/powershell/azure)및 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) [에 대 한 Data Lake 도구](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md)를 권장 합니다. 이 문서의 단계는 Data Lake Tools for Visual Studio를 사용하여 파일을 업로드하고 예제 Hive 쿼리를 실행합니다.

    Hive 쿼리를 실행 하는 다른 방법에 대 한 자세한 내용은 [Azure HDInsight의 Apache Hive 및 HiveQL?](hdinsight-use-hive.md)을 참조 하세요.

* HDInsight 클러스터의 Hadoop. 클러스터를 만드는 방법에 대 한 자세한 내용은 [HDInsight 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조 하세요.

## <a name="net-on-hdinsight"></a>HDInsight에서.NET

*Linux 기반 HDInsight* 클러스터는 [Mono(https://mono-project.com)](https://mono-project.com)를 사용하여 .NET 애플리케이션을 실행합니다. Mono 버전 4.2.1은 HDInsight 버전 3.6에 포함되어 있습니다.

.NET 프레임워크 버전과 Mono의 호환성에 대한 자세한 내용은 [Mono 호환성](https://www.mono-project.com/docs/about-mono/compatibility/)을 참조하세요.

HDInsight 버전에 포함 된 .NET Framework 및 Mono 버전에 대 한 자세한 내용은 [hdinsight 구성 요소 버전](../hdinsight-component-versioning.md)을 참조 하세요.

## <a name="create-the-c-projects"></a>C\# 프로젝트 만들기

다음 섹션에서는 Apache Hive UDF 및 Apache Pig UDF에 대해 Visual Studio에서 c # 프로젝트를 만드는 방법을 설명 합니다.

### <a name="apache-hive-udf"></a>Apache Hive UDF

Apache Hive UDF에 대 한 c # 프로젝트를 만들려면:

1. Visual Studio를 실행합니다.

2. **새 프로젝트 만들기**를 선택합니다.

3. **새 프로젝트 만들기** 창에서 **콘솔 앱 (.NET Framework)** 템플릿 (c # 버전)을 선택 합니다. **다음**을 선택합니다.

4. **새 프로젝트 구성** 창에서 **프로젝트 이름** 으로 *hivecsharp.exe*를 입력 하 고 새 프로젝트를 저장할 **위치로** 이동 하거나 만듭니다. 그런 다음 **만들기**를 선택합니다.

5. Visual Studio IDE에서 *Program.cs* 의 내용을 다음 코드로 바꿉니다.

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

6. 메뉴 모음에서 빌드 **Build**  >  **솔루션** 빌드를 선택 하 여 프로젝트를 빌드합니다.

7. 솔루션을 닫습니다.

### <a name="apache-pig-udf"></a>Apache Pig UDF

Apache Hive UDF에 대 한 c # 프로젝트를 만들려면:

1. Visual Studio를 엽니다.

2. **시작** 창에서 **새 프로젝트 만들기**를 선택합니다.

3. **새 프로젝트 만들기** 창에서 **콘솔 앱 (.NET Framework)** 템플릿 (c # 버전)을 선택 합니다. **다음**을 선택합니다.

4. **새 프로젝트 구성** 창에서 **프로젝트 이름** 으로 *문은 pigudf.exe*를 입력 하 고로 이동 하거나 새 프로젝트를 저장할 **위치** 를 만듭니다. 그런 다음 **만들기**를 선택합니다.

5. Visual Studio IDE에서 *Program.cs* 의 내용을 다음 코드로 바꿉니다.

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

6. 메뉴 모음에서 빌드 **Build**  >  **솔루션** 빌드를 선택 하 여 프로젝트를 빌드합니다.

7. 솔루션을 열어 둡니다.

## <a name="upload-to-storage"></a>스토리지에 업로드

그런 다음 Hive 및 Pig UDF 응용 프로그램을 HDInsight 클러스터의 저장소에 업로드 합니다.

1. Visual Studio에서 **뷰**  >  **서버 탐색기**로 이동 합니다.

1. **서버 탐색기**에서 **Azure**를 마우스 오른쪽 단추로 클릭 하 **Microsoft Azure 구독에 연결**을 선택 하 고 로그인 프로세스를 완료 합니다.

1. 이 애플리케이션을 배포하려는 HDInsight 클러스터를 확장합니다. 텍스트가 포함된 항목 **(기본 Storage 계정)** 이 목록에 표시됩니다.

    ![기본 저장소 계정, HDInsight 클러스터 서버 탐색기](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * 이 항목을 확장할 수 있는 경우 **Azure Storage 계정을** 클러스터의 기본 저장소로 사용 하 고 있습니다. 클러스터의 기본 스토리지에서 파일을 보려면 항목을 확장한 다음 **(기본 컨테이너)** 를 두 번 클릭합니다.

    * 이 항목을 확장할 수 없는 경우 클러스터의 기본 저장소로 **Azure Data Lake Storage** 을 사용 하 고 있습니다. 클러스터의 기본 스토리지에 있는 파일을 보려면 항목을 확장한 다음 **(기본 Storage 계정)** 을 두 번 클릭합니다.

1. .exe 파일을 업로드하려면 다음 방법 중 하나를 사용합니다.

    * **Azure Storage 계정을**사용 하는 경우 **Blob 업로드** 아이콘을 선택 합니다.

        ![새 프로젝트에 대 한 HDInsight 업로드 아이콘](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        **새 파일 업로드** 대화 상자에서 **파일 이름**아래에 있는 **찾아보기**를 선택 합니다. **Blob 업로드** 대화 상자에서 *hivecsharp.exe* 프로젝트에 대 한 *bin\debug* 폴더로 이동한 다음 *HiveCSharp.exe* 파일을 선택 합니다. 마지막으로 **열기** 를 선택 하 고 **확인** 을 선택 하 여 업로드를 완료 합니다.

    * **Azure Data Lake Storage**를 사용 하는 경우 파일 목록에서 빈 영역을 마우스 오른쪽 단추로 클릭 한 다음 **업로드**를 선택 합니다. 마지막으로 *HiveCSharp.exe* 파일을 선택 하 고 **열기**를 선택 합니다.

    *HiveCSharp.exe* 업로드가 완료되면 *PigUDF.exe* 파일의 업로드 프로세스를 반복합니다.

## <a name="run-an-apache-hive-query"></a>Apache Hive 쿼리 실행

이제 Hive UDF 응용 프로그램을 사용 하는 Hive 쿼리를 실행할 수 있습니다.

1. Visual Studio에서 **뷰**  >  **서버 탐색기**로 이동 합니다.

2. **Azure**를 확장한 다음 **HDInsight**를 확장합니다.

3. *HiveCSharp* 애플리케이션을 배포한 클러스터를 마우스 오른쪽 단추로 클릭하고 **Hive 쿼리 작성**을 선택합니다.

4. Hive 쿼리로 다음 텍스트를 사용합니다.

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
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
    > 클러스터에 사용되는 기본 스토리지의 유형과 일치하는 `add file` 문에서 주석 처리를 제거합니다.

    이 쿼리는에서, 및 필드를 선택 하 `clientid` `devicemake` `devicemodel` `hivesampletable` 고 필드를 *HiveCSharp.exe* 응용 프로그램에 전달 합니다. 쿼리는 애플리케이션이 3개의 필드를 반환할 것을 예상하며 `clientid`, `phoneLabel` 및 `phoneHash`로 저장됩니다. 또한이 쿼리는 기본 저장소 컨테이너의 루트에 *HiveCSharp.exe* 를 찾으려고 합니다.

5. 기본 **Interactive** 를 **Batch**로 전환 하 고 **제출** 을 선택 하 여 HDInsight 클러스터에 작업을 제출 합니다. **Hive 작업 요약** 창이 열립니다.

6. **작업 상태가** **완료**로 변경 될 때까지 요약을 새로 고치려면 **새로 고침** 을 선택 합니다. 작업 출력을 보려면 **작업 출력**을 선택 합니다.

## <a name="run-an-apache-pig-job"></a>Apache Pig 작업 실행

Pig UDF 응용 프로그램을 사용 하는 Pig 작업을 실행할 수도 있습니다.

1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. 예를 들어 명령을 실행 `ssh sshuser@<clustername>-ssh.azurehdinsight.net` 합니다. 자세한 내용은 [USE SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)를 참조 하세요.

2. 다음 명령을 사용 하 여 Pig 명령줄을 시작 합니다.

    ```shell
    pig
    ```

    `grunt>` 프롬프트가 표시됩니다.

3. .NET Framework 애플리케이션을 사용하는 Pig 작업을 실행하려면 다음을 입력합니다.

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    `DEFINE`문은 `streamer` *PigUDF.exe* 응용 프로그램에 대 한 별칭을 만들어 클러스터의 `CACHE` 기본 저장소에서 로드 합니다. 나중에 `streamer` 연산자와 함께를 사용 `STREAM` 하 여에 포함 된 단일 줄을 처리 하 `LOG` 고 데이터를 일련의 열로 반환 합니다.

    > [!NOTE]
    > 스트리밍에 사용 되는 응용 프로그램 이름은 \` 별칭을 지정할 때 (backtick) 문자,와 함께 사용 될 때 ' (작은따옴표) 문자를 사용 하 여 묶어야 합니다 `SHIP` .

4. 마지막 줄을 입력하면 작업이 시작됩니다. 다음 텍스트와 비슷한 출력이 반환됩니다.

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. `exit`Pig를 종료 하는 데 사용 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight의 Hive 및 Pig에서 .NET Framework 응용 프로그램을 사용 하는 방법을 알아보았습니다. Python을 Hive 및 Pig와 함께 사용하는 방법에 대해 알고 싶으면 [HDInsight에서 Apache Hive 및 Apache Pig와 함께 Python 사용](python-udf-hdinsight.md)을 참조하세요.

Hive를 사용 하 고 MapReduce를 사용 하는 방법에 대해 알아보려면 다음 문서를 참조 하세요.

* [HDInsight에서 Apache Hive 사용](hdinsight-use-hive.md)
* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)
* [Pig 라틴어 기본 사항](https://pig.apache.org/docs/latest/basic.html)
