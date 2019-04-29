---
title: 'Microsoft Genomics: 문제 해결 가이드 | Microsoft Docs'
titleSuffix: Azure
description: 문제 해결 전략에 대해 자세히 알아봅니다.
keywords: 문제 해결, 오류, 디버깅
services: microsoft-genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: article
ms.date: 10/29/2018
ms.openlocfilehash: 78084e6beac7b390b1ea1afe888030c5224856b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790507"
---
# <a name="troubleshooting-guide"></a>문제 해결 가이드

Microsoft Genomics 서비스 MSGEN을 사용할 때 발생할 수 있는 일반적인 문제를 위한 몇 가지 문제 해결 팁입니다.

 문제 해결과 관련이 없는 FAQ의 경우 [일반적인 질문](frequently-asked-questions-genomics.md)을 참조하세요.
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>1단계: 워크플로와 연결 된 오류 코드 찾기

다음을 통해 워크플로와 연결된 오류 메시지를 찾을 수 있습니다.

1. `msgen status` 명령줄 사용 및 입력
2. standardoutput.txt의 콘텐츠 검사

### <a name="1-using-the-command-line-msgen-status"></a>1. `msgen status` 명령줄 사용

```bash
msgen status -u URL -k KEY -w ID 
```




다음과 같은 세 가지 필수 인수가 있습니다.

* URL - API에 대한 기본 URI
* KEY - Genomics 계정에 대한 액세스 키
    * URL과 KEY를 찾으려면 Azure Portal로 이동하여 Microsoft Genomics 계정 페이지를 엽니다. **관리** 제목 아래에서 **액세스 키**를 선택하세요. 거기에서 API URL과 액세스 키를 모두 찾을 수 있습니다.

  
* ID - 워크플로 ID
    * 워크플로 ID를 찾으려면 `msgen list` 명령을 입력합니다. 구성 파일이 URL 및 액세스 키를 포함하며, 사용자의 msgen exe와 동일한 위치에 있다고 가정하면 명령이 다음과 같이 표시됩니다. 
        
        ```bash
        msgen list -f "config.txt"
        ```
        이 명령의 출력은 다음과 같이 표시됩니다.
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  또는 URL과 KEY를 직접 입력하는 대신 구성 파일의 경로를 포함할 수 있습니다. 이러한 인수를 구성 파일뿐만 아니라 명령줄에 포함하면 명령줄 인수가 우선적으로 적용됩니다.  

msgen 실행 파일과 동일한 경로에 있는 config.txt 파일 및 워크플로 ID 1001의 경우 명령은 다음과 같이 표시됩니다.

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2.  standardoutput.txt의 콘텐츠 검사 
질문의 워크플로에 대한 출력 컨테이너를 찾습니다. MSGEN은 모든 워크플로를 실행한 후 `[workflowfilename].logs.zip` 폴더를 만듭니다. 폴더의 압축을 풀어 해당 콘텐츠를 확인합니다.

* outputFileList.txt - 워크플로 중에 생성된 출력 파일의 목록입니다.
* standarderror.txt - 이 파일은 비어 있습니다.
* standardoutput.txt - 워크플로를 실행하는 동안 발생한 오류를 포함한 모든 최상위 상태 메시지를 기록합니다.
* GATK 로그 파일 - `logs` 폴더에 있는 다른 모든 파일입니다.

문제 해결을 위해 standardoutput.txt의 콘텐츠를 검사하고 나타나는 오류 메시지를 기록합니다.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>2단계: 일반적인 오류에 대 한 권장된 단계를 시도 하세요.

이 섹션에서는 Microsoft Genomics 서비스(msgen)에서 출력되는 일반적인 오류와 이를 해결하는 데 사용할 수 있는 전략에 대해 간략히 설명합니다. 

Microsoft Genomics 서비스(msgen)는 다음 두 종류의 오류를 throw할 수 있습니다.

1. 내부 서비스 오류: 내부 매개 변수 또는 입력된 파일을 수정 하 여 확인할 수 없는 서비스에 있는 오류입니다. 경우에 따라 워크플로를 다시 전송하면 이러한 오류를 해결할 수도 있습니다.
2. 입력된 오류: 파일 형식 수정 또는 올바른 인수를 사용 하 여 확인할 수 있는 오류입니다.

### <a name="1-internal-service-errors"></a>1. 내부 서비스 오류

내부 서비스 오류는 사용자 조치가 불가능합니다. 워크플로를 다시 제출할 수 있지만 작동하지 않는 경우 Microsoft Genomics 지원에 문의하세요.

| 오류 메시지                                                                                                                            | 권장되는 문제 해결 단계                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 내부 오류가 발생했습니다. 워크플로를 다시 제출해 보세요. 이 오류가 다시 표시되면 Microsoft Genomics 지원에 문의하세요. | 워크플로를 다시 제출합니다. 문제가 계속되면 지원 [티켓](file-support-ticket-genomics.md )을 만들어 Microsoft Genomics 지원에 문의하세요. |

### <a name="2-input-errors"></a>2. 입력 오류

이러한 오류는 사용자가 조치할 수 있습니다. 파일 형식 및 오류 코드에 따라 Microsoft Genomics 서비스는 고유한 오류 코드를 출력합니다. 아래에 나열된 권장된 문제 해결 단계를 따릅니다.

| 파일 형식 | 오류 코드 | 오류 메시지                                                                           | 권장되는 문제 해결 단계                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| 모두          | 701        | 읽기 [readId]에 [numberOfBases] 자료가 있지만 한도는 [maxReadLength]입니다.           | 이 오류에 대한 가장 일반적인 이유는 파일 손상으로 인한 두 개의 읽기 연결입니다. 입력 파일을 확인합니다. |
| BAM          | 200        |   ‘[yourFileName]’ 파일을 읽을 수 없습니다.                                                                                       | BAM 파일의 형식을 확인합니다. 올바른 형식의 파일을 사용하여 워크플로를 다시 제출합니다.                                                                           |
| BAM          | 201        |  [File_name] BAM 파일을 읽을 수 없습니다.                                                                                      |BAM 파일의 형식을 확인합니다.  올바른 형식의 파일을 사용하여 워크플로를 제출합니다.                                                                            |
| BAM          | 202        | [File_name] BAM 파일을 읽을 수 없습니다. 파일이 너무 작고 헤더가 누락되었습니다.                                                                                        | BAM 파일의 형식을 확인합니다.  올바른 형식의 파일을 사용하여 워크플로를 제출합니다.                                                                            |
| BAM          | 203        |   [File_name] BAM 파일을 읽을 수 없습니다. 파일의 헤더가 손상되었습니다.                                                                                      |BAM 파일의 형식을 확인합니다.  올바른 형식의 파일을 사용하여 워크플로를 제출합니다.                                                                           |
| BAM          | 204        |    [File_name] BAM 파일을 읽을 수 없습니다. 파일의 헤더가 손상되었습니다.                                                                                     | BAM 파일의 형식을 확인합니다.  올바른 형식의 파일을 사용하여 워크플로를 제출합니다.                                                                           |
| BAM          | 205        |    [File_name] BAM 파일을 읽을 수 없습니다. 파일의 헤더가 손상되었습니다.                                                                                     | BAM 파일의 형식을 확인합니다.  올바른 형식의 파일을 사용하여 워크플로를 제출합니다.                                                                            |
| BAM          | 206        |   [File_name] BAM 파일을 읽을 수 없습니다. 파일의 헤더가 손상되었습니다.                                                                                      | BAM 파일의 형식을 확인합니다.  올바른 형식의 파일을 사용하여 워크플로를 제출합니다.                                                                            |
| BAM          | 207        |  [File_name] BAM 파일을 읽을 수 없습니다. 파일이 [offset] 오프셋 근처에서 잘립니다.                                                                                       | BAM 파일의 형식을 확인합니다.  올바른 형식의 파일을 사용하여 워크플로를 제출합니다.                                                                            |
| BAM          | 208        |   BAM 파일이 잘못되었습니다. [Read_Id] ReadID에 [File_name] 파일의 시퀀스가 없습니다.                                                                                      | BAM 파일의 형식을 확인합니다.  올바른 형식의 파일을 사용하여 워크플로를 제출합니다.                                                                             |
| FASTQ        | 300        |  FASTQ 파일을 읽을 수 없습니다. [File_name]이(가) 줄 바꿈 문자로 끝나지 않았습니다.                                                                                     | FASTQ 파일의 형식을 수정하고 워크플로를 다시 제출합니다.                                                                           |
| FASTQ        | 301        |   [File_name] FASTQ 파일을 읽을 수 없습니다. FASTQ 레코드가 [_offset] 오프셋의 버퍼 크기보다 큽니다.                                                                                      | FASTQ 파일의 형식을 수정하고 워크플로를 다시 제출합니다.                                                                         |
| FASTQ        | 302        |     FASTQ 구문 오류입니다. [File_name] 파일에 빈 줄에 있습니다.                                                                                    | FASTQ 파일의 형식을 수정하고 워크플로를 다시 제출합니다.                                                                         |
| FASTQ        | 303        |       FASTQ 구문 오류입니다. [File_name] 파일의 [_offset] 오프셋, [line_type] 줄 형식, [_char] 문자에서 시작 문자가 잘못되었습니다.                                                                                  | FASTQ 파일의 형식을 수정하고 워크플로를 다시 제출합니다.                                                                         |
| FASTQ        | 304      |  [_ReadID] ReadID에서 FASTQ 구문 오류가 있습니다.  일괄 처리의 첫 번째 읽기에 [File_name] 파일의 /1에서 끝나는 readID가 없습니다.                                                                                       | FASTQ 파일의 형식을 수정하고 워크플로를 다시 제출합니다.                                                                         |
| FASTQ        | 305        |  [_readID] readID에서 FASTQ 구문 오류가 있습니다. 일괄 처리의 두 번째 읽기에 [File_name] 파일의 /2에서 끝나는 readID가 없습니다.                                                                                      | FASTQ 파일의 형식을 수정하고 워크플로를 다시 제출합니다.                                                                          |
| FASTQ        | 306        |  [_ReadID] ReadID에서 FASTQ 구문 오류가 있습니다. 쌍의 첫 번째 읽기에 [File_name] 파일의 /1에서 끝나는 ID가 없습니다.                                                                                       | FASTQ 파일의 형식을 수정하고 워크플로를 다시 제출합니다.                                                                          |
| FASTQ        | 307        |   [_ReadID] ReadID에서 FASTQ 구문 오류가 있습니다. ReadID가 /1 또는 /2로 끝나지 않습니다. [File_name] 파일을 쌍으로 연결된 FASTQ 파일로 사용할 수 없습니다.                                                                                      |FASTQ 파일의 형식을 수정하고 워크플로를 다시 제출합니다.                                                                          |
| FASTQ        | 308        |  FASTQ 읽기 오류입니다. 두 읽기가 서로 다르게 응답됩니다. 올바른 FASTQ 파일을 선택하지 못했습니까?                                                                                       | FASTQ 파일의 형식을 수정하고 워크플로를 다시 제출합니다.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>3단계: Microsoft Genomics 지원에 문의

작업이 계속 실패하거나 다른 질문이 있는 경우 Azure Portal의 Microsoft Genomics 지원에 문의하세요. 지원 요청을 제출하는 방법에 대한 추가 정보는 [여기](file-support-ticket-genomics.md)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Genomics 서비스와 관련된 일반적인 문제를 해결하는 방법을 알아보았습니다. 자세한 내용 및 일반적인 FAQ는 [일반적인 질문](frequently-asked-questions-genomics.md)을 참조하세요. 
