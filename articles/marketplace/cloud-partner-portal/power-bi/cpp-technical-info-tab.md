---
title: Power BI 앱 제안에 대한 기술 정보 - Azure Marketplace | Microsoft Docs
description: Microsoft AppSource Marketplace의 Power BI 앱 제안에 대한 기술 정보 필드를 구성합니다.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
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
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: ca77da897eed51c8d832cad7052c2144d6ada562
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725768"
---
# <a name="power-bi-apps-technical-info-tab"></a>Power BI 앱 기술 정보 탭

에 **새 제품** 페이지를 사용 합니다 **기술 정보** 및 제공 하기 위해 Power BI 설치 관리자 패키지 URL을 새 제품을 검사 해야 하는 다른 정보 탭.  초기 릴리스의 경우, 모든 Power BI 앱은 무료 이며 AppSource에서 다운로드할 수 있습니다. 이 때문에이 제품 유형에 대 한 재고 관리 단위 (Sku)를 정의할 수 없습니다.

![기술 정보 탭](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>기술 정보 필드 

에 **기술 정보** 탭에서 다음 표에서 설명 하는 필드를 완료 합니다. 필드 레이블에 끝에 별표 (*)는 필수 필드를 의미 합니다.

|        필드          |  설명                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **설치 관리자 URL**     | Power BI 앱을 게시 하 고 프로덕션으로 승격 하는 경우이 URL을 생성 합니다.  자세한 내용은 [Power BI에서 대시보드 및 보고서를 사용 하 여 앱을 게시](https://docs.microsoft.com/power-bi/service-create-distribute-apps)합니다.  |
|  **유효성 검사 지침**  |  원한다 면 Microsoft 유효성 검사 팀 구성, 연결 및 응용 프로그램을 테스트 하는 지침 (최대 3000 자)를 추가 합니다. 일반적인 구성 설정, 계정, 매개 변수 또는 테스트 데이터 연결 옵션을 사용할 수 있는 기타 정보를 포함 합니다. 이 정보는 유효성 검사 팀에만 표시 하 고 유효성 검사 목적 으로만 사용 됩니다.  |
| **이 앱은 Power BI 콘텐츠 팩으로 만들어집니까?** | 현재이 필드는 내부적 으로만 사용 됩니다. 기본 설정을 그대로 **No**합니다. 설정을 변경 하면 **예**, 게시 프로세스를 중지할 수 있습니다.  |  
|  |  |


## <a name="next-steps"></a>다음 단계

에 [상점 정보](./cpp-storefront-details-tab.md) 탭에서 앱에 대 한 마케팅 및 법률 정보를 제공 합니다.

