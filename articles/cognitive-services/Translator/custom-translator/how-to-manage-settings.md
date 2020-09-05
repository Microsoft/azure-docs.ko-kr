---
title: 설정을 관리하는 방법 - Custom Translator
titleSuffix: Azure Cognitive Services
description: Custom Translator에서 설정을 관리하고, 작업 영역을 만들고, 작업 영역을 공유하며 구독 키를 관리하는 방법입니다.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 488ca52abdd5f8d6cfd1f92a5f91861a17a77d19
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510517"
---
# <a name="how-to-manage-settings"></a>설정을 관리하는 방법

사용자 지정 변환기 설정 페이지에서 작업 영역을 공유 하 고, Translator 구독 키를 수정 하 고, 작업 영역을 삭제할 수 있습니다.

설정 페이지에 액세스하려면

1. [Custom Translator](https://portal.customtranslator.azure.ai/) 포털에 로그인합니다.
2. Custom Translator 포털에서 사이드바에서 기어 아이콘을 클릭합니다.

    ![링크 설정](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>Translator 구독 연결

모델을 학습 하거나 배포 하려면 작업 영역과 연결 된 번역기 구독 키가 있어야 합니다.

구독이 없으면 다음 단계를 수행합니다.

1. Translator 리소스 만들기를 구독 합니다. 번역기 [에 등록](https://docs.microsoft.com/azure/cognitive-services/translator/translator-how-to-signup) 하 여 번역기 키를 구독 하 고 가져오는 방법을 따르세요.
2. Translator 구독의 키를 적어둡니다. Key1 또는 Key2가 허용됩니다.
3. Custom Translator 포털로 다시 이동합니다.

## <a name="create-a-new-workspace"></a>새 작업 영역 만들기

1. "사용자 지정 변환기" 사이드바에서 "+ 작업 영역 만들기" 단추를 클릭 합니다.

    ![새 작업 영역 만들기](media/how-to/create-new-workspace.png)

2. 대화 상자에서 새 작업 영역의 이름을 입력합니다.
3. "다음"을 클릭 합니다.
4. 구독 유형을 선택 합니다.
5. 구독 영역을 선택 합니다. 이 지역은 번역기 리소스 키가 생성 될 때 선택한 지역과 일치 해야 합니다.
6. 번역기 구독의 키를 입력 하 고 "저장" 단추를 클릭 합니다.

    ![새 작업 영역 만들기 대화 상자](media/how-to/create-new-workspace-dialog.png)


### <a name="modify-existing-key"></a>기존 키 수정

1. 작업 영역의 “설정” 페이지로 이동합니다.
2. 키 변경을 클릭 합니다.

    ![구독 키를 추가하는 방법](media/how-to/how-to-add-subscription-key.png)

3. 대화 상자에서 번역기 구독의 키를 입력 하 고 "저장" 단추를 클릭 합니다.

    ![구독 키를 추가하는 방법](media/how-to/how-to-add-subscription-key-dialog.png)

## <a name="manage-your-workspace"></a>작업 영역 관리

작업 영역이란 사용자 지정 번역 시스템을 구성하고 빌드하기 위한 작업 구축에 대 한 작업 공간입니다. 적업 영역은 여러 프로젝트, 모델 및 문서를 포함할 수 있습니다.

작업의 다른 부분을 다른 사람들과 공유해야 하는 경우 여러 작업 영역을 만드는 것이 유용할 수 있습니다.

## <a name="share-your-workspace"></a>작업 영역 공유

Custom Translator에서는 작업의 다른 부분을 다른 사람들과 공유해야 하는 경우 작업 영역을 다른 사용자와 공유할 수 있습니다.

1. 작업 영역 “설정” 페이지로 이동합니다.
2. "공유 설정" 섹션에서 "사용자 추가" 단추를 클릭 합니다.

    ![작업 영역 공유](media/how-to/share-workspace.png)

3. 대화 상자에서 이 작업 영역을 공유할 이메일 주소의 목록을 쉼표로 구분하여 입력합니다. Custom Translator에 로그인하는 데 사용하는 이메일 주소를 공유해야 합니다. 그런 다음 적절 한 공유 권한 수준을 선택 하 고 "저장" 단추를 클릭 합니다.

    ![작업 영역 공유 대화 상자](media/how-to/share-workspace-dialog.png)

4. 작업 영역에 여전히 기본 이름 “내 작업 영역”이 있는 경우 작업 영역을 공유하기 전에 변경해야 합니다.
5. “저장”을 클릭합니다.

## <a name="sharing-permissions"></a>공유 권한

1. **읽기 권한자:** 작업 영역의 읽기 권한자는 작업 영역에서 모든 정보를 볼 수 있게 됩니다.

2. **편집자:** 작업 영역의 편집자는 문서를 추가하고, 모델을 학습하고, 문서 및 프로젝트를 삭제할 수 있습니다. 구독 키를 추가할 수는 있지만 작업 영역을 공유하는 사람을 수정하거나 작업 영역을 삭제하거나 작업 영역 이름을 변경할 수는 없습니다.

3. **소유자:** 소유자는 작업 영역에 대한 모든 권한을 갖습니다.

## <a name="change-sharing-permission"></a>공유 권한 변경

작업 영역을 공유 하는 경우 "공유 설정" 섹션은이 작업 영역을 공유 하는 모든 전자 메일 주소를 표시 합니다. 작업 영역에 소유자 액세스 권한이 있는 사용자는 각 이메일 주소에 대한 기존 공유 권한을 변경할 수 있습니다.

1. 각 전자 메일에 대 한 "공유 설정" 섹션에서 드롭다운 메뉴에 현재 사용 권한 수준이 표시 됩니다.

2. 드롭다운 메뉴를 클릭하고 해당 이메일 주소에 할당하려는 새로운 권한 수준을 선택합니다.

    ![공유 권한 설정](media/how-to/sharing-permission-settings.png)

## <a name="pin-your-workspace"></a>작업 영역 고정

처음 만든 작업 영역은 기본적으로 고정 되어 있습니다. 로그인 할 때마다 고정 된 작업 영역이 사이트 로드에 표시 됩니다. 여러 작업 영역을 만들고 로그인 할 때 이러한 작업 영역 중 하나를 기본으로 설정 하려는 경우 고정 해야 합니다.

1. 사이드바에서 고정 하려는 작업 영역의 이름을 클릭 합니다.
2. 작업 영역에 대 한 "설정" 페이지로 이동 합니다.
3. 고정 아이콘을 클릭 합니다.

    ![작업 영역 고정](media/how-to/how-to-pin-workspace.png)

## <a name="next-steps"></a>다음 단계

- [작업 영역 및 프로젝트를 만드는 방법](workspace-and-project.md) 알아보기
