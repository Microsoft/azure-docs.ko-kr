---
title: X12 메시지 디코딩 - Azure Logic Apps | Microsoft Docs
description: 엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 X12 메시지 디코더를 사용하여 EDI 유효성 검사 및 승인 생성
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.date: 01/27/2017
ms.openlocfilehash: 4a19462f4f849602fd14fe1204f1c7e3c01e6ec4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106521"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 X12 메시지 디코딩

X12 메시지 디코드 커넥터를 사용하여 거래 업체 규약과 비교하여 봉투(Envelope)의 유효성을 검사하고, EDI 및 파트너별 속성의 유효성을 검사하고, 교환을 트랜잭션 집합으로 분할하거나 전체 교환을 유지하고, 처리된 트랜잭션에 대한 승인을 생성할 수 있습니다. 이 커넥터를 사용하려면 논리 앱에서 기존 트리거에 커넥터를 추가해야 합니다.

## <a name="before-you-start"></a>시작하기 전에

필요한 항목은 다음과 같습니다.

* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)
* [통합 계정](logic-apps-enterprise-integration-create-integration-account.md)이 이미 정의되고 Azure 구독과 연결되었습니다. X12 메시지 디코딩 커넥터를 사용하는 통합 계정이 있어야 합니다.
* 통합 계정에 이미 정의된 둘 이상의 [파트너](logic-apps-enterprise-integration-partners.md)
* 통합 계정에 이미 정의된 [X12 규약](logic-apps-enterprise-integration-x12.md)

## <a name="decode-x12-messages"></a>X12 디코딩 메시지

1. [논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)

2. X12 메시지 디코딩 커넥터에는 트리거가 없으므로 요청 트리거와 마찬가지로 논리 앱을 시작하는 트리거를 추가해야 합니다. Logic App Designer에서 트리거를 추가하고 작업을 논리 앱에 추가합니다.

3.  검색 상자에서 필터에 "x12"를 입력합니다. **X12 - X12 메시지 디코딩**을 선택합니다.
   
    !["x12" 검색](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. 이전에 통합 계정에 연결을 만들지 않은 경우 이제 해당 연결을 만들라는 메시지가 나타납니다. 연결의 이름을 지정하고 연결하려는 통합 계정을 선택합니다. 

    ![통합 계정 연결 세부 사항 제공](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    별표가 있는 속성은 필수 사항입니다.

    | 자산 | 세부 정보 |
    | --- | --- |
    | 연결 이름 * |연결의 이름을 입력합니다. |
    | 통합 계정 * |통합 계정의 이름을 입력합니다. 통합 계정 및 논리 앱이 동일한 Azure 위치에 있어야 합니다. |

5.  완료되면 연결 정보는 이 예제와 유사해야 합니다. 연결 만들기를 완료하려면 **만들기**를 선택합니다.
   
    ![통합 계정 연결 세부 사항](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. 이 예제와 같이 연결을 만든 후에 디코딩할 X12 플랫 파일 메시지를 선택합니다.

    ![통합 계정 연결 생성](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    예를 들면 다음과 같습니다.

    ![디코딩할 X12 플랫 파일 메시지를 선택합니다.](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > 실제 메시지 콘텐츠 또는 메시지 배열에 대한 페이로드(올바른 경우 또는 잘못된 경우) base64로 인코딩됩니다. 따라서 이 콘텐츠를 처리하는 식을 지정해야 합니다.
   > 콘텐츠를 코드 뷰에서 또는 디자이너의 식 작성기를 사용하여 입력할 수 있는 XML로 처리하는 예제는 다음과 같습니다.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![콘텐츠 예제](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 디코딩 세부 정보

X12 디코딩 커넥터는 다음과 같은 태스크를 수행합니다.

* 거래 업체 규약에 대한 봉투의 유효성 검사
* EDI 및 파트너 관련 속성의 유효성을 검사합니다.
  * EDI 구조 유효성 검사 및 확장된 스키마 유효성 검사
  * 교환 봉투 구조의 유효성 검사
  * 제어 스키마에 대한 봉투의 스키마 유효성 검사
  * 메시지 스키마에 대한 트랜잭션 집합 데이터 요소의 스키마 유효성 검사
  * 트랜잭션 집합 데이터 요소에서 수행한 EDI 유효성 검사 
* 교환, 그룹 및 트랜잭션 집합 제어 번호가 중복되지 않았는지 확인합니다.
  * 이전에 받은 교환에 대한 교환 제어 번호를 확인합니다.
  * 교환에 있는 다른 그룹 제어 번호에 대한 그룹 제어 번호를 확인합니다.
  * 해당 그룹의 다른 트랜잭션 집합 제어 번호에 대한 트랜잭션 집합 제어 번호를 확인합니다.
* 교환을 트랜잭션 집합으로 분할하거나 전체 교환을 유지합니다.
  * 교환을 트랜잭션 집합으로 분할 오류 발생 시 트랜잭션 집합 일시 중단 합니다. 트랜잭션으로 교환을 설정 하 고 각 트랜잭션 집합을 구문 분석 합니다. 
  X12 디코드 작업은 유효성 검사에 실패한 트랜잭션 집합만 `badMessages`에 출력하고, 나머지 트랜잭션 집합은 `goodMessages`에 출력합니다.
  * 교환을 트랜잭션 집합으로 분할 오류 발생 시 교환 일시 중단 합니다. 트랜잭션으로 교환을 설정 하 고 각 트랜잭션 집합을 구문 분석 합니다. 
  교환에 포함된 하나 이상의 트랜잭션 집합이 유효성 검사에 실패할 경우 X12 디코드 작업은 해당 교환에 포함된 모든 트랜잭션 집합을 `badMessages`에 출력합니다.
  * 교환 유지-오류 발생 시 트랜잭션 집합 일시 중단 합니다. 교환을 유지 하 고 전체 교환을 처리 합니다. 
  X12 디코드 작업은 유효성 검사에 실패한 트랜잭션 집합만 `badMessages`에 출력하고, 나머지 트랜잭션 집합은 `goodMessages`에 출력합니다.
  * 교환 유지-오류 시 교환 일시 중단 합니다. 교환을 유지 하 고 전체 교환을 처리 합니다. 
  교환에 포함된 하나 이상의 트랜잭션 집합이 유효성 검사에 실패할 경우 X12 디코드 작업은 해당 교환에 포함된 모든 트랜잭션 집합을 `badMessages`에 출력합니다. 
* 기술 및/또는 기능 승인을 생성합니다(구성된 경우).
  * 기술 승인은 헤더 유효성 검사의 결과로 생성됩니다. 기술 승인은 주소 수신기에 의한 교환 헤더 및 트레일러의 처리 상태를 보고합니다.
  * 기능 승인은 본문 유효성 검사의 결과로 생성됩니다. 기능 승인은 받은 문서를 처리하는 동안 발생한 오류를 각각 보고합니다.

## <a name="view-the-swagger"></a>swagger 보기
[swagger 정보](/connectors/x12/)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기") 

