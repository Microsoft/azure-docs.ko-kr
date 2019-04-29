---
title: Azure Data Lake U-SQL SDK를 사용하여 U-SQL 작업을 로컬로 실행 및 테스트
description: 로컬 워크스테이션에서 명령줄 및 프로그래밍 인터페이스를 사용하여 U-SQL 작업을 로컬로 실행 및 테스트하는 방법을 알아봅니다.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 14908225e78b79cb748e712ae23643ddde4a4242
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813498"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Azure Data Lake U-SQL SDK를 사용하여 U-SQL 실행 및 테스트

U-SQL 스크립트를 개발할 때 클라우드로 제출하기 전에 로컬에서 U-SQL 스크립트를 실행하고 테스트하는 것이 일반적입니다. Azure Data Lake는 이 시나리오에 대해 Azure Data Lake U-SQL SDK라는 Nuget 패키지를 제공합니다. 이를 통해 U-SQL 실행 및 테스트 규모를 쉽게 조정할 수 있습니다. 이 U-SQL 테스트를 CI(연속 통합) 시스템에 통합하여 컴파일 및 테스트를 자동화할 수도 있습니다.

GUI 도구를 사용하여 U-SQL 스크립트를 수동으로 로컬 실행 및 디버깅하는 방법에 관심이 있는 경우 Azure Data Lake Tools for Visual Studio를 사용할 수 있습니다. [여기](data-lake-analytics-data-lake-tools-local-run.md)에서 자세히 알아볼 수 있습니다.

## <a name="install-azure-data-lake-u-sql-sdk"></a>Azure Data Lake U-SQL SDK 설치

Nuget.org의 [여기](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)에서 Azure Data Lake U-SQL SDK를 다운로드할 수 있습니다. 사용하기 전에 다음과 같이 종속성이 있는지 확인해야 합니다.

### <a name="dependencies"></a>종속성

Data Lake U-SQL SDK에는 다음과 같은 종속성이 필요합니다.

- [Microsoft .NET Framework 4.6 이상](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 및 Windows SDK 10.0.10240.0 이상(이 문서에서는 CppSDK로 지칭함). CppSDK는 다음 두 가지 방법으로 다운로드할 수 있습니다.

  - [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou)을 설치합니다. 프로그램 파일 폴더 아래에 \Windows Kits\10 폴더가 생성됩니다(예: C:\Program Files (x86)\Windows Kits\10\). 또한 \Windows Kits\10\Lib 아래에서 Windows 10 SDK 버전을 찾습니다. 이들 폴더가 보이지 않으면 Visual Studio를 다시 설치하고 설치하는 동중 Windows 10 SDK를 선택해야 합니다. Visual Studio와 함께 설치된 경우에는 U-SQL 로컬 컴파일러가 해당 버전을 자동으로 찾습니다.

    ![Data Lake Tools for Visual Studio의 Windows 10 SDK 로컬 실행](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - [Visual Studio용 Data Lake 도구](https://aka.ms/adltoolsvs)를 설치합니다. 미리 패키지된 Visual C++ 및 Windows SDK 파일은 C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK에서 찾을 수 있습니다. 이 경우 U-SQL 로컬 컴파일러는 이러한 종속성을 자동으로 찾을 수 없습니다. 이에 대한 CppSDK 경로를 지정해야 합니다. 파일을 다른 위치로 복사하거나 그대로 사용할 수 있습니다.

## <a name="understand-basic-concepts"></a>기본 개념 이해

### <a name="data-root"></a>데이터 루트

데이터 루트 폴더는 로컬 컴퓨팅 계정의 "로컬 저장소"입니다. 이는 Data Lake Analytics 계정의 Azure Data Lake Store 계정과 동일합니다. 다른 데이터 루트 폴더로 전환하는 것은 다른 저장소 계정으로 전환하는 것과 같습니다. 다른 데이터 루트 폴더와 공유된 데이터에 액세스하려는 경우 스크립트에서 절대 경로를 사용해야 합니다. 또는 데이터 루트 폴더 아래에 파일 시스템 심볼 링크를 만들어 공유 데이터를 가리키도록 합니다(예, NTFS의 **mklink**).

데이터 루트 폴더는 다음 용도로 사용됩니다.

- 데이터베이스, 테이블, TVF(테이블 반환 함수), 어셈블리 등의 로컬 메타데이터를 저장합니다.
- U-SQL에서 상대 경로로 정의된 입력 및 출력 경로 조회 - 상대 경로를 사용하면 U-SQL 프로젝트를 Azure에 보다 쉽게 배포할 수 있습니다.

### <a name="file-path-in-u-sql"></a>U-SQL의 파일 경로

U-SQL 스크립트의 상대 경로 및 로컬 절대 경로를 사용할 수 있습니다. 상대 경로는 지정된 데이터-루트 폴더 경로 기준으로 하는 것입니다. 스크립트를 서버 쪽과 호환되게 하려면 경로 구분 기호로 "/"를 사용하는 것이 좋습니다. 다음은 상대 경로 및 이와 대등한 절대 경로의 몇 가지 예입니다. 이 예에서 C:\LocalRunDataRoot는 데이터 루트 폴더입니다.

|상대 경로|절대 경로|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>작업 디렉터리

U-SQL 스크립트를 로컬로 실행하면 컴파일 중에 현재 실행 중인 디렉터리 아래에 작업 디렉터리가 만들어집니다. 컴파일 결과 외에도 로컬 실행에 필요한 런타임 파일이 이 작업 디렉터리에 섀도 복사됩니다. 작업 디렉터리 루트 폴더를 "ScopeWorkDir"이라고 하고 작업 디렉터리 아래의 파일은 다음과 같습니다.

|디렉터리/파일|디렉터리/파일|디렉터리/파일|정의|설명|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |런타임 버전의 해시 문자열|로컬 실행에 필요한 런타임 파일의 섀도 복사본|
| |Script_66AE4909AA0ED06C| |스크립트 이름 + 스크립트 경로의 해시 문자열|컴파일 출력 및 실행 단계 로깅|
| | |\_script\_.abr|컴파일러 출력|대수 파일|
| | |\_ScopeCodeGen\_.*|컴파일러 출력|생성된 관리 코드|
| | |\_ScopeCodeGenEngine\_.*|컴파일러 출력|생성된 네이티브 코드|
| | |참조된 어셈블리|어셈블리 참조|참조된 어셈블리 파일|
| | |deployed_resources|리소스 배포|리소스 배포 파일|
| | |xxxxxxxx.xxx[1..n]\_\*.\*|실행 로그|실행 단계에 대한 로그|


## <a name="use-the-sdk-from-the-command-line"></a>명령줄에서 SDK 사용

### <a name="command-line-interface-of-the-helper-application"></a>도우미 애플리케이션의 명령줄 인터페이스

SDK directory\build\runtime에서 LocalRunHelper.exe는 일반적으로 사용되는 대부분의 로컬 실행 기능에 대한 인터페이스를 제공하는 명령줄 도우미 애플리케이션입니다. 명령 및 인수 스위치는 모두 대소문자를 구분합니다. 도우미를 호출하려면 다음 명령을 사용합니다.

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

인수를 사용하지 않거나 다음과 같이 **help** 스위치와 함께 LocalRunHelper.exe를 실행하여 도움말 정보를 표시합니다.

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

도움말 정보에서 다음과 같이 구성됩니다.

-  **Command**: 명령의 이름을 제공합니다.  
-  **Required Argument**: 제공해야 하는 인수를 나열합니다.  
-  **Optional Argument**: 선택적이며 기본값을 갖는 인수를 나열합니다.  선택적 부울 인수에는 매개 변수가 없으며, 이 매개 변수의 출현은 기본값에 부정적이라는 것을 의미합니다.

### <a name="return-value-and-logging"></a>반환 값 및 로깅

도우미 애플리케이션은 성공한 경우 **0**을, 실패한 경우 **-1**을 반환합니다. 기본적으로 도우미는 모든 메시지를 현재 콘솔로 보냅니다. 그러나 대부분의 명령은 출력을 로그 파일로 리디렉션하는 **-MessageOut path_to_log_file** 선택적 인수를 지원합니다.

### <a name="environment-variable-configuring"></a>환경 변수 구성

U-SQL 로컬 실행을 위해서는 지정된 데이터 루트가 로컬 저장소 계정으로 필요하고, 종속성에 대해 지정된 CppSDK 경로가 필요합니다. 명령줄에서 인수를 설정하고 그에 대한 환경 변수를 설정할 수 있습니다.

- **SCOPE_CPP_SDK** 환경 변수를 설정합니다.

    Data Lake Tools for Visual Studio를 설치하여 Microsoft Visual C++ 및 Windows SDK를 가져온 경우 다음 폴더가 있는지 확인합니다.

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    이 디렉터리를 가리키도록 **SCOPE_CPP_SDK**라는 새로운 환경 변수를 정의합니다. 또는 다른 위치에 폴더를 복사하고 **SCOPE_CPP_SDK**를 지정합니다.

    환경 변수를 설정하는 것 외에도 명령줄을 사용할 때 **-CppSDK** 인수를 지정할 수도 있습니다. 이 인수는 기본 CppSDK 환경 변수를 덮어씁니다.

- **LOCALRUN_DATAROOT** 환경 변수를 설정합니다.

    데이터 루트를 가리키는 **LOCALRUN_DATAROOT**라는 새로운 환경 변수를 정의합니다.

    환경 변수를 설정하는 것 외에도 명령줄을 사용할 때 데이터 루트 경로로 **-DataRoot** 인수를 지정할 수 있습니다. 이 인수는 기본 데이터 루트 환경 변수를 덮어씁니다. 모든 작업에 대해 기본 데이터 루트 환경 변수를 덮어 쓸 수 있도록 실행 중인 모든 명령줄에 이 인수를 추가해야 합니다.

### <a name="sdk-command-line-usage-samples"></a>SDK 명령줄 사용 샘플

#### <a name="compile-and-run"></a>컴파일 및 실행

**run** 명령은 스크립트를 컴파일한 다음 컴파일 결과를 실행하는 데 사용됩니다. 명령줄 인수는 **compile**과 **execute**의 조합입니다.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

다음은 **run**에 대한 선택적 인수입니다.


|인수|기본값|설명|
|--------|-------------|-----------|
|-CodeBehind|거짓|스크립트에는 .cs 코드 숨김이 있습니다.|
|-CppSDK| |CppSDK 디렉터리입니다.|
|-DataRoot| DataRoot 환경 변수|로컬 실행을 위한 데이터 루트이며, 기본값은 'LOCALRUN_DATAROOT' 환경 변수입니다.|
|-MessageOut| |콘솔의 메시지를 파일에 덤프합니다.|
|-Parallel|1|지정된 병렬 처리로 계획을 실행합니다.|
|-References| |';'(세미콜론)으로 구분된 코드 참조의 추가 참조 어셈블리 또는 데이터 파일의 경로 목록입니다.|
|-UdoRedirect|거짓|Udo 어셈블리 리디렉션 구성을 생성합니다.|
|-UseDatabase|마스터|코드 숨김 임시 어셈블리 등록에 사용할 데이터베이스입니다.|
|-Verbose|거짓|런타임의 자세한 출력을 표시합니다.|
|-WorkDir|현재 디렉터리|컴파일러 사용 및 출력을 위한 디렉터리입니다.|
|-RunScopeCEP|0|사용할 ScopeCEP 모드입니다.|
|-ScopeCEPTempPath|temp|데이터 스트리밍에 사용할 임시 경로입니다.|
|-OptFlags| |쉼표로 구분된 최적화 프로그램 플래그 목록입니다.|


예를 들면 다음과 같습니다.

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

**compile**과 **execute**를 조합하는 것 외에도 컴파일된 실행 파일을 개별적으로 컴파일하고 실행할 수 있습니다.

#### <a name="compile-a-u-sql-script"></a>U-SQL 스크립트 컴파일

**compile** 명령은 U-SQL 스크립트를 실행 파일로 컴파일하는 데 사용됩니다.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

다음은 **compile**에 대한 선택적 인수입니다.


|인수|설명|
|--------|-----------|
| -CodeBehind [기본값 'False']|스크립트에는 .cs 코드 숨김이 있습니다.|
| -CppSDK [기본값 '']|CppSDK 디렉터리입니다.|
| -DataRoot [기본값 'DataRoot 환경 변수']|로컬 실행을 위한 데이터 루트이며, 기본값은 'LOCALRUN_DATAROOT' 환경 변수입니다.|
| -MessageOut [기본값 '']|콘솔의 메시지를 파일에 덤프합니다.|
| -References [기본값 '']|';'(세미콜론)으로 구분된 코드 참조의 추가 참조 어셈블리 또는 데이터 파일의 경로 목록입니다.|
| -Shallow [기본값 'False']|단순 컴파일입니다.|
| -UdoRedirect [기본값 'False']|Udo 어셈블리 리디렉션 구성을 생성합니다.|
| -UseDatabase [기본값 'master']|코드 숨김 임시 어셈블리 등록에 사용할 데이터베이스입니다.|
| -WorkDir [기본값 '현재 디렉터리']|컴파일러 사용 및 출력을 위한 디렉터리입니다.|
| -RunScopeCEP [기본값 '0']|사용할 ScopeCEP 모드입니다.|
| -ScopeCEPTempPath [기본값 'temp']|데이터 스트리밍에 사용할 임시 경로입니다.|
| -OptFlags [기본값 '']|쉼표로 구분된 최적화 프로그램 플래그 목록입니다.|


몇 가지 사용 예제는 다음과 같습니다.

U-SQL 스크립트를 컴파일합니다.

    LocalRunHelper compile -Script d:\test\test1.usql

U-SQL 스크립트를 컴파일하고 데이터 루트 폴더를 설정합니다. 이는 환경 변수 설정을 덮어 씁니다.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

U-SQL 스크립트를 컴파일하고 작업 디렉터리, 참조 어셈블리 및 데이터베이스를 설정합니다.

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>컴파일된 결과 실행

**execute** 명령은 컴파일된 결과를 실행하는 데 사용됩니다.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

다음은 **compile**에 대한 선택적 인수입니다.

|인수|기본값|설명|
|--------|-------------|-----------|
|-DataRoot | '' |메타데이터 실행을 위한 루트 데이터입니다. **LOCALRUN_DATAROOT** 환경 변수로 기본 설정합니다.|
|-MessageOut | '' |콘솔의 메시지를 파일에 덤프합니다.|
|-Parallel | '1' |지정된 병렬 처리 수준으로 생성된 로컬 실행 단계를 실행하는 표시기입니다.|
|-Verbose | 'False' |런타임의 자세한 출력을 보여주는 표시기입니다.|

사용 예는 다음과 같습니다.

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>프로그래밍 인터페이스와 함께 SDK 사용

프로그래밍 인터페이스는 모두 LocalRunHelper.exe에 있습니다. 이 인터페이스를 사용하여 U-SQL SDK 및 C# 테스트 프레임워크의 기능을 통합하여 U-SQL 스크립트 로컬 테스트의 크기를 조정할 수 있습니다. 이 문서에서는 이러한 인터페이스를 사용하여 U-SQL 스크립트를 테스트하는 방법을 보여 주기 위해 표준 C# 단위 테스트 프로젝트를 사용합니다.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>1단계: 만들기 C# 단위 테스트 프로젝트 및 구성

- 파일 > 새로 만들기 > 프로젝트 > Visual C# > 테스트 > 단위 테스트 프로젝트를 통해 C# 단위 테스트 프로젝트를 만듭니다.
- 프로젝트에 대한 참조로 LocalRunHelper.exe를 추가합니다. LocalRunHelper.exe는 Nuget 패키지의 \build\runtime\LocalRunHelper.exe에 있습니다.

    ![Azure Data Lake U-SQL SDK 참조 추가](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK는 x64 환경**만** 지원합니다. 따라서 빌드 플랫폼 대상을 x64로 설정해야 합니다. 프로젝트 속성 > 빌드 > 플랫폼 대상을 통해 설정할 수 있습니다.

    ![Azure Data Lake U-SQL SDK x64 프로젝트 구성](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- 테스트 환경을 x64로 설정해야 합니다. Visual Studio에서 테스트 > 테스트 설정 > 기본 프로세서 아키텍처 > x64를 통해 설정할 수 있습니다.

    ![Azure Data Lake U-SQL SDK x64 테스트 환경 구성](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- NugetPackage\build\runtime\ 아래에 있는 모든 종속성 파일을 일반적으로 ProjectFolder\bin\x64\Debug 아래에 있는 프로젝트 작업 디렉터리로 복사해야 합니다.

### <a name="step-2-create-u-sql-script-test-case"></a>2단계: U-SQL 스크립트 테스트 사례 만들기

다음은 U-SQL 스크립트 테스트에 대한 샘플 코드입니다. 테스트를 위해 스크립트, 입력 파일 및 예상 출력 파일을 준비해야 합니다.

    using System;
    using Microsoft.VisualStudio.TestTools.UnitTesting;
    using System.IO;
    using System.Text;
    using System.Security.Cryptography;
    using Microsoft.Analytics.LocalRun;

    namespace UnitTestProject1
    {
        [TestClass]
        public class USQLUnitTest
        {
            [TestMethod]
            public void TestUSQLScript()
            {
                //Specify the local run message output path
                StreamWriter MessageOutput = new StreamWriter("../../../log.txt");

                LocalRunHelper localrun = new LocalRunHelper(MessageOutput);

                //Configure the DateRoot path, Script Path and CPPSDK path
                localrun.DataRoot = "../../../";
                localrun.ScriptPath = "../../../Script/Script.usql";
                localrun.CppSdkDir = "../../../CppSDK";

                //Run U-SQL script
                localrun.DoRun();

                //Script output 
                string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");

                //Expected script output
                string ExpectedResult = "../../../ExpectedOutput/result.csv";

                Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);

                //Don't forget to close MessageOutput to get logs into file
                MessageOutput.Close();
            }
        }
    }

    namespace Test.Helpers
    {
        public static class FileAssert
        {
            static string GetFileHash(string filename)
            {
                Assert.IsTrue(File.Exists(filename));

                using (var hash = new SHA1Managed())
                {
                    var clearBytes = File.ReadAllBytes(filename);
                    var hashedBytes = hash.ComputeHash(clearBytes);
                    return ConvertBytesToHex(hashedBytes);
                }
            }

            static string ConvertBytesToHex(byte[] bytes)
            {
                var sb = new StringBuilder();

                for (var i = 0; i < bytes.Length; i++)
                {
                    sb.Append(bytes[i].ToString("x"));
                }
                return sb.ToString();
            }

            public static void AreEqual(string filename1, string filename2)
            {
                string hash1 = GetFileHash(filename1);
                string hash2 = GetFileHash(filename2);

                Assert.AreEqual(hash1, hash2);
            }
        }
    }


### <a name="programming-interfaces-in-localrunhelperexe"></a>LocalRunHelper.exe의 프로그래밍 인터페이스

LocalRunHelper.exe는 U-SQL 로컬 컴파일, 실행 등을 위한 프로그래밍 인터페이스를 제공합니다. 이러한 인터페이스는 다음과 같습니다.

**생성자**

public LocalRunHelper([System.IO.TextWriter messageOutput = null])

|매개 변수|형식|설명|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|출력 메시지의 경우 콘솔을 사용하도록 null로 설정|

**Properties**

|자산|Type|설명|
|--------|----|-----------|
|AlgebraPath|문자열|대수 파일의 경로입니다(대수 파일은 컴파일 결과 중 하나임).|
|CodeBehindReferences|문자열|스크립트에 추가 코드 숨김 참조가 있으면 경로를 ';'으로 구분합니다.|
|CppSdkDir|문자열|CppSDK 디렉터리입니다.|
|CurrentDir|문자열|현재 디렉터리입니다.|
|DataRoot|문자열|데이터 루트 경로입니다.|
|DebuggerMailPath|문자열|디버거 메일 슬롯에 대한 경로입니다.|
|GenerateUdoRedirect|bool|어셈블리 로딩 리디렉션 오버라이드 구성을 생성하려는 경우에 사용합니다.|
|HasCodeBehind|bool|스크립트에는 코드 숨김이 있습니다.|
|InputDir|문자열|입력 데이터에 대한 디렉터리입니다.|
|MessagePath|문자열|메시지 덤프 파일 경로입니다.|
|OutputDir|문자열|출력 데이터에 대한 디렉터리입니다.|
|병렬 처리|ssNoversion|대수 실행을 위한 병렬 처리입니다.|
|ParentPid|ssNoversion|종료할 서비스 모니터에 대한 상위 모니터 PID입니다. 무시하려면 0 또는 음수로 설정합니다.|
|ResultPath|문자열|결과 덤프 파일 경로입니다.|
|RuntimeDir|문자열|런타임 디렉터리입니다.|
|ScriptPath|문자열|스크립트를 찾을 수 있는 위치입니다.|
|Shallow|bool|단순 컴파일이거나 그렇지 않습니다.|
|TempDir|문자열|임시 디렉터리|
|UseDataBase|문자열|코드 숨김 임시 어셈블리 등록에 사용할 데이터베이스를 지정합니다. 기본값은 master입니다.|
|WorkDir|문자열|기본 설정 작업 디렉터리입니다.|


**메서드**

|메서드|설명|Return|매개 변수|
|------|-----------|------|---------|
|public bool DoCompile()|U-SQL 스크립트를 컴파일합니다.|성공 시 True입니다.| |
|public bool DoExec()|컴파일된 결과를 실행합니다.|성공 시 True입니다.| |
|public bool DoRun()|U-SQL 스크립트(Compile + Execute)를 실행합니다.|성공 시 True입니다.| |
|public bool IsValidRuntimeDir(문자열 경로)|지정된 경로가 유효한 런타임 경로인지 확인합니다.|유효한 경우 True입니다.|런타임 디렉터리의 경로입니다.|


## <a name="faq-about-common-issue"></a>일반적인 문제에 대한 FAQ

### <a name="error-1"></a>오류 1:
E_CSC_SYSTEM_INTERNAL: 내부 오류입니다. 파일 또는 어셈블리 'ScopeEngineManaged.dll'이나 해당 종속성 중 하나를 로드할 수 없습니다. 지정된 모듈을 찾을 수 없습니다.

다음 항목을 확인하세요.

- X64 환경인지 확인합니다. 빌드 대상 플랫폼 및 테스트 환경은 x64을 가리킵니다 **1 단계: 만들기 C# 단위 테스트 프로젝트 및 구성** 위에 있습니다.
- NugetPackage\build\runtime\ 아래의 모든 종속 파일을 프로젝트 작업 디렉터리로 복사했는지 확인합니다.


## <a name="next-steps"></a>다음 단계

* U-SQL을 알아보려면 [Azure Data Lake Analytics U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)을 참조하세요.
* 진단 정보를 기록하려면 [Azure Data Lake Analytics에 대한 진단 로그에 액세스](data-lake-analytics-diagnostic-logs.md)를 참조하세요.
* 더 복잡한 쿼리를 보려면 [Azure Data Lake Analytics을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)을 참조하세요.
* 작업 세부 정보를 보려면, [Azure Data lake Analytics 작업에 대한 작업 브라우저 및 작업 보기 사용하기](data-lake-analytics-data-lake-tools-view-jobs.md)를 참조하세요.
* 꼭짓점 실행 보기를 사용하려면 [Data Lake Tools for Visual Studio에서 Vertex Execution View 사용](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)을 참조하세요.
