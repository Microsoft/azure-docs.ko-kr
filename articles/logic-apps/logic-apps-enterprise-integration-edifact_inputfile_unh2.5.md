---
title: EDIFACT 메시지의 UNH 2.5 세그먼트
description: 엔터프라이즈 통합 팩를 사용 하 여 Azure Logic Apps에서 UNH 2.5 세그먼트로 EDIFACT 메시지를 해결 합니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: c6d95cb938a341b94a32989e82c42d37956c0a80
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179847"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Azure Logic Apps에서 UNH2.5 세그먼트를 사용하여 EDIFACT 문서를 처리합니다.

EDIFACT 문서에 UNH 2.5 세그먼트가 있으면 세그먼트가 스키마 조회에 사용 됩니다. 예를 들어이 샘플 EDIFACT 메시지에서 UNH 필드는 `EAN008` 다음과 같습니다.

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

이 메시지를 처리 하려면 아래에 설명 된 단계를 따르세요.

1. 스키마를 업데이트 합니다.

1. 규약 설정을 확인 합니다.

## <a name="update-the-schema"></a>스키마 업데이트

메시지를 처리 하려면 UNH 2.5 루트 노드 이름이 있는 스키마를 배포 해야 합니다. 예를 들어 샘플 UNH 필드의 스키마 루트 이름은 `EFACT_D03B_ORDERS_EAN008` 입니다. `D03B_ORDERS`다른 UNH 2.5 세그먼트가 있는 각에 대해 개별 스키마를 배포 해야 합니다.

## <a name="add-schema-to-edifact-agreement"></a>EDIFACT 규약에 스키마 추가

### <a name="edifact-decode"></a>EDIFACT 디코딩

들어오는 메시지를 디코딩하려면 EDIFACT 규약의 수신 설정에서 스키마를 설정 합니다.

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 엽니다.

1. 통합 계정에 스키마를 추가 합니다.

1. EDIFACT 규약의 수신 설정에서 스키마를 구성 합니다.

1. EDIFACT 규약을 선택 하 고 **JSON으로 편집**을 선택 합니다. 수신 규약의 섹션에 UNH 2.5 값을 추가 합니다 `schemaReferences` .

   ![수신 규약에 UNH 2.5 추가](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT 인코딩

들어오는 메시지를 인코딩하려면 EDIFACT 규약 송신 설정에서 스키마를 구성 합니다.

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 엽니다.

1. 통합 계정에 스키마를 추가 합니다.

1. EDIFACT 규약의 송신 설정에서 스키마를 구성 합니다.

1. EDIFACT 규약을 선택 하 고 **JSON으로 편집**을 선택 합니다. 송신 규약 섹션에 UNH 2.5 값을 추가 합니다 `schemaReferences` .

   ![규약을 보내도록 UNH 2.5 추가](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>다음 단계

* [통합 계정 계약](../logic-apps/logic-apps-enterprise-integration-agreements.md) 에 대 한 자세한 정보
