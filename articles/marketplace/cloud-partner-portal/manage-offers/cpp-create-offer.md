---
title: 마켓플레이스 제품 만들기 - Azure Marketplace | Microsoft Docs
description: Cloud 파트너 포털을 사용하여 Azure 및 AppSource Marketplace에서 제품 만들기
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pbutlerm
ms.openlocfilehash: af9b34d90098409135020fa8a45ecd0253f25b22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825718"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>Azure Marketplace 및 AppSource 제품 만들기

Cloud 파트너 포털의 필수 용도 중 하나는 게시자가 제품을 만들고 Microsoft Azure 및 AppSource Marketplace에 게시할 수 있도록 하는 것입니다.  이 작업은 항상 [새 제품 메뉴](../portal-tour/cpp-new-offer-menu.md)에서 원하는 제품 유형을 선택하는 것으로 시작됩니다.  해당 제품 유형에 적합한 **새 제품** 페이지가 응답으로 표시됩니다.  예를 들어 다음 이미지는 Azure 애플리케이션 유형에 대한 기본 **새 제품** 페이지를 보여 줍니다.

![새 제품 기본 페이지](./media/new-offer-page.png)

이 페이지의 위쪽에 표시되는 가로 메뉴 모음에서 다음 두 개의 탭을 선택할 수 있습니다. 
- **편집기** 탭 - 새 제품 인스턴스에 대한 정보를 입력하고 자산을 업로드할 수 있습니다.  이 탭은 기본적으로 표시됩니다.
- **상태** 탭 - 게시 상태를 제공하고 유효성 검사 및 검토 문제를 나열합니다. 

제품을 만들 때 **편집기** 탭을 사용하여 해당 제품에 대한 정보를 입력합니다. 

## <a name="editing-operations"></a>편집 작업

데이터 입력 영역 위에 있는 가로 도구 모음에는 다음 단추가 표시됩니다.

|   단추    |   목적                                                          |
|   ------    |  --------                                                          |
| **저장**    | 최근 데이터 입력 변경 내용을 저장합니다.  페이지를 나가기 전에 변경 내용을 수동으로 저장해야 합니다. 저장하지 않으면 변경 내용이 손실됩니다. | 
| **취소** | 최근 데이터 입력 변경 내용(마지막 저장 이후)을 취소합니다.             |
| **비교** | 현재 제품의 상태를 게시된 제품과 비교합니다.  제품이 성공적으로 게시된 후에만 사용할 수 있습니다.  |
| **게시** | 이 제품에 대한 게시 프로세스를 시작합니다.                       |
| **삭제**  | 이 제품이 생성된 후 게시되기 전에 제품을 삭제합니다. |
|   |   |


## <a name="editing-tabs"></a>편집 탭

제품을 만들 때 **새 제품** 페이지의 왼쪽 수직 열에 있는 각 탭에서 필수 및 선택적 데이터를 제공합니다.  데이터 수집을 위해 표준 사용자 인터페이스 컨트롤(예: 텍스트 상자, 드롭다운 메뉴, 확인란)이 표시됩니다.  특정 편집 탭 컬렉션은 제품 유형에 따라 달라지지만, 다음 표에는 몇 가지 공통 탭이 나와 있습니다.

|      탭 이름       |   목적                                                            |
|      --------       |   -------                                                            |
| **제품 설정**  | 제품 및 게시자 ID 정보를 수집합니다.                    |
| **SKU**            | 제품의 각 SKU(Stock Keeping Unit) 버전에 대한 기술 및 비즈니스 특성을 정의합니다. |
| **시험 사용**      | 이 선택적 기능을 지원하는 유형의 경우 제품에 대한 데모를 정의합니다.  자세한 내용은 [시험 사용이란?](../test-drive/what-is-test-drive.md)을 참조하세요.  |
| **마켓플레이스** 또는 **상점** | 마켓플레이스에 제품을 나열하는 데 사용되는 텍스트 문자열, 문서 및 이미지를 수집합니다. |
| **지원**         | 고객, 엔지니어링 및 온라인 지원을 위한 연락처 정보를 수집합니다.  |
|  |  |

유사한 이름이 지정된 탭의 콘텐츠가 제품 유형에 따라 다를 수 있습니다.  이러한 탭의 제품별 세부 정보는 각 제품 유형의 “제품 만들기” 섹션에서 제공됩니다.


## <a name="next-steps"></a>다음 단계

제품을 만들고 저장한 후 게시하기 전이나 후에 [해당 상태를 확인](./cpp-view-status-offer.md)할 수 있습니다.
