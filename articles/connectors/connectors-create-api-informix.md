---
title: IBM Informix 데이어베이스에 연결 - Azure Logic Apps | Microsoft Docs
description: IBM Informix REST API 및 Azure Logic Apps로 리소스 관리
author: gplarsen
manager: jeconnoc
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 6004c02f190bbfcf374b3b5d2a5c478f0e52c961
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691011"
---
# <a name="get-started-with-the-informix-connector"></a>Informix 커넥터 시작
Informix용 Microsoft 커넥터는 Logic Apps를 IBM Informix 데이터베이스에 저장된 리소스에 연결합니다. Informix 커넥터는 TCP/IP 네트워크를 통해 원격 Informix 서버 컴퓨터와 통신하는 Microsoft 클라이언트를 포함합니다. Azure 가상화에서 실행되는 Windows용 IBM Informix와 같은 클라우드 데이터베이스, 온-프레미스 데이터 게이트웨이를 사용하는 온-프레미스 데이터베이스를 포함합니다. IBM Informix 플랫폼 및 버전의 [지원되는](connectors-create-api-informix.md#supported-informix-platforms-and-versions) 목록을 참조하세요(이 항목에서).

커넥터는 다음 데이터베이스 작업을 지원합니다.

* 데이터베이스 테이블 목록 표시
* SELECT를 사용하여 단일 행 읽기
* SELECT를 사용하여 전체 행 읽기
* INSERT를 사용하여 단일 행 추가
* UPDATE를 사용하여 단일 행 변경
* DELETE를 사용하여 단일 행 삭제

이 항목에서는 논리 앱에서 커넥터를 사용하여 데이터베이스 작업을 처리하는 방법을 보여 줍니다.

Logic Apps에 대해 자세히 알아보려면 [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="available-actions"></a>사용 가능한 작업
이 커넥터에서 지원하는 논리 앱 작업은 다음과 같습니다.

* Getables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>테이블 나열
작업에 대한 논리 앱 만들기는 Microsoft Azure 포털을 통해 수행되는 여러 단계들로 구성됩니다.

논리 앱 내에서 테이블을 나열 하는 Informix 데이터베이스에서 작업을 추가할 수 있습니다. 이 작업에는 커넥터와 같은 Informix 스키마 문을 처리를 지시 `CALL SYSIBM.SQLTABLES`합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
1. **Azure 시작 보드**에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. **이름**(예: `InformixgetTables`), **구독**, **리소스 그룹**, **위치** 및 **App Service 계획**을 입력합니다. **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

### <a name="add-a-trigger-and-action"></a>트리거 및 작업 추가
1. **Logic Apps 디자이너**에서 **템플릿** 목록의 **빈 LogicApp**을 선택합니다.
2. **트리거** 목록에서 **되풀이**를 선택합니다. 
3. **되풀이** 트리거에서 **편집**, **빈도** 드롭다운 목록을 차례로 선택하여 **일**을 선택한 다음 **간격**을 선택하여 **7**을 입력합니다.  
4. **+ 새 단계** 상자를 선택한 다음 **작업 추가**를 선택합니다.
5. **작업** 목록의 **추가 작업 검색** 편집 상자에 **informix**를 입력한 다음 **Informix - 테이블 가져오기(미리 보기)** 를 선택합니다.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. **Informix - 테이블 가져오기** 구성 창에서 **온-프레미스 데이터 게이트웨이를 통해 연결**을 사용할 수 있게 하는 **확인란**을 선택합니다. 설정이 클라우드에서 온-프레미스로 변경되었는지 확인합니다.
   
   * **서버**값으로 주소 또는 별칭:(콜론)포트 번호 형식의 값을 입력합니다. 예를 들어 `ibmserver01:9089`을(를) 입력합니다.
   * **데이터베이스**에 대한 값을 입력합니다. 예를 들어 `nwind`을(를) 입력합니다.
   * **인증**값을 선택합니다. 예를 들어 **기본**을 선택합니다.
   * **사용자 이름**에 대한 값을 입력합니다. 예를 들어 `informix`을(를) 입력합니다.
   * **암호**에 대한 값을 입력합니다. 예를 들어 `Password1`을(를) 입력합니다.
   * **게이트웨이**에 대한 값을 선택합니다. 예를 들어 **datagateway01**을 선택합니다.
7. **만들기**를 선택한 다음 **저장**을 선택합니다. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. **InformixgetTables** 블레이드에서 **요약** 아래의 **모두 실행** 목록에 나열된 첫 번째 항목(가장 최근 실행)을 선택합니다.
9. **논리 앱 실행** 블레이드에서 **실행 정보**를 선택합니다. **작업** 목록에서 **Get_tables**를 선택합니다. **상태** 값을 확인하는데, 이 값은 **Succeeded**여야 합니다. **입력 링크**를 선택하여 입력을 확인합니다. **출력 링크**를 선택하고 출력을 확인합니다. 테이블 목록이 포함되어 있어야 합니다.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>연결 만들기
이 커넥터는 다음 연결 속성을 사용하는 온-프레미스와 클라우드에서 데이터베이스에 대한 연결을 지원합니다. 

| 자산 | 설명 |
| --- | --- |
| 서버 |필수 사항입니다. 콜론으로 구분된 TCP/IP 포트 번호가 뒤에 붙는 IPv4 또는 IPv6 형식 중 하나로 구성되어 TCP/IP 주소 또는 별칭을 나타내는 문자열 값이 허용됩니다. |
| 데이터베이스 |필수 사항입니다. DRDA 관계형 데이터베이스 이름(RDBNAM)을 나타내는 문자열 값이 허용됩니다. IBM Informix 데이터베이스 이름(dbname)으로 인식되는 데이터베이스인 Informix에는 128바이트 문자열이 허용됩니다. |
| 인증 |선택 사항입니다. 목록 항목 값, 즉 기본 또는 Windows (kerberos)가 허용됩니다. |
| 사용자 이름 |필수 사항입니다. 문자열 값을 허용합니다. |
| 암호 |필수 사항입니다. 문자열 값을 허용합니다. |
| 게이트웨이 |필수 사항입니다. 저장소 그룹의 Logic Apps에 정의된 온-프레미스 데이터 게이트웨이를 나타내는 목록 항목 값이 허용됩니다. |

## <a name="create-the-on-premises-gateway-connection"></a>온-프레미스 게이트웨이 연결 만들기
이 커넥터는 온-프레미스 데이터 게이트웨이를 통해 온-프레미스 Informix 데이터베이스에 액세스할 수 있습니다. 자세한 내용은 게이트웨이 항목을 참조하세요. 

1. **게이트웨이** 구성 창에서 **게이트웨이를 통해 연결**을 사용할 수 있게 하는 **확인란**을 선택합니다. 설정이 클라우드에서 온-프레미스로 변경되었는지 확인합니다.
2. **서버**값으로 주소 또는 별칭:(콜론)포트 번호 형식의 값을 입력합니다. 예를 들어 `ibmserver01:9089`을(를) 입력합니다.
3. **데이터베이스**에 대한 값을 입력합니다. 예를 들어 `nwind`을(를) 입력합니다.
4. **인증**값을 선택합니다. 예를 들어 **기본**을 선택합니다.
5. **사용자 이름**에 대한 값을 입력합니다. 예를 들어 `informix`을(를) 입력합니다.
6. **암호**에 대한 값을 입력합니다. 예를 들어 `Password1`을(를) 입력합니다.
7. **게이트웨이**에 대한 값을 선택합니다. 예를 들어 **datagateway01**을 선택합니다.
8. **만들기** 를 선택하여 계속합니다. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>클라우드 연결 만들기
이 커넥터는 클라우드 Informix 데이터베이스에 액세스할 수 있습니다. 

1. **게이트웨이** 구성 창에서 **게이트웨이를 통해 연결** **확인란**을 비워둡니다. 
2. **연결 이름**에 대한 값을 입력합니다. 예를 들어 `hisdemo2`을(를) 입력합니다.
3. **Informix 서버 이름**값으로 주소 또는 별칭:(콜론)포트 번호 형식의 값을 입력합니다. 예를 들어 `hisdemo2.cloudapp.net:9089`을(를) 입력합니다.
4. **Informix 데이터베이스 이름**값을 입력합니다. 예를 들어 `nwind`을(를) 입력합니다.
5. **사용자 이름**에 대한 값을 입력합니다. 예를 들어 `informix`을(를) 입력합니다.
6. **암호**에 대한 값을 입력합니다. 예를 들어 `Password1`을(를) 입력합니다.
7. **만들기** 를 선택하여 계속합니다. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>SELECT를 사용하여 전체 행 가져오기
Informix 테이블의 모든 행을 가져오는 논리 앱 작업을 만들 수 있습니다. 이 작업은 커넥터가 `SELECT * FROM AREA`와(과) 같은 Informix SELECT 문을 처리하도록 지시합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
1. **Azure 시작 보드**에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. **이름**(예: "**InformixgetRows**"), **구독**, **리소스 그룹**, **위치** 및 **App Service 계획**을 입력합니다. **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

### <a name="add-a-trigger-and-action"></a>트리거 및 작업 추가
1. **Logic Apps 디자이너**에서 **템플릿** 목록의 **빈 LogicApp**을 선택합니다.
2. **트리거** 목록에서 **되풀이**를 선택합니다. 
3. **되풀이** 트리거에서 **편집**, **빈도** 드롭다운 목록을 차례로 선택하여 **일**을 선택한 다음 **간격**을 선택하여 **7**을 입력합니다. 
4. **+ 새 단계** 상자를 선택한 다음 **작업 추가**를 선택합니다.
5. **작업** 목록의 **추가 작업 검색** 편집 상자에 **informix**를 입력한 다음 **Informix - 행 가져오기(미리 보기)** 를 선택합니다.
6. **행 가져오기(미리 보기)** 작업에서 **연결 변경**을 선택합니다.
7. **연결** 구성 창에서 **새로 만들기**를 선택합니다. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. **게이트웨이** 구성 창에서 **게이트웨이를 통해 연결** **확인란**을 비워둡니다.
   
   * **연결 이름**에 대한 값을 입력합니다. 예를 들어 `HISDEMO2`을(를) 입력합니다.
   * **Informix 서버 이름**값으로 주소 또는 별칭:(콜론)포트 번호 형식의 값을 입력합니다. 예를 들어 `HISDEMO2.cloudapp.net:9089`을(를) 입력합니다.
   * **Informix 데이터베이스 이름**값을 입력합니다. 예를 들어 `NWIND`을(를) 입력합니다.
   * **사용자 이름**에 대한 값을 입력합니다. 예를 들어 `informix`을(를) 입력합니다.
   * **암호**에 대한 값을 입력합니다. 예를 들어 `Password1`을(를) 입력합니다.
9. **만들기** 를 선택하여 계속합니다.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. **테이블 이름** 목록에서 **아래쪽 화살표**, **AREA**를 차례로 선택합니다.
11. 필요에 따라 **고급 옵션 표시** 를 선택하여 쿼리 옵션을 지정합니다.
12. **저장**을 선택합니다. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. **InformixgetRows** 블레이드에서 **요약** 아래의 **모두 실행** 목록에 나열된 첫 번째 항목(가장 최근 실행)을 선택합니다.
14. **논리 앱 실행** 블레이드에서 **실행 정보**를 선택합니다. **작업** 목록에서 **Get_rows**를 선택합니다. **상태** 값을 확인하는데, 이 값은 **Succeeded**여야 합니다. **입력 링크**를 선택하여 입력을 확인합니다. **출력 링크**를 선택하고 출력을 확인합니다. 행 목록이 포함되어 있어야 합니다.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>INSERT를 사용하여 단일 행 추가
Informix 테이블에서 한 행을 추가 하는 논리 앱 작업을 만들 수 있습니다. 이 작업에는 같은 Informix INSERT 문을 처리 하는 데 커넥터 지시 `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
1. **Azure 시작 보드**에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. **이름**(예: `InformixinsertRow`), **구독**, **리소스 그룹**, **위치** 및 **App Service 계획**을 입력합니다. **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

### <a name="add-a-trigger-and-action"></a>트리거 및 작업 추가
1. **Logic Apps 디자이너**에서 **템플릿** 목록의 **빈 LogicApp**을 선택합니다.
2. **트리거** 목록에서 **되풀이**를 선택합니다. 
3. **되풀이** 트리거에서 **편집**, **빈도** 드롭다운 목록을 차례로 선택하여 **일**을 선택한 다음 **간격**을 선택하여 **7**을 입력합니다. 
4. **+ 새 단계** 상자를 선택한 다음 **작업 추가**를 선택합니다.
5. **작업** 목록의 **추가 작업 검색** 편집 상자에 **informix**를 입력한 다음 **Informix - 행 삽입(미리 보기)** 을 선택합니다.
6. **행 가져오기(미리 보기)** 작업에서 **연결 변경**을 선택합니다. 
7. **연결** 구성 창에서 연결을 선택하려면 선택합니다. 예를 들어 **hisdemo2**를 선택합니다.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. **테이블 이름** 목록에서 **아래쪽 화살표**, **AREA**를 차례로 선택합니다.
9. 모든 필수 열(빨간색 별표 참조) 값을 입력합니다. 예를 들어 `Area 99999` 유형의 **AREAID**에는 `99999`을(를) 입력하고 **REGIONID**에는 `102`을(를) 입력합니다. 
10. **저장**을 선택합니다.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. **InformixinsertRow** 블레이드에서 **요약** 아래의 **모두 실행** 목록에 나열된 첫 번째 항목(가장 최근 실행)을 선택합니다.
12. **논리 앱 실행** 블레이드에서 **실행 정보**를 선택합니다. **작업** 목록에서 **Get_rows**를 선택합니다. **상태** 값을 확인하는데, 이 값은 **Succeeded**여야 합니다. **입력 링크**를 선택하여 입력을 확인합니다. **출력 링크**를 선택하고 출력을 확인합니다. 새 행이 포함되어 있어야 합니다.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>SELECT를 사용하여 단일 행 가져오기
Informix 테이블에서 한 행을 인출 하는 논리 앱 작업을 만들 수 있습니다. 이 그러면 커넥터와 같은 Informix SELECT WHERE 문을 처리 하도록 지시 `SELECT FROM AREA WHERE AREAID = '99999'`합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
1. **Azure 시작 보드**에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. **이름**(예: `InformixgetRow`), **구독**, **리소스 그룹**, **위치** 및 **App Service 계획**을 입력합니다. **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

### <a name="add-a-trigger-and-action"></a>트리거 및 작업 추가
1. **Logic Apps 디자이너**에서 **템플릿** 목록의 **빈 LogicApp**을 선택합니다.
2. **트리거** 목록에서 **되풀이**를 선택합니다. 
3. **되풀이** 트리거에서 **편집**, **빈도** 드롭다운 목록을 차례로 선택하여 **일**을 선택한 다음 **간격**을 선택하여 **7**을 입력합니다. 
4. **+ 새 단계** 상자를 선택한 다음 **작업 추가**를 선택합니다.
5. **작업** 목록의 **추가 작업 검색** 편집 상자에 **informix**를 입력한 다음 **Informix - 행 가져오기(미리 보기)** 를 선택합니다.
6. **행 가져오기(미리 보기)** 작업에서 **연결 변경**을 선택합니다. 
7. **연결** 구성 창에서 기존 연결을 선택하려면 선택합니다. 예를 들어 **hisdemo2**를 선택합니다.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. **테이블 이름** 목록에서 **아래쪽 화살표**, **AREA**를 차례로 선택합니다.
9. 모든 필수 열(빨간색 별표 참조) 값을 입력합니다. 예를 들어 **AREAID**에는 `99999`을(를) 입력합니다. 
10. 필요에 따라 **고급 옵션 표시** 를 선택하여 쿼리 옵션을 지정합니다.
11. **저장**을 선택합니다. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. **InformixgetRow** 블레이드에서 **요약** 아래의 **모두 실행** 목록에 나열된 첫 번째 항목(가장 최근 실행)을 선택합니다.
13. **논리 앱 실행** 블레이드에서 **실행 정보**를 선택합니다. **작업** 목록에서 **Get_rows**를 선택합니다. **상태** 값을 확인하는데, 이 값은 **Succeeded**여야 합니다. **입력 링크**를 선택하여 입력을 확인합니다. **출력 링크**를 선택하고 출력을 확인합니다. 행이 포함되어 있어야 합니다.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>UPDATE를 사용하여 단일 행 변경
Informix 테이블에서 하나의 행을 변경 하는 논리 앱 작업을 만들 수 있습니다. 이 그러면 커넥터와 같은 Informix UPDATE 문을 처리를 지시 `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
1. **Azure 시작 보드**에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. **이름**(예: `InformixupdateRow`), **구독**, **리소스 그룹**, **위치** 및 **App Service 계획**을 입력합니다. **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

### <a name="add-a-trigger-and-action"></a>트리거 및 작업 추가
1. **Logic Apps 디자이너**에서 **템플릿** 목록의 **빈 LogicApp**을 선택합니다.
2. **트리거** 목록에서 **되풀이**를 선택합니다. 
3. **되풀이** 트리거에서 **편집**, **빈도** 드롭다운 목록을 차례로 선택하여 **일**을 선택한 다음 **간격**을 선택하여 **7**을 입력합니다. 
4. **+ 새 단계** 상자를 선택한 다음 **작업 추가**를 선택합니다.
5. **작업** 목록의 **추가 작업 검색** 편집 상자에 **informix**를 입력한 다음 **Informix - 행 업데이트(미리 보기)** 를 선택합니다.
6. **행 가져오기(미리 보기)** 작업에서 **연결 변경**을 선택합니다. 
7. **연결** 구성 창에서 기존 연결을 선택하려면 선택합니다. 예를 들어 **hisdemo2**를 선택합니다.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. **테이블 이름** 목록에서 **아래쪽 화살표**, **AREA**를 차례로 선택합니다.
9. 모든 필수 열(빨간색 별표 참조) 값을 입력합니다. 예를 들어 `Updated 99999` 유형의 **AREAID**에는 `99999`을(를) 입력하고 **REGIONID**에는 `102`을(를) 입력합니다. 
10. **저장**을 선택합니다. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. **InformixupdateRow** 블레이드에서 **요약** 아래의 **모두 실행** 목록에 나열된 첫 번째 항목(가장 최근 실행)을 선택합니다.
12. **논리 앱 실행** 블레이드에서 **실행 정보**를 선택합니다. **작업** 목록에서 **Get_rows**를 선택합니다. **상태** 값을 확인하는데, 이 값은 **Succeeded**여야 합니다. **입력 링크**를 선택하여 입력을 확인합니다. **출력 링크**를 선택하고 출력을 확인합니다. 새 행이 포함되어 있어야 합니다.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>DELETE를 사용하여 단일 행 삭제
Informix 테이블에서 하나의 행을 제거 하는 논리 앱 작업을 만들 수 있습니다. 이 작업에는 같은 Informix DELETE 문을 처리 하는 데 커넥터 지시 `DELETE FROM AREA WHERE AREAID = '99999'`합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기
1. **Azure 시작 보드**에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. **이름**(예: `InformixdeleteRow`), **구독**, **리소스 그룹**, **위치** 및 **App Service 계획**을 입력합니다. **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

### <a name="add-a-trigger-and-action"></a>트리거 및 작업 추가
1. **Logic Apps 디자이너**에서 **템플릿** 목록의 **빈 LogicApp**을 선택합니다.
2. **트리거** 목록에서 **되풀이**를 선택합니다. 
3. **되풀이** 트리거에서 **편집**, **빈도** 드롭다운 목록을 차례로 선택하여 **일**을 선택한 다음 **간격**을 선택하여 **7**을 입력합니다. 
4. **+ 새 단계** 상자를 선택한 다음 **작업 추가**를 선택합니다.
5. **작업** 목록의 **추가 작업 검색** 편집 상자에 **informix**를 입력한 다음 **Informix - 행 삭제(미리 보기)** 를 선택합니다.
6. **행 가져오기(미리 보기)** 작업에서 **연결 변경**을 선택합니다. 
7. **연결** 구성 창에서 기존 연결을 선택합니다. 예를 들어 **hisdemo2**를 선택합니다.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. **테이블 이름** 목록에서 **아래쪽 화살표**, **AREA**를 차례로 선택합니다.
9. 모든 필수 열(빨간색 별표 참조) 값을 입력합니다. 예를 들어 **AREAID**에는 `99999`을(를) 입력합니다. 
10. **저장**을 선택합니다. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. **InformixdeleteRow** 블레이드에서 **요약** 아래의 **모두 실행** 목록에 나열된 첫 번째 항목(가장 최근 실행)을 선택합니다.
12. **논리 앱 실행** 블레이드에서 **실행 정보**를 선택합니다. **작업** 목록에서 **Get_rows**를 선택합니다. **상태** 값을 확인하는데, 이 값은 **Succeeded**여야 합니다. **입력 링크**를 선택하여 입력을 확인합니다. **출력 링크**를 선택하고 출력을 확인합니다. 삭제된 행이 포함되어 있어야 합니다.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>지원되는 Informix 플랫폼 및 버전
DRDA (Distributed Relational Database Architecture) 클라이언트 연결을 지원하도록 구성된 경우 이 커넥터에서 지원하는 IBM Informix 버전은 다음과 같습니다.

* IBM Informix 12.1
* IBM Informix 11.7

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/informix/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md) [API 목록](apis-list.md)에서 Logic Apps의 사용 가능한 다른 커넥터를 확인하세요.

