---
title: '빠른 시작: 일괄 처리 질문을 사용하여 기술 자료 테스트'
description: QnA Maker 일괄 처리 테스트 도구에서 QnA Maker 리소스의 기술 자료를 테스트하여 예상 대답, 신뢰도 점수 및 다중 턴 프롬프트를 확인합니다.
ms.topic: tutorial
ms.date: 05/26/2020
ms.openlocfilehash: bcee98678212a3bc233c36a831f6bcde7e6ae550
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531072"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>빠른 시작: 일괄 처리 질문 및 예상 대답을 사용하여 기술 자료 테스트

QnA Maker 일괄 처리 테스트 도구에서 QnA Maker 리소스의 기술 자료를 테스트하여 예상 대답, 신뢰도 점수 및 다중 턴 프롬프트를 확인합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* 영어를 사용하는 [QnA Maker 서비스를 만들거나](create-publish-knowledge-base.md) 기존 서비스를 사용합니다.
* [다중 턴 샘플 `.docx` 파일](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)을 다운로드합니다.
* [일괄 처리 테스트 도구](https://aka.ms/qnamakerbatchtestingtool)를 다운로드하고, `.zip` 파일에서 실행 파일을 추출합니다.

## <a name="sign-into-qna-maker-portal"></a>QnA Maker 포털에 로그인

QnA Maker 포털에 [로그인](https://www.qnamaker.ai/)합니다.

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>다중 턴 샘플 .docx 파일에서 새 기술 자료 만들기

1. 도구 모음에서 **기술 자료 만들기**를 선택합니다.
1. QnA Maker 리소스가 이미 있으므로 **1단계**를 건너뛰고, **2단계**로 이동하여 다음과 같은 기존 리소스 정보를 선택합니다.
    * Azure Active Directory ID
    * Azure 구독 이름
    * Azure QnA 서비스 이름
    * 언어 - 영어
1. 기술 자료의 이름으로 `Multi-turn batch test quickstart` 이름을 입력합니다.

1. **4단계**에서는 다음 표를 사용하여 설정을 구성합니다.

    |설정|값|
    |--|--|
    |**URL, .pdf 또는 .docx 파일에서 다중 턴 추출을 사용하도록 설정합니다.**|선택|
    |**기본 답변 텍스트**| `Batch test - default answer not found.`|
    |**+ 파일 추가**|필수 구성 요소에서 다운로드한 `.docx` 파일 목록을 선택합니다.|
    |**잡담**|**Professional** 선택|

1. **5단계**에서 **KB 만들기**를 선택합니다.

    만들기 프로세스가 완료되면 포털에 편집 가능한 기술 자료가 표시됩니다.

## <a name="save-train-and-publish-knowledge-base"></a>기술 자료 저장, 학습 및 게시

1. 도구 모음에서 **저장 및 학습**을 선택하여 기술 자료를 저장합니다.
1. 도구 모음에서 **게시**를 선택한 다음, **게시**를 다시 선택하여 기술 자료를 게시합니다. 게시하면 기술 자료를 퍼블릭 URL 엔드포인트의 쿼리에 사용할 수 있습니다. 게시가 완료되면 **게시** 페이지에 표시된 호스트 URL 및 엔드포인트 키 정보를 저장합니다.

    |필요한 데이터| 예제|
    |--|--|
    |게시된 호스트|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |게시된 키|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`(`Endpoint` 뒤에 표시되는 32자 문자열)|
    |앱 ID|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (`POST`의 일부로 표시되는 36자 문자열) |

## <a name="create-batch-test-file-with-question-ids"></a>질문 ID를 사용하여 일괄 처리 테스트 파일 만들기

일괄 처리 테스트 도구를 사용하려면 텍스트 편집기를 사용하여 `batch-test-data-1.tsv`라는 파일을 만듭니다. 파일은 UTF-8 형식이어야 하며 탭으로 구분된 다음 열이 있어야 합니다.

|TSV 입력 파일 필드|메모|예제|
|--|--|--|
|기술 자료 ID|기술 자료 ID는 [게시] 페이지에 있습니다. 단일 파일에서 여러 기술 자료 ID를 사용하여 동일한 서비스에서 여러 기술 자료를 한 번에 테스트합니다.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (`POST`의 일부로 표시되는 36자 문자열) |
|질문|사용자가 입력하는 질문 텍스트입니다. 최대 1,000자입니다.|`How do I sign out?`|
|메타데이터 태그|선택 사항|`topic:power`(_키:값_ 형식 사용)|
|top(상위 항목) 매개 변수|선택 사항|`25`|
|예상 대답 ID|선택 사항|`13`|

이 기술 자료의 경우 2개의 필수 열만 있는 3개의 행을 파일에 추가합니다. 첫 번째 열은 기술 자료 ID이고, 두 번째 열은 다음 질문 목록이어야 합니다.

|열 2 - 질문|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

이러한 질문은 기술 자료에서 나온 정확한 문구이며 신뢰도 점수로 100을 반환해야 합니다.

다음으로, 동일한 기술 자료 ID를 사용하여 다음 질문과 비슷하지만 3개 행에서 정확히 동일하지 않은 몇 가지 질문을 추가합니다.

|열 2 - 질문|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> 각 열은 탭 구분 기호로만 구분해야 합니다. 선행 또는 후행 공백이 열 데이터에 추가되며, 형식 또는 크기가 올바르지 않으면 프로그램에서 예외를 throw합니다.

Excel에서 열면 일괄 처리 테스트 파일이 다음 이미지와 같습니다. 보안상 기술 자료 ID는 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`로 대체되었습니다. 자체 일괄 처리 테스트의 경우 열에 기술 자료 ID가 표시되는지 확인합니다.

> [!div class="mx-imgBorder"]
> ![일괄 처리 테스트에서 .tsv 파일의 첫 번째 버전 입력](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>배치 파일 테스트

명령줄에서 다음 CLI 형식을 사용하여 일괄 처리 테스트 프로그램을 실행합니다.

`YOUR-RESOURCE-NAME` 및 `ENDPOINT-KEY`를 서비스 이름 및 엔드포인트 키에 대한 사용자 고유의 값으로 바꿉니다. 이러한 값은 QnA Maker 포털의 **설정** 페이지에서 찾을 수 있습니다.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
테스트가 완료되고 `out.tsv` 파일이 생성됩니다.

> [!div class="mx-imgBorder"]
> ![일괄 처리 테스트에서 .tsv 파일의 첫 번째 버전 출력](../media/batch-test/batch-test-1-output.png)

보안상 기술 자료 ID는 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`로 대체되었습니다. 자체 일괄 처리 테스트의 경우 열에 기술 자료 ID가 표시됩니다.

각 질문이 기술 자료에 표시되는 것과 정확히 동일하므로 4번째 열에 있는 신뢰도 점수의 테스트 출력에서 예상대로 100점을 반환한 상위 3개의 질문을 보여 줍니다. 질문에 대한 새 문구가 있는 마지막 3개 질문에서는 신뢰도 점수로 100을 반환하지 않습니다. 테스트와 사용자의 점수를 모두 높이려면 더 많은 대체 질문을 기술 자료에 추가해야 합니다.

## <a name="testing-with-the-optional-fields"></a>선택적 필드를 사용하여 테스트

형식과 프로세스를 이해했으면 채팅 로그와 같은 데이터 원본에서 기술 자료에 대해 실행할 테스트 파일을 생성할 수 있습니다.

데이터 원본과 프로세스가 자동화되므로 여러 설정을 사용하여 테스트 파일을 여러 번 실행하여 올바른 값을 결정할 수 있습니다.

예를 들어 채팅 로그가 있고 어떤 채팅 로그 텍스트를 어떤 메타데이터 필드에 적용할지 결정하려면 테스트 파일을 만들고 모든 행에 대한 메타데이터 필드를 설정합니다. 테스트를 실행한 다음, 메타데이터와 일치하는 행을 검토합니다. 일반적으로 일치 항목은 양수여야 하지만, 가양성 결과를 검토해야 합니다. 가양성은 메타데이터와 일치하지만, 텍스트를 기반으로 하는 경우 일치하지 않아야 하는 행입니다.

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>입력 일괄 처리 테스트 파일의 선택적 필드 사용

다음 차트를 사용하여 선택적 데이터의 필드 값을 찾는 방법을 이해합니다.

|열 번호|선택적 열|데이터 위치|
|--|--|--|
|3|metadata|기존 _키:값_ 쌍에 대한 기존 기술 자료를 내보냅니다.|
|4|top|기본값으로 `25`가 추천됩니다.|
|5|질문 및 대답 세트 ID|ID 값에 대한 기존 기술 자료를 내보냅니다. 또한 ID가 출력 파일에 반환되었는지 확인합니다.|

## <a name="add-metadata-to-the-knowledge-base"></a>기술 자료에 메타데이터 추가

1. QnA 포털의 **편집** 페이지에서 `topic:power`라는 메타데이터를 다음 질문에 추가합니다.

    |질문|
    |--|
    |Surface Pro 4 요금 청구|
    |배터리 수준 확인|

    두 개의 QnA 쌍에는 메타데이터 세트가 있습니다.

    > [!TIP]
    > 각 세트의 메타데이터 및 QnA ID를 확인하려면 기술 자료를 내보냅니다. **설정** 페이지를 선택한 다음, `.xls` 파일로 **내보내기**를 선택합니다. 다운로드한 이 파일을 찾고, Excel에서 열어 메타데이터 및 ID를 검토합니다.

1. **저장 및 학습**을 선택하고, **게시** 페이지를 선택한 다음, **게시** 단추를 선택합니다. 이러한 작업을 수행하면 일괄 처리 테스트에서 변경 내용을 사용할 수 있습니다. **설정** 페이지에서 기술 자료를 다운로드합니다.

    다운로드한 파일에는 올바른 메타데이터 형식과 올바른 질문 및 대답 세트 ID가 있습니다. 이러한 필드는 다음 섹션에서 사용합니다.

    > [!div class="mx-imgBorder"]
    > ![메타데이터를 사용하여 내보낸 기술 자료](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>두 번째 일괄 처리 테스트 만들기

일괄 처리 테스트에는 다음 두 가지 주요 시나리오가 있습니다.
* **채팅 로그 파일 처리** - 이전에 확인되지 않은 질문에 대한 상위 항목의 대답을 결정합니다. 가장 일반적인 상황은 채팅 봇의 사용자 질문과 같은 쿼리의 로그 파일을 처리해야 하는 경우입니다. 필수 열만 사용하여 배치 파일 테스트를 만듭니다. 테스트는 각 질문에 대한 상위 항목의 대답을 반환합니다. 그렇다고 상위 항목의 대답이 정답이라는 의미는 아닙니다. 이 테스트가 완료되면 유효성 검사 테스트로 이동합니다.
* **유효성 검사 테스트** - 예상 대답의 유효성을 검사합니다. 이 테스트를 수행하려면 일괄 처리 테스트에서 모든 질문과 일치하는 예상 대답의 유효성을 검사해야 합니다. 이렇게 하려면 몇 가지 수동 프로세스가 필요할 수 있습니다.

다음 절차에서는 채팅 로그를 처리하는 시나리오를 가정합니다.

1. `batch-test-data-2.tsv`라는 선택적 데이터를 포함하는 새 일괄 처리 테스트 파일을 만듭니다. 원래의 일괄 처리 테스트 입력 파일에서 6개 행을 추가한 다음, 각 행에 대한 메타데이터, 상위 항목 대답 및 QnA 쌍 ID를 추가합니다.

    기술 자료와 비교하여 채팅 로그의 새 텍스트를 확인하는 자동화된 프로세스를 시뮬레이션하려면 각 열에 대한 메타데이터를 동일한 값(`topic:power`)으로 설정합니다.

    > [!div class="mx-imgBorder"]
    > ![일괄 처리 테스트에서 .tsv 파일의 두 번째 버전 입력](../media/batch-test/batch-test-2-input.png)

1. 테스트를 다시 실행하여 두 번째 테스트임을 나타내도록 입력 및 출력 파일 이름을 변경합니다.

    > [!div class="mx-imgBorder"]
    > ![일괄 처리 테스트에서 .tsv 파일의 두 번째 버전 출력](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>테스트 결과 및 자동화된 테스트 시스템

이 테스트 출력 파일은 자동화된 연속 테스트 파이프라인의 일부로 구문 분석할 수 있습니다.

이 특정 테스트 출력은 다음과 같이 읽어야 합니다. 각 행은 메타데이터를 사용하여 필터링되었으며, 각 행이 기술 자료의 메타데이터와 일치하지 않으므로 일치하지 않는 해당 행에 대한 기본 대답("kb에서 적절한 일치 항목이 없습니다")이 반환되었습니다. 일치하는 행 중에서 QnA ID와 점수가 반환되었습니다.

예상 대답 ID와 일치하는 행이 없으므로 모든 행에서 잘못된 레이블을 반환했습니다.

이러한 결과를 통해 채팅 로그를 가져오고 텍스트를 각 행의 쿼리로 사용할 수 있음을 확인할 수 있습니다. 데이터에 대해 전혀 알지 못하더라도 결과에서 앞으로 이동하는 데 사용할 수 있는 데이터에 대해 많은 정보를 알려줍니다.

* meta-data
* QnA ID
* score

테스트에서 meta-data를 사용하여 필터링하는 것이 적절한가요? 그렇거나 그렇지 않을 수도 있습니다. 테스트 시스템은 meta-data 쌍이 없는 테스트뿐만 아니라 각 meta-data 쌍에 대한 테스트 파일도 만들어야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

기술 자료 테스트를 계속 수행하지 않으려면 배치 파일 도구와 테스트 파일을 삭제합니다.

이 기술 자료를 계속 사용하지 않으려면 다음 단계에 따라 기술 자료를 삭제합니다.

1. QnA Maker 포털의 위쪽 메뉴에서 **내 기술 자료**를 선택합니다.
1. 기술 자료 목록에서 이 빠른 시작의 기술 자료 행에 있는 **삭제** 아이콘을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://go.microsoft.com/fwlink/?linkid=2092179)
