---
title: Azure 대시보드에서 사용자 지정 markdown 타일 사용
description: Azure 대시보드에 markdown 타일을 추가하여 정적 콘텐츠를 표시하는 방법을 알아봅니다.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/08/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 74102423461a56bb6fc19c2eb9874f96a76e34e0
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310716"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Azure 대시보드에서 markdown 타일을 사용하여 사용자 지정 콘텐츠 표시

Azure 대시보드에 markdown 타일을 추가하여 사용자 지정 정적 콘텐츠를 표시할 수 있습니다. 예를 들어 기본 지침, 이미지 또는 markdown 타일에 대한 하이퍼링크 집합을 표시할 수 있습니다.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>대시보드에 markdown 타일 추가

1. Azure Portal 사이드바에서 **대시보드**를 선택합니다.

   ![포털 사이드바를 보여 주는 스크린샷](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. 사용자 지정 대시보드를 만든 경우 대시보드 보기에서 드롭다운을 사용하여 사용자 지정 markdown 타일이 표시되는 대시보드를 선택합니다. 편집 아이콘을 선택하여 **타일 갤러리**를 엽니다.

   ![대시보드 편집 보기를 보여 주는 스크린샷](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. **타일 갤러리**에서 **Markdown** 라는 타일을 찾은 다음 **추가**를 선택 합니다. 타일이 대시보드에 추가되고 **markdown 편집** 창이 열립니다.

1. **제목** 및 **부제목**에 대한 값을 입력 합니다 .이 값은 다른 필드로 이동한 후 타일에 표시 됩니다.

   ![제목 및 부제목 입력 결과를 보여 주는 스크린샷](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Markdown 내용 포함: **인라인 편집** 또는 **URL을 사용 하 여 콘텐츠 삽입**에 대한 옵션 중 하나를 선택 합니다.

   - Markdown를 직접 입력 하려면 **인라인 편집** 을 선택 합니다.

      ![인라인 콘텐츠 입력을 보여 주는 스크린샷](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - 온라인으로 호스팅된 기존 markdown 콘텐츠를 사용 하려는 경우 **URL을 사용 하 여 콘텐츠 삽입** 을 선택 합니다.

      ![URL 입력을 보여 주는 스크린샷](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > 보안을 강화 하기 위해 markdown 파일을 만들어 [암호화가 사용 되는 Azure storage 계정 blob](../storage/common/storage-service-encryption.md)에 저장 한 다음 URL 옵션을 사용 하 여 파일을 가리킬 수 있습니다. Markdown 콘텐츠는 저장소 계정의 암호화 옵션을 통해 암호화 됩니다. 파일에 대한 권한이 있는 사용자만 대시보드의 markdown 콘텐츠를 볼 수 있습니다.

1. **완료**를 선택하여 **markdown 편집** 창을 해제합니다. 콘텐츠는 오른쪽 아래 모서리에서 핸들을 끌어 크기를 조정할 수 있는 Markdown 타일에 표시 됩니다.

   ![사용자 지정 markdown 타일을 보여 주는 스크린샷](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>markdown 콘텐츠 기능 및 제한 사항

markdown 타일에서는 일반 텍스트, markdown 구문 및 HTML 콘텐츠의 모든 조합을 사용할 수 있습니다. Azure Portal에서 _marked_라는 오픈 소스 라이브러리를 사용하여 콘텐츠를 타일에 표시되는 HTML로 변환합니다. _marked_에서 생성된 HTML은 렌더링되기 전에 포털에서 전처리됩니다. 이 단계는 사용자 지정이 포털의 보안 또는 레이아웃에 영향을 주지 않도록 하는 데 도움이 됩니다. 전처리 중에 잠재적인 위협이 되는 HTML 부분이 모두 제거됩니다. 포털에서 허용되지 않는 콘텐츠 형식은 다음과 같습니다.

* JavaScript – `<script>` 태그와 인라인 JavaScript 평가가 제거됩니다.
* iframes - `<iframe>` 태그가 제거됩니다.
* Style - `<style>` 태그가 제거됩니다. HTML 요소의 인라인 스타일 특성은 공식적으로 지원되지 않습니다. 일부 인라인 스타일 요소는 사용자에게 적합하지만 포털의 레이아웃에 방해가 되면 언제든지 작동을 중지할 수 있습니다. markdown 타일은 포털의 기본 스타일을 사용하는 기본 정적 콘텐츠를 위한 것입니다.

## <a name="next-steps"></a>다음 단계

* 사용자 지정 대시보드를 만들려면 [Azure Portal에서 대시보드 만들기 공유](../azure-portal/azure-portal-dashboards.md)를 참조하세요.
