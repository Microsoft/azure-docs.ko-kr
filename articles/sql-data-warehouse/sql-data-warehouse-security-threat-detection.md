---
title: 위협 감지 - Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Data Warehouse에서 위협 탐지를 구성하고 의심스러운 이벤트 탐색합니다.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: a3204c2705ba7b83c4fe22ab6bdd15c11eeeeda5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421132"
---
# <a name="threat-detection-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에서 위협 탐지
Azure SQL Data Warehouse에서 위협 탐지를 구성하고 의심스러운 이벤트 탐색합니다.

## <a name="what-is-threat-detection"></a>위협 탐지란?
위협 감지는 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 감지합니다. 

위협 감지는 비정상적인 활동에 대한 보안 경고를 제공하여 잠재적인 위협이 발생하면 고객이 이를 감지하고 대응할 수 있도록 하는 새로운 차원의 보안을 제공합니다. 사용자는 데이터 웨어하우스의 데이터를 액세스, 침해 또는 악용하려는 시도로 인해 의심스러운 이벤트가 발생했는지를 판단하기 위해서 [Azure SQL Data Warehouse 감사](sql-data-warehouse-auditing-overview.md) 를 사용하여 의심스러운 이벤트를 살펴볼 수 있습니다.
위협 감지는 보안 전문가가 되거나 고급 보안 모니터링 시스템을 관리할 필요 없이 데이터 웨어하우스에 대한 잠재적인 위협에 간단하게 대처할 수 있도록 합니다.

예를 들어 위협 감지는 잠재적인 SQL 삽입 시도를 나타내는 비정상적인 데이터베이스 활동을 감지합니다. SQL 삽입은 데이터 기반 애플리케이션 공격에 사용되는 인터넷 상의 일반적인 웹 애플리케이션 보안 문제 중 하나입니다. 공격자는 데이터베이스의 데이터를 침범하거나 수정하기 위해 애플리케이션의 취약성을 이용하여 악의적인 SQL 문을 애플리케이션 항목 필드에 삽입합니다.

## <a name="set-up-threat-detection-for-your-database"></a>데이터베이스에 대한 위협 감지 설정
1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal을 시작합니다.
2. 모니터링할 SQL Data Warehouse의 구성 블레이드로 이동합니다. 설정 블레이드에서 **감사 및 위협 감지**를 선택합니다.
   
    ![탐색 창](media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png)
3. **감사 및 위협 감지** 구성 블레이드에서 감사를 **켜면** 위협 감지 설정이 표시됩니다.
   
    ![탐색 창](media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png)
4. 위협 감지를 **켭니다**
5. 비정상적인 데이터 웨어하우스 활동이 감지되는 경우 보안 경고를 수신할 이메일 목록을 구성합니다.
6. **감사 및 위협 감지** 구성 블레이드에서 **저장**을 클릭하여 새로운 또는 업데이트된 감사 및 위협 감지 정책을 저장합니다.
   
    ![탐색 창](media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png)

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>의심스러운 이벤트 감지 시 비정상적인 데이터 웨어하우스 활동 살펴보기
1. 비정상적인 데이터베이스 활동이 감지되면 이메일 알림을 받게 됩니다. <br/>
    이메일에는 비정상적인 활동의 특징, 데이터베이스 이름, 서버 이름, 이벤트 시간을 포함하여 의심스러운 보안 이벤트에 대한 정보가 제공됩니다. 또한 가능한 원인에 대한 정보와 데이터베이스에 대한 잠재적인 위협을 조사하고 완화시키기 위해 권장되는 조치가 제공됩니다.<br/>
   
    ![탐색 창](media/sql-data-warehouse-security-threat-detection/4_td_email.png)
2. 이메일에서 **Azure SQL 감사 로그** 링크를 클릭하면 Azure 포털이 열리고 의심스러운 이벤트가 발생한 무렵의 시간에 해당하는 감사 레코드가 표시됩니다.
   
    ![탐색 창](media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png)
3. 의심스러운 데이터베이스 활동에 대한 세부 정보(예: SQL 문, 실패 원인, 클라이언트 IP)를 보려면 감사 레코드를 클릭합니다.
   
    ![탐색 창](media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png)
4. 미리 구성된 Excel 템플릿을 열고 의심스러운 이벤트가 발생한 무렵의 시간에 대한 감사 로그를 가져와서 심층적인 분석을 실행하려면 감사 레코드 블레이드에서 **Excel에서 열기**를 클릭합니다.<br/>
   **참고:** Excel 2010 이상의 경우 파워 쿼리 및 **빠른 결합** 설정이 필요합니다.
   
    ![탐색 창](media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png)
5. **빠른 결합** 설정을 구성하려면 - **파워 쿼리** 리본 탭에서 **옵션**을 선택하여 옵션 대화 상자를 표시합니다. 개인 정보 섹션을 선택하고 두 번째 옵션 '개인 정보 보호 수준을 무시하고 잠재적으로 성능 향상'을 선택합니다.
   
    ![탐색 창](media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png)
6. SQL 감사 로그를 로드하려면, 설정 탭의 매개 변수가 바르게 설정되었는지 확인한 다음, '데이터' 리본을 선택하고 '모두 새로 고침' 단추를 클릭합니다.
   
    ![탐색 창](media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png)
7. **SQL 감사 로그** 시트에 결과가 표시되며 사용자는 이를 통해 감지된 비정상적인 활동을 심층적으로 분석하고 애플리케이션의 보안 이벤트에 대한 영향을 완화시킬 수 있습니다.

## <a name="next-steps"></a>다음 단계
보안 정보를 더 보려면 [데이터베이스 보호](sql-data-warehouse-overview-manage-security.md)를 참조하세요.
