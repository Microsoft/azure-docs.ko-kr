---
title: Azure SQL Database 관리 인스턴스 시간대 | Microsoft Docs "
description: Azure SQL Database Managed Instance의 표준 시간대 세부 사항에 알아봅니다
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 04/10/2019
ms.openlocfilehash: 23314e97051da95ab164baeab6e9d089f486351a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487414"
---
# <a name="time-zone-in-azure-sql-database-managed-instance-preview"></a>Azure SQL Database 관리 되는 인스턴스 (미리 보기)의 표준 시간대

클라우드 솔루션의 데이터 계층에 대 한 utc (협정 세계시)를 사용 하는 것이 좋습니다, 하지만 Azure SQL Database 관리 되는 인스턴스 하나를 선택할 표준 시간대의 날짜 및 시간 값을 저장 하 고 날짜를 호출 하는 기존 응용 프로그램의 요구를 충족 하도록 하 고 특정 표준 시간대의 암시적 컨텍스트를 사용 하 여 시간 함수.

T-SQL 함수 [getdate ()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) 하거나 CLR 코드 수준 인스턴스에서 설정 하는 표준 시간대를 관찰 합니다. 또한 SQL 에이전트 작업 인스턴스의 표준 시간대에 따라 일정을 따릅니다.

  >[!NOTE]
  > 관리 되는 인스턴스는 표준 시간대 설정을 지 원하는 Azure SQL Database의 전용 배포 옵션입니다. 다른 배포 옵션에는 항상 UTC 따라야합니다.
사용 하 여 [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) 아닌 UTC 표준 시간대의 날짜 및 시간 정보를 해석 하는 경우 단일 및 풀링된 SQL 데이터베이스에 있습니다.

## <a name="supported-time-zones"></a>지원 되는 표준 시간대

관리 되는 인스턴스의 기본 운영 체제에서 지원 되는 표준 시간대의 집합을 상속 하 고 정기적으로 새 표준 시간대 정의 가져오고 기존 변경 내용을 반영 개로 업데이트 중입니다.

지원 되는 표준 시간대 이름의 목록을 통해 노출 되는 [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) 시스템 뷰.

## <a name="setting-time-zone"></a>표준 시간대 설정

관리 되는 인스턴스의 표준 시간대만 인스턴스를 만드는 동안 설정할 수 있습니다. 기본 표준 시간대는 utc (협정 세계시).

  >[!NOTE]
  > 기존 관리 되는 인스턴스의 표준 시간대를 변경할 수 없습니다.

### <a name="setting-the-time-zone-through-azure-portal"></a>Azure portal 통해 표준 시간대를 설정합니다.

새 인스턴스에 대 한 매개 변수를 입력할 때 지원 되는 표준 시간대의 목록에서 표준 시간대를 선택 합니다.
  
![인스턴스를 만드는 동안 표준 시간대 설정](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

된 timezoneId 속성에서 지정 하 [Resource Manager 템플릿을](https://aka.ms/sql-mi-create-arm-posh) 인스턴스를 만드는 동안 표준 시간대를 설정 합니다.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

된 timezoneId 속성에 대 한 지원 되는 값 목록은이 문서의 끝에 있습니다.

지정 하지 않으면 표준 시간대는 UTC로 설정 됩니다.

## <a name="checking-the-time-zone-of-instance"></a>인스턴스의 표준 시간대를 확인 하는 중

[CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) 함수 인스턴스의 표준 시간대의 표시 이름을 반환 합니다.

## <a name="cross-feature-considerations"></a>기능 간 고려 사항

### <a name="restore-and-import"></a>복원 및 가져오기

백업 파일을 복원할 수도 있고 다른 표준 시간대 설정을 사용 하 여 인스턴스 또는 서버에서 관리 되는 인스턴스로 데이터를 가져올 수 있습니다. 그러나 해야 주의 해야 하 고 응용 프로그램 동작 및 쿼리 및 보고서의 결과를 분석할 때와 다른 표준 시간대 설정 사용 하 여 두 SQL Server 인스턴스 간에 데이터를 전송 합니다.

### <a name="point-in-time-restore"></a>지정 시간 복원

지정 시간 복원을 수행할 때에 복원 시간을 일광 절약 시간 및 해당 잠재적인 변경으로 인해 모든 모호성을 피하기 위해 UTC 시간으로 해석 됩니다.

### <a name="auto-failover-groups"></a>자동 장애 조치 그룹

장애 조치에서 기본 및 보조 인스턴스에서 동일한 표준 시간대를 사용 하 여 그룹에 적용 되지 않습니다 하지만 것이 좋습니다.
  >[!IMPORTANT]
  > 다른 표준 시간대를 포함 하는 것에 대 한 올바른 시나리오 있기는 지역 보조 복제본 인스턴스 읽기 배율 전용 데, 참고 보조 인스턴스를 수동 또는 자동 장애 조치의 경우이 원래 표준 시간대는 유지 됩니다.

## <a name="limitations"></a>제한 사항

- 기존 관리 되는 인스턴스의 표준 시간대를 변경할 수 없습니다.
- SQL 에이전트 작업에서 시작 하는 외부 프로세스 인스턴스의 표준 시간대를 지원 하지 않습니다.
- 관리 되는 인스턴스의 기본 [새로 만들기-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance) PowerShell cmdlet에 전달 시간이 아닌 지원 영역 매개 변수 아직 있습니다. 사용 하 여 사용 하 여 PowerShell 래퍼의 [Resource Manager 템플릿을](https://aka.ms/sql-mi-create-arm-posh) 대신 합니다.
- CLI 명령 [az sql mi 만들기](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-create) 시간대 매개 변수를 아직 지원 하지 않습니다.

## <a name="list-of-supported-time-zones"></a>지원 되는 표준 시간대의 목록

| **표준 시간대 ID** | **표준 시간대 표시 이름** |
| --- | --- |
| 날짜 변경선 표준시 | (UTC-12:00) 날짜변경선 서쪽 |
| UTC-11 | (UTC-11:00) 협정세계시-11 |
| 알류샨 표준시 | (UTC-10:00) 알류샨 열도 |
| 하와이 표준시 | (UTC-10:00) 하와이 |
| 마키저스 표준시 | (UTC-09:30) 마르케사스 제도 |
| 알래스카 표준시 | (UTC-09:00) 알래스카 |
| UTC-09 | (UTC-09:00) 협정세계시-09 |
| 태평양 표준시(멕시코) | (UTC-08:00) 바하칼리포르니아 |
| UTC-08 | (UTC-08:00) 협정세계시-08 |
| 태평양 표준시 | (UTC-08:00) 태평양 표준시(미국과 캐나다) |
| 미국 산지 표준시 | (UTC-07:00) 애리조나 |
| 산지 표준시(멕시코) | (UTC-07:00) 치와와, 라파스, 마사틀란 |
| 산지 표준시 | (UTC-07:00) 산지 표준시(미국과 캐나다) |
| 중앙 아메리카 표준시 | (UTC-06:00) 중앙 아메리카 |
| 중부 표준시 | (UTC-06:00) 중부 표준시(미국과 캐나다) |
| 이스터 섬 표준시 | (UTC-06:00) 이스터 섬 |
| 중부 표준시(멕시코) | (UTC-06:00) 과달라하라, 멕시코시티, 몬테레이 |
| 캐나다 중부 표준시 | (UTC-06:00) 서스캐처원 |
| SA 태평양 표준시 | (UTC-05:00) 보고타, 리마, 키토, 리오 브랑코 |
| 동부 표준시(멕시코) | (UTC-05:00) 체투말 |
| 동부 표준시 | (UTC-05:00) 동부 표준시(미국과 캐나다) |
| 아이티 표준시 | (UTC-05:00) 아이티 |
| 쿠바 표준시 | (UTC-05:00) 하바나 |
| 미국 동부 표준시 | (UTC-05:00) 인디애나(동부) |
| 터크스 케이커스 표준시 | (UTC-05:00) 터크스 케이커스 |
| 파라과이 표준시 | (UTC-04:00) 아순시온 |
| 대서양 표준시 | (UTC-04:00) 대서양 표준시(캐나다) |
| 베네수엘라 표준시 | (UTC-04:00) 카라카스 |
| 브라질 중부 표준시 | (UTC-04:00) 쿠이아바 |
| SA 서부 표준시 | (UTC-04:00) 조지타운, 라파스, 마나우스, 산후안 |
| 태평양 SA 표준시 | (UTC-04:00) 산티아고 |
| 뉴펀들랜드 표준시 | (UTC-03:30) 뉴펀들랜드 |
| 토칸칭스 표준시 | (UTC-03:00) 아라구아이나 |
| E. 남아메리카 표준시 | (UTC-03:00) 브라질리아 |
| SA 동부 표준시 | (UTC-03:00) 카옌, 포르탈레자 |
| 아르헨티나 표준 시간 | (UTC-03:00) 부에노스아이레스 |
| 그린란드 표준시 | (UTC-03:00) 그린란드 |
| 몬테비디오 표준시 | (UTC-03:00) 몬테비데오 |
| 마가야네스 표준시 | (UTC-03:00) 푼타 아레나스 |
| 생피에르 표준시 | (UTC-03:00) 생피에르앤드미클롱 |
| 바이아 표준시 | (UTC-03:00) 살바도르 |
| UTC-02 | (UTC-02:00) 협정세계시-02 |
| 중부-대서양 표준시 | (UTC-02:00) 중부-대서양 - 이전 |
| 아조레스 표준시 | (UTC-01:00) 아조레스 |
| 카보베르데 표준시 | (UTC-01:00) 카보베르데 제도 |
| UTC | (UTC) 협정세계시 |
| GMT 표준시 | (UTC+00:00) 더블린, 에딘버러, 리스본, 런던 |
| 그리니치 표준시 | (UTC+00:00) 몬로비아, 레이캬비크 |
| W. 유럽 표준시 | (UTC+01:00) 암스테르담, 베를린, 베른, 로마, 스톡홀름, 빈 |
| 중앙 유럽 표준시 | (UTC+01:00) 베오그라드, 브라티슬라바, 부다페스트, 류블랴나, 프라하 |
| 로망스 표준시 | (UTC+01:00) 브뤼셀, 코펜하겐, 마드리드, 파리 |
| 모로코 표준 시간 | (UTC+01:00) 카사블랑카 |
| 상투메 표준시 | (UTC+01:00) 상투메 |
| 중앙 유럽 표준시 | (UTC+01:00) 사라예보, 스코페, 바르샤바, 자그레브 |
| W. 중앙 아프리카 표준시 | (UTC+01:00) 서중앙 아프리카 |
| 요르단 표준시 | (UTC+02:00) 암만 |
| GTB 표준시 | (UTC+02:00) 아테네, 부쿠레슈티 |
| 중동 표준시 | (UTC+02:00) 베이루트 |
| 이집트 표준시 | (UTC+02:00) 카이로 |
| E. 유럽 표준시 | (UTC+02:00) 키시나우 |
| 시리아 표준시 | (UTC+02:00) 다마스쿠스 |
| 팔레스타인 표준시 | (UTC+02:00) 가자, 헤브론 |
| 남아프리카 표준시 | (UTC+02:00) 하라레, 프리토리아 |
| FLE 표준시 | (UTC+02:00) 헬싱키, 키예프, 리가, 소피아, 탈린, 빌뉴스 |
| 이스라엘 표준시 | (UTC+02:00) 예루살렘 |
| 칼리닌그라드 표준시 | (UTC+02:00) 칼리닌그라드 |
| 수단 표준시 | (UTC+02:00) 하르툼 |
| 리비아 표준시 | (UTC+02:00) 트리폴리 |
| 나미비아 표준시 | (UTC+02:00) 빈트후크 |
| 아랍 표준시 | (UTC+03:00) 바그다드 |
| 터키 표준시 | (UTC+03:00) 이스탄불 |
| 아랍 표준시 | (UTC+03:00) 쿠웨이트, 리야드 |
| 벨라루스 표준시 | (UTC+03:00) 민스크 |
| 러시아 표준시 | (UTC+03:00) 모스코바, 상트페테르부르크 |
| E. 아프리카 표준시 | (UTC+03:00) 나이로비 |
| 이란 표준시 | (UTC+03:30) 테헤란 |
| 아랍 표준시 | (UTC+04:00) 아부다비, 무스카트 |
| 아스트라한 표준시 | (UTC+04:00) 아스트라칸, 울랴노프스크 |
| 아제르바이잔 표준시 | (UTC+04:00) 바쿠 |
| 러시아 시간대 3 | (UTC+04:00) 이제프스크, 사마라 |
| 모리셔스 표준 시간 | (UTC+04:00) 포트루이스 |
| 사라토프 표준시 | (UTC+04:00) 사라토프 |
| 조지아어 표준시 | (UTC+04:00) 트빌리시 |
| 볼고그라드 표준시 | (UTC+04:00) 볼고그라드 |
| 코코서스 표준시 | (UTC+04:00) 예레반 |
| 아프가니스탄 표준시 | (UTC+04:30) 카불 |
| 서아시아 표준시 | (UTC+05:00) 아슈하바트, 타슈켄트 |
| 예카테린부르크 표준시 | (UTC+05:00) 예카테린부르크 |
| 파키스탄 표준 시간 | (UTC+05:00) 이슬라마바드, 카라치 |
| 인도 표준시 | (UTC+05:30) 첸나이, 콜카타, 뭄바이, 뉴델리 |
| 스리랑카 표준시 | (UTC+05:30) 스리자야와르데네푸라 |
| 네팔 표준시 | (UTC+05:45) 카트만두 |
| 중앙 아시아 표준시 | (UTC+06:00) 아스타나 |
| 방글라데시 표준시 | (UTC+06:00) 다카 |
| 옴스크 표준시 | (UTC+06:00) 옴스크 |
| 미얀마 표준시 | (UTC+06:30) 양곤(랑군) |
| 동남 아시아 표준시 | (UTC+07:00) 방콕, 하노이, 자카르타 |
| 알타이 표준시 | (UTC+07:00) 바르나울, 고르노알타이스크 |
| W. 몽골 표준시 | (UTC+07:00) 호브드 |
| 북아시아 표준시 | (UTC+07:00) 크라스노야르스크 |
| N. 중앙 아시아 표준시 | (UTC+07:00) 노보시비르스크 |
| 톰스크 표준시 | (UTC+07:00) 톰스크 |
| 중국 표준시 | (UTC+08:00) 베이징, 충칭, 홍콩, 우루무치 |
| 북아시아 동부 표준시 | (UTC+08:00) 이르쿠츠크 |
| 싱가포르 표준시 | (UTC+08:00) 쿠알라룸푸르, 싱가포르 |
| W. 오스트레일리아 표준시 | (UTC+08:00) 퍼스 |
| 타이베이 표준시 | (UTC+08:00) 타이베이 |
| 울란바토르 표준시 | (UTC+08:00) 울란바토르 |
| 오스트레일리아 중부 표준시 | (UTC+08:45) 유클라 |
| 트란스바이칼 표준시 | (UTC+09:00) 치타 |
| 도쿄 표준시 | (UTC+09:00) 오사카, 삿포로, 도쿄 |
| 북한 표준시 | (UTC+09:00) 평양 |
| 대한민국 표준시 | (UTC+09:00) 서울 |
| 야쿠츠크 표준시 | (UTC+09:00) 야쿠츠크 |
| 중부 오스트레일리아 표준시 | (UTC+09:30) 애들레이드 |
| 오스트레일리아 중부 표준시 | (UTC+09:30) 다윈 |
| E. 오스트레일리아 표준시 | (UTC+10:00) 브리즈번 |
| 오스트레일리아 동부 표준시 | (UTC+10:00) 캔버라, 멜버른, 시드니 |
| 서태평양 표준시 | (UTC+10:00) 괌, 포트모르즈비 |
| 태즈메이니아 표준시 | (UTC+10:00) 호바트 |
| 블라디보스토크 표준시 | (UTC+10:00) 블라디보스토크 |
| 로드하우 표준시 | (UTC+10:30) 로드하우 섬 |
| 부건빌 표준시 | (UTC+11:00) 부건빌 섬 |
| 러시아 시간대 10 | (UTC+11:00) 초쿠르다흐 |
| 마가단 표준시 | (UTC+11:00) 마가단 |
| 노퍽 표준시 | (UTC+11:00) 노퍽 섬 |
| 사할린 표준시 | (UTC+11:00) 사할린 |
| 중앙 태평양 표준시 | (UTC+11:00) 솔로몬 제도, 뉴칼레도니아 |
| 러시아 시간대 11 | (UTC+12:00) 아나디리, 페트로파블로프스크-캄차스키 |
| 뉴질랜드 표준시 | (UTC+12:00) 오클랜드, 웰링턴 |
| UTC+12 | (UTC+12:00) 협정세계시+12 |
| 피지 표준시 | (UTC+12:00) 피지 |
| 캄차카 반도 표준시 | (UTC+12:00) 페트로파블로프스크-캄차스키 - 이전 |
| 채텀 섬 표준시 | (UTC+12:45) 체텀 제도 |
| UTC+13 | (UTC+13:00) 협정 세계시+13 |
| 통가 표준시 | (UTC+13:00) 누쿠알로파 |
| 사모아 표준시 | (UTC+13:00) 사모아 |
| 라인 제도 표준시 | (UTC+14:00) 키리티마티 섬 |

## <a name="see-also"></a>참고 항목

- [CURRENT_TIMEZONE (Transact SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (TRANSACT-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)