---
title: UNH 2.5 세그먼트를 사용하여 EDIFACT 메시지 처리 - Azure Logic Apps | Microsoft Docs
description: 엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 UNH2.5 세그먼트를 사용하여 EDIFACT 문서를 확인합니다.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/27/2017
ms.openlocfilehash: 926c9ebe8675d8b50d4544be813ae0b15492ae35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681675"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Azure Logic Apps에서 UNH2.5 세그먼트를 사용하여 EDIFACT 문서를 처리합니다.

EDIFACT 문서에 UNH2.5가 표시되면 스키마 조회에 사용되고 있는 것입니다. 

예제: UNH 필드는 **EAN008** EDIFACT 메시지에  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

메시지를 처리하기 위해 수행하는 단계 
1. 스키마 업데이트
2. 규약 설정 확인  

## <a name="update-the-schema"></a>스키마 업데이트
메시지를 처리하려면 UNH2.5 루트 노드 이름을 갖는 스키마를 배포해야 합니다.  예제에서 스키마 루트 이름은 **EFACT_D03B_ORDERS_EAN008**입니다.  

다른 UNH2.5 세그먼트를 갖는 각 D03B_ORDERS에 대해 개별 스키마를 배포해야 합니다.  

## <a name="add-schema-to-the-edifact-agreement"></a>EDIFACT 규약에 스키마 추가
### <a name="edifact-decode"></a>EDIFACT 디코딩
들어오는 메시지를 디코딩하려면 EDIFACT 규약 수신 설정에서 스키마를 구성합니다.
1. 통합 계정에 스키마를 추가합니다.    
2. EDIFACT 규약 수신 설정에서 스키마를 구성합니다. 
3. EDIFACT 규약을 선택하고 **JSON으로 편집**을 클릭합니다.  수신 규약 **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)에 UNH2.5 값을 추가합니다.

### <a name="edifact-encode"></a>EDIFACT 인코딩
들어오는 메시지를 인코딩하려면 EDIFACT 규약 송신 설정에서 스키마를 구성합니다.
1. 통합 계정에 스키마를 추가합니다.    
2. EDIFACT 규약 송신 설정에서 스키마를 구성합니다. 
3. EDIFACT 규약을 선택하고 **JSON으로 편집**을 클릭합니다.  송신 규약 **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)에 UNH2.5 값을 추가합니다.

## <a name="next-steps"></a>다음 단계
* [통합 계정 규약에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-agreements.md "엔터프라이즈 통합 규약에 대해 알아보기")  