---
title: Azure 대시보드에서 사용자 지정 markdown 타일 사용
description: Azure 대시보드에 markdown 타일을 추가하여 정적 콘텐츠를 표시하는 방법을 알아봅니다.
services: azure-portal
keywords: ''
author: kfollis
ms.author: v-biyu
origin.date: 01/25/2019
ms.date: 03/04/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ec8cbddda4137656a53fd4968c451cd413959274
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551587"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Azure 대시보드에서 markdown 타일을 사용하여 사용자 지정 콘텐츠 표시

Azure 대시보드에 markdown 타일을 추가하여 사용자 지정 정적 콘텐츠를 표시할 수 있습니다. 예를 들어 markdown 타일을 사용하여 기본 지침, 이미지 또는 하이퍼링크 세트를 표시할 수 있습니다.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>대시보드에 markdown 타일 추가

1. Azure Portal 사이드바에서 **대시보드**를 선택합니다. 사용자 지정 대시보드를 만든 경우 대시보드 보기에서 드롭다운을 사용하여 사용자 지정 markdown 타일이 표시되는 대시보드를 선택합니다. 편집 아이콘을 선택하여 **타일 갤러리**를 엽니다.

   ![대시보드 편집 보기를 보여 주는 스크린샷](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. **파일 갤러리**에서 **markdown**이라는 타일을 찾고 **추가**를 클릭합니다. 타일이 대시보드에 추가되고 **markdown 편집** 창이 열립니다.

3. **제목**, **부제목** 및 **콘텐츠** 필드를 편집하여 타일을 사용자 지정합니다. 여기에 표시된 예에서는 사용자 지정 지원 센터 정보를 표시하도록 markdown 타일이 편집되었습니다.

   ![markdown 타일 편집 보기를 표시하는 스크린샷](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. **완료**를 선택하여 **markdown 편집** 창을 해제합니다. 콘텐츠가 markdown 타일에 표시됩니다. 그러면 오른쪽 아래 모서리의 핸들을 끌어 크기를 조정할 수 있습니다.

   ![사용자 지정 markdown 타일을 보여 주는 스크린샷](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>markdown 콘텐츠 기능 및 제한 사항

markdown 타일에서는 일반 텍스트, markdown 구문 및 HTML 콘텐츠의 모든 조합을 사용할 수 있습니다. Azure Portal에서 _marked_라는 오픈 소스 라이브러리를 사용하여 콘텐츠를 타일에 표시되는 HTML로 변환합니다. _marked_에서 생성된 HTML은 렌더링되기 전에 포털에서 전처리됩니다. 이 단계는 사용자 지정이 포털의 보안 또는 레이아웃에 영향을 주지 않도록 하는 데 도움이 됩니다. 전처리 중에 잠재적인 위협이 되는 HTML 부분이 모두 제거됩니다. 포털에서 허용되지 않는 콘텐츠 형식은 다음과 같습니다.

* JavaScript – `<script>` 태그와 인라인 JavaScript 평가가 제거됩니다.
* iframes - `<iframe>` 태그가 제거됩니다.
* Style - `<style>` 태그가 제거됩니다. HTML 요소의 인라인 스타일 특성은 공식적으로 지원되지 않습니다. 일부 인라인 스타일 요소는 사용자에게 적합하지만 포털의 레이아웃에 방해가 되면 언제든지 작동을 중지할 수 있습니다. markdown 타일은 포털의 기본 스타일을 사용하는 기본 정적 콘텐츠를 위한 것입니다.

## <a name="next-steps"></a>다음 단계

* 사용자 지정 대시보드를 만들려면 [Azure Portal에서 대시보드 만들기 공유](../azure-portal/azure-portal-dashboards.md)를 참조하세요.
