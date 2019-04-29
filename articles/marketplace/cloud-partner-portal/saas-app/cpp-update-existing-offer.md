---
title: 기존 Azure SaaS 애플리케이션 제품 업데이트 | Microsoft Docs
description: Azure Marketplace에서 기존 SaaS 애플리케이션 제품을 업데이트하는 방법입니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 9d490a0ec09e351e4cee00e7c30c9161bdfac3e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594266"
---
# <a name="update-an-existing-saas-application-offer"></a>기존 SaaS 애플리케이션 제품 업데이트

제품이 게시되고 라이브 상태가 된 후 제품을 대상으로 다양한 종류의 업데이트를 수행할 수 있습니다. 새 버전의 제품에 적용할 변경 내용을 저장하고 Marketplace에 반영되도록 다시 게시해야 합니다. 이 문서에서는 [Cloud 파트너 포털](https://cloudpartner.azure.com/)에서 SaaS 제품을 업데이트하는 다양한 측면을 단계별로 안내합니다.

다음과 같이 제품을 업데이트하는 여러 가지 이유가 있습니다.

- 기존 앱에 새 버전 추가
- 앱 업데이트
- 앱에 새로운 기능 추가
- 제품에 대한 마켓플레이스 메타데이터 업데이트

이러한 수정 작업을 지원하기 위해 포털에서 **비교** 및 **기록** 기능을 제공합니다.

## <a name="unpermitted-changes-to-a-saas-offer"></a>SaaS 제품에 대해 허용되지 않는 변경

제품이 Azure Marketplace에서 라이브 상태가 되면 변경할 수 없는 SaaS 제품의 특성이 있습니다. 다음 설정을 변경할 수 없습니다.

- 해당 제품의 제품 ID 및 게시자 ID
- 버전 태그(예: `1.0.1`)
- 기존 제품에 대한 청구/라이선스 모델 변경

## <a name="common-update-operations"></a>일반적인 업데이트 작업
 
다음 업데이트 작업이 빈번하게 이루어집니다.

### <a name="update-offer-contacts"></a>제품 연락처 업데이트

제품에 대한 지원 연락처를 업데이트하려면 다음 단계를 사용합니다.

1. [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.
2. **모든 제품** 아래에서 업데이트할 제품을 찾습니다.
3. **연락처** 탭으로 이동합니다. 연락처를 업데이트합니다.
4. **게시**를 선택하여 변경 내용을 게시하는 워크플로를 시작합니다.


### <a name="update-offer-marketplace-metadata"></a>제품 마켓플레이스 메타데이터 업데이트

다음 단계를 사용하여 제품과 연결된 마켓플레이스 메타데이터를 업데이트합니다. (예: 회사 이름, 로고 등)

1. [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.
2. **모든 제품** 아래에서 업데이트할 제품을 찾습니다.
3. **상점 세부 정보** 탭으로 이동합니다. [SaaS 제품 게시](./cpp-publish-offer.md) 문서의 지침을 사용하여 메타데이터를 변경합니다.
4. **게시**를 선택하여 변경 내용을 게시하는 워크플로를 시작합니다.

## <a name="compare-feature"></a>기능 비교

게시된 제품을 변경하는 경우 비교 기능을 사용하여 수행한 변경을 감사할 수 있습니다. 다음 화면 캡처는 게시된 제품에 대한 비교 옵션을 보여 줍니다.

![비교를 사용하여 Cloud 파트너 포털의 제품 변경 내용을 확인할 수 있습니다.](./media/saas-offer-compare.png)

### <a name="to-use-the-compare-feature"></a>비교 기능을 사용하려면:

1. 편집 프로세스 중에 언제든지 제품에 대해 비교를 선택합니다.
2. 마케팅 자산 및 메타데이터 버전을 함께 비교합니다.

## <a name="publishing-history"></a>게시 기록

이전의 모든 게시 작업을 보려면 Cloud 파트너 포털의 왼쪽 탐색 메뉴 모음에서 **기록** 탭을 선택합니다. Azure Marketplace 제품의 수명 동안 수행된 타임스탬프 지정 작업을 확인할 수 있습니다.

![Cloud 파트너 포털의 제품 기록 보기](./media/saas-offer-history.png)

감사 기록 페이지를 사용하여 특정 제품을 검색하고 게시자, 제품 및 이벤트 유형(예: OfferGoLiveRequested)과 같은 필터를 적용할 수 있습니다. 감사 기록을 csv 파일로 다운로드할 수도 있습니다.


## <a name="next-steps"></a>다음 단계

[SaaS 애플리케이션 제품](./cpp-saas-offer.md)