---
title: X12 메시지 인코딩 - Azure Logic Apps | Microsoft Docs
description: 엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 X12 메시지 인코더를 사용하여 EDI 유효성을 검사하고 XML 디코딩 메시지 변환
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.date: 01/27/2017
ms.openlocfilehash: 871d6d2b2019372bd258f8909ed0feeeddac4af7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106513"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 X12 메시지 인코딩

X12 메시지 인코딩 커넥터를 사용하여 EDI 및 파트너 관련 속성의 유효성을 검사하고 XML로 인코딩된 메시지를 교환에서 EDI 트랜잭션 집합으로 변환하며 기술 승인, 기능 승인 또는 둘 다를 요청할 수 있습니다.
이 커넥터를 사용하려면 논리 앱에서 기존 트리거에 커넥터를 추가해야 합니다.

## <a name="before-you-start"></a>시작하기 전에

필요한 항목은 다음과 같습니다.

* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)
* [통합 계정](logic-apps-enterprise-integration-create-integration-account.md)이 이미 정의되고 Azure 구독과 연결되었습니다. X12 메시지 인코딩 커넥터를 사용하는 통합 계정이 있어야 합니다.
* 통합 계정에 이미 정의된 둘 이상의 [파트너](logic-apps-enterprise-integration-partners.md)
* 통합 계정에 이미 정의된 [X12 규약](logic-apps-enterprise-integration-x12.md)

## <a name="encode-x12-messages"></a>X12 메시지 인코딩

1. [논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)

2. X12 메시지 인코딩 커넥터에는 트리거가 없으므로 요청 트리거와 마찬가지로 논리 앱을 시작하는 트리거를 추가해야 합니다. Logic App Designer에서 트리거를 추가하고 작업을 논리 앱에 추가합니다.

3.  검색 상자에서 필터에 "x12"를 입력합니다. X12 - **규약 이름으로 X12 메시지 인코딩** 또는 **X12 - ID으로 X12 메시지 인코딩** 중 하나를 선택합니다.
   
    !["x12" 검색](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. 이전에 통합 계정에 연결을 만들지 않은 경우 이제 해당 연결을 만들라는 메시지가 나타납니다. 연결의 이름을 지정하고 연결하려는 통합 계정을 선택합니다. 
   
    ![통합 계정 연결](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    별표가 있는 속성은 필수 사항입니다.

    | 자산 | 세부 정보 |
    | --- | --- |
    | 연결 이름 * |연결의 이름을 입력합니다. |
    | 통합 계정 * |통합 계정의 이름을 입력합니다. 통합 계정 및 논리 앱이 동일한 Azure 위치에 있어야 합니다. |

5.  완료되면 연결 정보는 이 예제와 유사해야 합니다. 연결 만들기를 완료하려면 **만들기**를 선택합니다.

    ![통합 계정 연결 생성](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    새로운 연결이 만들어집니다.

    ![통합 계정 연결 세부 사항](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>규약 이름으로 X12 메시지 인코딩

규약 이름으로 X12 메시지를 인코딩하도록 선택한 경우 **X12 규약의 이름** 목록을 열고 기존 X12 규약을 입력 또는 선택합니다. 인코딩할 XML 메시지를 입력합니다.

![인코딩할 X12 규약 이름 및 XML 메시지를 입력합니다.](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>ID로 X12 메시지 인코딩

ID로 X12 메시지를 인코딩하도록 선택한 경우 보낸 사람 식별자, 보낸 사람 한정자, 받는 사람 식별자 및 받는 사람 한정자를 X12 규약에 구성된 대로 입력합니다. 인코딩할 XML 메시지를 선택합니다.
   
![보낸 사람 및 받는 사람에 대한 ID를 제공하고 인코딩할 XML 메시지를 선택합니다.](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 인코딩 세부 정보

X12 인코딩 커넥터는 다음과 같은 태스크를 수행합니다.

* 보낸 사람 및 받는 사람 컨텍스트 속성을 일치시켜 규약을 확인합니다.
* EDI 교환을 직렬화하며 XML로 인코딩된 메시지를 교환에서 EDI 트랜잭션 집합으로 변환합니다.
* 트랜잭션 집합 헤더 및 트레일러 세그먼트를 적용합니다.
* 교환 제어 번호, 그룹 제어 번호 및 나가는 교환 각각에 대한 트랜잭션 집합 제어 번호를 생성합니다.
* 페이로드 데이터에서 구분 기호를 바꿉니다.
* EDI 및 파트너 관련 속성의 유효성을 검사합니다.
  * 메시지 스키마에 대한 트랜잭션 집합 데이터 요소의 스키마 유효성 검사
  * 트랜잭션 집합 데이터 요소에서 수행한 EDI 유효성 검사
  * 트랜잭션 집합 데이터 요소에서 수행한 확장된 유효성 검사
* 기술 및/또는 기능 승인을 요청합니다(구성된 경우).
  * 기술 승인은 헤더 유효성 검사의 결과로 생성됩니다. 기술 승인은 주소 수신기에 의한 교환 헤더 및 트레일러의 처리 상태를 보고합니다.
  * 기능 승인은 본문 유효성 검사의 결과로 생성됩니다. 기능 승인은 받은 문서를 처리하는 동안 발생한 오류를 각각 보고합니다.

## <a name="view-the-swagger"></a>swagger 보기
[swagger 정보](/connectors/x12/)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기") 

