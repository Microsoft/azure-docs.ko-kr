---
title: 기술 자료 관리-QnA Maker
description: QnA Maker를 사용 하면 기술 자료 설정 및 콘텐츠에 대 한 액세스를 제공 하 여 기술 자료를 관리할 수 있습니다.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 08736ce61e6768cfd9ac3d3ded7092a4ab93fca9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551781"
---
# <a name="create-knowledge-base-and-manage-settings"></a>기술 자료 만들기 및 설정 관리

QnA Maker를 사용 하면 기술 자료 설정 및 데이터 원본에 대 한 액세스를 제공 하 여 기술 자료를 관리할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

> [!div class="checklist"]
> * Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/cognitive-services/) 계정을 만듭니다.
> * Azure Portal에서 만든 [QnA Maker 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) 입니다. 리소스를 만들 때 선택한 Azure Active Directory ID, 구독, QnA 리소스 이름을 기억하세요.

## <a name="create-a-knowledge-base"></a>기술 자료 만들기

1. Azure 자격 증명으로 [QnAMaker.ai](https://QnAMaker.ai) 포털에 로그인합니다.

1. QnA Maker 포털에서 **기술 자료 만들기**를 선택합니다.

1. **만들기** 페이지에서 QnA Maker 리소스가 이미 있는 경우 **1단계**를 건너뜁니다.

    리소스를 아직 만들지 않은 경우 **QnA 서비스 만들기**를 선택합니다. 그러면 구독에서 QnA Maker 서비스를 설정할 수 있는 [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)로 연결됩니다. 리소스를 만들 때 선택한 Azure Active Directory ID, 구독, QnA 리소스 이름을 기억하세요.

    Azure Portal에서 리소스 만들기를 완료하면 QnA Maker 포털로 돌아가서 브라우저 페이지를 새로 고치고 **2단계**를 계속 진행합니다.

1. **3단계**에서는 Active 디렉터리, 구독, 서비스(리소스) 및 서비스에서 만든 모든 기술 자료에 대한 언어를 선택합니다.

   ![QnA Maker 서비스 기술 자료를 선택하는 스크린샷](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. **3 단계**에서 기술 자료의 이름을로 `My Sample QnA KB` 합니다.

1. **4단계**에서는 다음 표를 사용하여 설정을 구성합니다.

    |설정|값|
    |--|--|
    |**URL, .pdf 또는 .docx 파일에서 다중 턴 추출을 사용하도록 설정합니다.**|선택|
    |**기본 답변 텍스트**| `Quickstart - default answer not found.`|
    |**+ URL 추가**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**잡담**|**Professional** 선택|

1. **5 단계**에서 **KB 만들기**를 선택 합니다.

    추출 프로세스는 문서를 읽고 질문과 답변을 확인하는 데 몇 분 정도가 걸립니다.

    QnA Maker가 기술 자료를 성공적으로 만들면 **기술 자료** 페이지가 열립니다. 이 페이지에서 기술 자료의 내용을 편집할 수 있습니다.

## <a name="edit-knowledge-base"></a>기술 자료 편집

1.  맨 위 탐색 모음에서 **내 기술 자료**를 선택합니다.

       직접 만들었거나 공유된 모든 서비스를 **마지막으로 수정한 날짜**의 내림차순으로 볼 수 있습니다.

       ![내 기술 자료](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. 특정 기술 자료를 선택하여 편집합니다.

1.  **설정**을 선택합니다. 다음 목록에는 변경할 수 있는 필드가 나와 있습니다.

       |목표|작업|
       |--|--|
       |URL 추가|**기술 자료 관리 -> '+ URL 추가'** 링크를 클릭하여 기술 자료에 새 FAQ 콘텐츠를 추가할 새 URL을 추가할 수 있습니다.|
       |URL 삭제|삭제 아이콘, 휴지통을 선택하여 기존 URL을 삭제할 수 있습니다.|
       |콘텐츠 새로 고침|기술 자료에서 기존 URL의 최신 콘텐츠를 탐색하도록 하려면 **새로 고침** 확인란을 선택합니다. 이렇게 하면 기술 자료가 최신 URL 내용으로 한 번 업데이트 됩니다. 이는 정기적인 업데이트 일정을 설정 하지 않습니다.|
       |파일 추가|**기술 자료 관리**를 선택한 다음, **+ 파일 추가**를 선택하여 기술 자료의 일부가 되도록 지원되는 파일 문서를 추가할 수 있습니다.|
    |가져오기|**기술 자료 가져오기** 단추를 선택 하 여 기존 기술 자료를 가져올 수도 있습니다. |
    |업데이트|기술 자료의 업데이트는 기술 자료와 관련된 QnA Maker 서비스를 만드는 동안 사용되는 **관리 가격 책정 계층**에 따라 달라집니다. 필요한 경우 Azure Portal에서 관리 계층을 업데이트할 수도 있습니다.

  1. 기술 자료를 변경한 후 변경 내용을 유지 하기 위해 페이지의 오른쪽 위 모서리에서 **저장 및 학습** 을 선택 합니다.

       ![저장 후 학습](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >**저장 후 학습**을 선택하기 전에 페이지를 나가면 모든 변경 내용이 손실됩니다.



## <a name="manage-large-knowledge-bases"></a>큰 기술 자료 관리

* **데이터 원본 그룹**: qnas는 추출 된 데이터 원본에 따라 그룹화 됩니다. 데이터 원본을 확장하거나 축소할 수 있습니다.

    ![QnA Maker 데이터 원본 막대를 사용하여 데이터 원본 질문과 답변을 축소 및 확장합니다.](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* 기술 자료 **검색**: 기술 자료 테이블의 맨 위에 있는 입력란에를 입력 하 여 기술 자료를 검색할 수 있습니다. 질문, 답변 또는 메타데이터 콘텐츠를 검색하려면 Enter 키를 클릭합니다. 검색 필터를 제거하려면 X 아이콘을 클릭합니다.

    ![질문과 답변 위의 QnA Maker 검색 상자를 사용하여 필터 일치 항목으로만 뷰를 줄입니다.](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **페이지 매김**: 데이터 원본을 빠르게 이동 하 여 방대한 기술 자료 관리

    ![질문과 답변 위의 QnA Maker 페이지 매김 기능을 사용하여 질문과 답변 페이지를 이동합니다.](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>기술 자료 삭제

KB(기술 자료)를 삭제하는 것은 영구 작업입니다. 실행을 취소할 수 없습니다. 기술 자료를 삭제하기 전에 QnA Maker 포털의 **설정** 페이지에서 기술 자료를 내보내야 합니다.

기술 자료를 공동 작업자와 공유 하는 경우] (base.md), 삭제 하면 모든 사용자가 KB에 액세스할 수 없게 됩니다.

## <a name="next-steps"></a>다음 단계

리소스에서 모든 기술 자료의 [언어를 관리 하는](language-knowledge-base.md) 방법에 대해 알아봅니다.

* QnA 쌍 편집
* QnA Maker에서 사용 하는 Azure 리소스 관리