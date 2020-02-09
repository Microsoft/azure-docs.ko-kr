---
title: 일반적인 질문-FAQ
titleSuffix: Microsoft Genomics
description: 기술 정보, SLA 및 청구를 포함 하 여 Microsoft Genomics 서비스 사용과 관련 된 일반적인 질문에 대한 답변을 받으세요.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: e8806bc4f761214e6740a22093b7e18030fdf881
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986039"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: 일반적인 질문

이 문서에는 Microsoft Genomics와 관련될 수 있는 주요 질문이 나열되어 있습니다. Microsoft Genomics 서비스에 대한 자세한 내용은 [Microsoft Genomics란?](overview-what-is-genomics.md)을 참조하세요. 문제 해결 방법에 대한 자세한 내용은 [문제 해결 가이드](troubleshooting-guide-genomics.md)를 참조하세요. 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Microsoft Genomics에서 GATK4 워크플로를 실행할 어떻게 할까요? 있나요?
Microsoft Genomics 서비스의 config.xml 파일에서 `gatk4`process_name를 지정 합니다. 정기적인 청구 요금으로 요금이 청구 됩니다.

## <a name="how-do-i-enable-output-compression"></a>출력 압축을 사용 하도록 설정 어떻게 할까요??
출력 압축에 대한 선택적 인수를 사용 하 여 출력 .vcf 또는 gvcf를 압축할 수 있습니다. 이는 `.gz` (bgzip output) 및 `.tbi` (tabix output) 파일을 생성 하기 위해 `-tabix` `-bgzip`를 실행 하는 것과 동일 합니다. `bgzip`는 .vcf 또는 gvcf 파일을 압축 하 `tabix` 압축 된 파일에 대한 인덱스를 만듭니다. 인수는 부울로, 기본적으로 `false`는 .vcf 출력에 대해 설정 되 고 gcvf 출력에 대해 기본적으로 `true` 됩니다. 명령줄에서를 사용 하려면 `-bz` 또는 `--bgzip-output`을 `true` (bgzip 및 tabix 실행) 또는 `false`로 지정 합니다. Config.xml 파일에서이 인수를 사용 하려면 파일에 `bgzip_output: true` 또는 `bgzip_output: false`를 추가 합니다.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>Microsoft Genomics의 SLA는 무엇인가요?
워크플로 API 요청을 수신하는 데 Microsoft Genomics 서비스의 99.9% 가용성을 보장합니다. 자세한 내용은 [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/)를 참조하세요.

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Microsoft Genomics의 사용량은 청구서에 어떻게 표시되나요?
Microsoft Genomics는 워크플로당 처리된 기가베이스 수를 기준으로 요금이 청구됩니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/genomics/)을 참조하세요.


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>`msgen` 클라이언트에 대해 가능한 모든 명령 및 인수 목록은 어디서 찾을 수 있나요?
`msgen help`를 실행하여 사용 가능한 명령 및 인수의 전체 목록을 가져올 수 있습니다. 추가 인수가 제공되지 않으면 각 `submit`, `list`, `cancel` 및 `status`에 대해 사용 가능한 도움말 섹션 목록이 표시됩니다. 특정 명령에 대한 도움말을 보려면 `msgen help command`를 입력하세요. 예를 들어, `msgen help submit`을 입력하면 모든 제출 옵션이 나열됩니다.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>`msgen` 클라이언트에 대해 가장 일반적으로 사용되는 명령은 무엇인가요?
`msgen` 클라이언트에 대해 가장 일반적으로 사용되는 명령은 다음과 같습니다. 

 |**명령**          |  **필드 설명** |
 |:--------------------|:-------------         |
 |`list`               |제출한 작업 목록을 반환합니다. 인수는 `msgen help list`을 참조하세요.  |
 |`submit`             |워크플로 요청을 서비스에 제출합니다. 인수는 `msgen help submit`을 참조하세요.|
 |`status`             |`--workflow-id`에 의해 지정된 워크플로의 상태를 반환합니다. `msgen help status`도 참조하세요. |
 |`cancel`             |`--workflow-id`에 의해 지정된 워크플로의 처리를 취소하기 위한 요청을 보냅니다. `msgen help cancel`도 참조하세요. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>`--api-url-base`에 대한 값을 어디에서 가져올 수 있나요?
Azure Portal로 이동하여 Genomics 계정 페이지를 여세요. **관리** 제목 아래에서 **액세스 키**를 선택하세요. 거기에서 API URL과 액세스 키를 모두 찾을 수 있습니다.

## <a name="where-do-i-get-the-value-for---access-key"></a>`--access-key`에 대한 값을 어디에서 가져올 수 있나요?
Azure Portal로 이동하여 Genomics 계정 페이지를 여세요. **관리** 제목 아래에서 **액세스 키**를 선택하세요. 거기에서 API URL과 액세스 키를 모두 찾을 수 있습니다.

## <a name="why-do-i-need-two-access-keys"></a>두 개의 액세스 키가 필요한 이유는 무엇인가요?
서비스 사용을 중단하지 않고 업데이트(재생성)하려면 두 개의 액세스 키가 필요합니다. 예를 들어 첫 번째 키를 업데이트하려는 경우 모든 새 워크플로에서 두 번째 키를 사용해야 합니다. 그런 다음, 첫 번째 키를 업데이트하기 전에 첫 번째 키를 사용하는 모든 워크플로가 완료될 때까지 기다립니다.

## <a name="do-you-save-my-storage-account-keys"></a>내 스토리지 계정 키를 저장하나요?
스토리지 계정 키는 Microsoft Genomics 서비스에 대한 단기 액세스 토큰을 만들어 입력 파일을 읽고 출력 파일을 쓰는 데 사용됩니다. 기본 토큰 지속 기간은 48시간입니다. 토큰 지속 기간은 submit 명령의 `-sas/--sas-duration` 옵션으로 변경할 수 있습니다. 값은 시간 단위입니다.

## <a name="what-genome-references-can-i-use"></a>어떤 게놈 참조를 사용할 수 있나요?

지원되는 참조는 다음과 같습니다.

 |참조              | `-pa/--process-args`의 값 |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38(대체 분석 없음) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>명령줄 인수를 구성 파일로 형식 지정하려면 어떻게 해야 하나요? 

msgen은 다음 형식으로 구성 파일을 이해합니다.
* 모든 옵션은 키에서 콜론으로 구분된 값이 있는 키-값 쌍으로 제공됩니다.
  공백은 무시됩니다.
* `#`으로 시작하는 줄은 무시됩니다.
* 긴 형식의 명령줄 인수는 선행 대시를 제거하고 단어 간 대시를 밑줄로 바꾸어 키로 변환할 수 있습니다. 다음은 몇 가지 변환 예입니다.

  |명령줄 인수            | 구성 파일 줄 |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base: https://url*    |
  |`-k/--access-key KEY`            | *access_key:KEY*              |      
  |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>다음 단계

다음 리소스를 사용하여 Microsoft Genomics를 시작하세요.
- 먼저 Microsoft Genomics 서비스를 통해 첫 번째 워크플로를 실행합니다. [Microsoft Genomics 서비스를 통해 워크플로 실행](quickstart-run-genomics-workflow-portal.md)
- Microsoft Genomics 서비스에서 처리하도록 자체 데이터 제출: [연결된 FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [여러 FASTQ 또는 BAM](quickstart-input-multiple.md) 

