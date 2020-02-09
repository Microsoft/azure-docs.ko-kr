---
title: 검토 도구를 통해 콘텐츠 검토를 사용 Content Moderator
titleSuffix: Azure Cognitive Services
description: 웹 포털에서 검토 도구를 통해 중재자가 이미지를 검토하도록 하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: b02324923e3f004395105b8e04165390cb950fe7
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044124"
---
# <a name="create-human-reviews"></a>사용자 리뷰 만들기

이 가이드에서는 검토 도구 웹 사이트에서 [리뷰](../review-api.md#reviews) 를 설정 하는 방법을 알아봅니다. 평가를 위해 인적 중재자의 콘텐츠를 저장 하 고 표시 합니다. 중재자는 적용 된 태그를 변경 하 고 적절 한 사용자 지정 태그를 적용할 수 있습니다. 사용자가 검토를 완료 하면 결과가 지정 된 콜백 엔드포인트으로 전송 되 고 사이트에서 콘텐츠가 제거 됩니다.

## <a name="prerequisites"></a>전제 조건

- Content Moderator [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트에서 로그인 하거나 계정을 만드세요.

## <a name="image-reviews"></a>이미지 검토

1. [검토 도구로](https://contentmoderator.cognitive.microsoft.com/)이동 하 여 **시도** 탭을 선택 하 고 검토할 이미지를 업로드 합니다.
1. 업로드 된 이미지의 처리가 완료 되 면 **검토** 탭으로 이동 하 여 **이미지**를 선택 합니다.

    ![검토 이미지 옵션이 강조 표시된 검토 도구를 보여주는 Chrome 브라우저](images/review-images-1.png)

    자동 중재 프로세스에서 할당 된 레이블과 함께 이미지가 표시 됩니다. 검토 도구를 통해 제출한 이미지는 다른 검토자에 게 표시 되지 않습니다.

1. 필요에 따라 화면에 표시 되는 이미지 수를 조정 하려면 슬라이더 (1)를 **표시 하도록 리뷰** 를 이동 합니다. **태그가 지정** 되거나 **태그가** 지정 되지 않은 단추 (2)를 클릭 하 여 이미지를 적절 하 게 정렬 합니다. 태그 패널 (3)을 클릭 하 여 설정 하거나 해제 합니다.

    ![검토에 대한 태그가 지정된 이미지를 사용하는 검토 도구를 보여주는 Chrome 브라우저](images/review-images-2.png)

1. 이미지에 대 한 자세한 내용을 보려면 축소판 그림에서 줄임표를 클릭 하 고 **세부 정보 보기**를 선택 합니다. **이동** 옵션을 사용 하 여 이미지를 만듦에 할당할 수 있습니다. 하위 팀에 대해 자세히 알아보려면 [팀](./configure.md#manage-team-and-subteams) 섹션을 참조 하세요.

    ![보기 세부 정보 옵션이 강조 표시된 이미지](images/review-images-3.png)

1. 세부 정보 페이지에서 이미지 조정 정보를 찾아봅니다.

    ![별도 창에서 나열된 조정 세부 정보를 사용하는 이미지](images/review-images-4.png)

1. 태그 할당을 검토하여 필요에 따라 업데이트한 후에는 **다음**을 클릭하여 검토를 제출합니다. 제출 후에는 약 5초 이내에 **이전** 단추를 클릭하여 이전 화면으로 돌아가 이미지를 다시 검토합니다. 그 후에는 이미지가 제출 큐에 더 이상 없으며 **이전** 단추를 더 이상 사용할 수 없습니다.

## <a name="text-reviews"></a>텍스트 검토

텍스트 검토는 이미지 검토와 유사 하 게 작동 합니다. 콘텐츠를 업로드 하는 대신 텍스트 (최대 1024 자)로 작성 하거나 붙여 넣습니다. 그런 다음 Content Moderator 텍스트를 분석 하 고 태그를 적용 합니다 (예: 불경 및 개인 데이터와 같은 기타 중재 정보 외). 텍스트 검토에서 검토를 제출 하기 전에 적용 된 태그를 설정/해제 하거나 사용자 지정 태그를 적용할 수 있습니다.

![Chrome 브라우저 창에서 플래그가 지정된 텍스트를 보여주는 검토 도구의 스크린샷](../images/reviewresults_text.png)

## <a name="next-steps"></a>다음 단계

이 가이드에서는 Content Moderator [검토 도구](https://contentmoderator.cognitive.microsoft.com)에서 검토를 설정 하 고 사용 하는 방법을 알아보았습니다. 다음으로, 검토를 프로그래밍 방식으로 만드는 방법을 알아보려면 [REST API 가이드](../try-review-api-review.md) 또는 [.net SDK 퀵 스타트](../dotnet-sdk-quickstart.md) 를 참조 하세요.