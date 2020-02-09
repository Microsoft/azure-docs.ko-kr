---
title: SaaS SQL 앱 비디오
description: 이 문서는 2017년 10월 11일에 열린 Ignite 컨퍼런스에서의 SaaS DB 테넌트 앱 디자인에 관한 81분짜리 동영상에 여러 시점을 인덱싱합니다. 관심 있는 파트로 건너뛸 수 있습니다. 적어도 3 가지 패턴을 설명합니다. 개발 및 관리를 단순화하는 Azure 기능을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 0194d99a10eb34ecd8ad10fa0a1509baf8436381
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818470"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Azure SQL Database를 사용하여 다중 테넌트 SaaS 앱에 대해 인덱싱되고 주석이 지정된 동영상

이 문서는 SaaS 테넌트 모델 또는 패턴에 관한 81분짜리 동영상의 시간 위치에 달린 주석이 지정된 인덱스입니다. 이 문서를 사용하면 동영상에서 관심이 있는 부분으로 뒤로 또는 앞으로 건너뛸 수 있습니다. 동영상은 Azure SQL Database에서 다중 테넌트 데이터베이스 애플리케이션에 대한 주요 디자인 옵션을 설명합니다. 동영상은 데모, 관리 코드의 연습 및 때로 쓰여진 설명서보다 경험에 의해 알려지는 더 자세한 내용을 포함합니다.

동영상은 다음에서 찾을 수 있는 쓰여진 설명서에 나오는 내용을 증폭합니다. 
- *개념:* [다중 테넌트 SaaS 데이터베이스 테넌트 패턴][saas-concept-design-patterns-563e]
- *자습서:* [정문 ticket SaaS 응용 프로그램][saas-how-welcome-wingtip-app-679t]

동영상과 문서는 클라우드의 Azure SQL Database에서 다중 테넌트 애플리케이션을 만드는 여러 단계를 설명합니다. Azure SQL Database의 특수 기능을 사용하면 관리하기 쉽고 안정적인 성능을 발휘하는 다중 테넌트 앱을 보다 쉽게 개발하고 구현할 수 있습니다.

우리는 쓰여진 설명서를 정기적으로 업데이트합니다. 동영상은 편집되거나 업데이트되지 않으므로, 결국 세부적인 부분의 상당량이 구식이 되고 말 것입니다.



## <a name="sequence-of-38-time-indexed-screenshots"></a>일련의 시간 인덱싱된 38개의 스크린샷

이 섹션은 81분짜리 동영상의 전반에 걸쳐 38 토론의 시간 위치를 인덱싱합니다. 각 시간 인덱스에 도영상의 스크린샷 및 때로 추가 정보를 담은 주석이 지정됩니다.

각 시간 인덱스의 형식은 *h:mm: ss*입니다. 예를 들어, **세션 목표**라는 제목이 붙은 두 번째 인덱싱된 시간 위치는 대략적인 시간 위치 **0:03:11**에 시작합니다.


### <a name="compact-links-to-video-indexed-time-locations"></a>동영상 인덱싱된 시간 위치에 대한 압축 링크

다음 제목은 이 문서의 마지막 부분에 나오는 해당 주석이 들어 있는 섹션과 연결된 링크입니다.

- [1. **(시작)** 시작 슬라이드, 0:00:03](#anchor-image-wtip-min00001)
- [2. 세션 목표, 0:03:11](#anchor-image-wtip-min00311)
- [3. 어 젠더, 0:04:17](#anchor-image-wtip-min00417)
- [4. 다중 테넌트 웹 앱, 0:05:05](#anchor-image-wtip-min00505)
- [5. 작동 중인 앱 웹 폼, 0:05:55](#anchor-image-wtip-min00555)
- [6. 테넌트 당 비용 (크기 조정, 격리, 복구), 0:09:31](#anchor-image-wtip-min00931)
- [7. 다중 테넌트에 대 한 데이터베이스 모델: 장점 및 단점, 0:11:59](#anchor-image-wtip-min01159)
- [8. 하이브리드 모델은 MT/ST, 0:13:01의 이점을 혼합 합니다.](#anchor-image-wtip-min01301)
- [9. 단일 테넌트 및 다중 테넌트: 장점 및 단점, 0:16:44](#anchor-image-wtip-min01644)
- [10. 풀은 예측할 수 없는 워크 로드에 대해 비용 효율적 이며 0:19:36](#anchor-image-wtip-min01936)
- [11. 테넌트 당 데이터베이스 및 하이브리드 ST/MT 데모, 0:20:08](#anchor-image-wtip-min02008)
- [12. Dojo를 보여주는 라이브 앱 양식, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB는, 0:28:54의 DBA가 아닙니다.](#anchor-image-wtip-min02854)
- [14. MYOB 탄력적 풀 사용 예제, 0:29:40](#anchor-image-wtip-min02940)
- [15. MYOB 및 다른 Isv의 학습, 0:31:36](#anchor-image-wtip-min03136)
- [16. 패턴은 E2E SaaS 시나리오, 0:43:15](#anchor-image-wtip-min04315)
- [17. 정식 하이브리드 다중 테넌트 SaaS 앱, 0:47:33](#anchor-image-wtip-min04733)
- [18. 정문 SaaS 샘플 앱, 0:48:10](#anchor-image-wtip-min04810)
- [19. 자습서에서 탐색 한 시나리오 및 패턴, 0:49:10](#anchor-image-wtip-min04910)
- [20. 자습서 및 GitHub 리포지토리 데모, 0:50:18](#anchor-image-wtip-min05018)
- [21. GitHub 리포지토리 Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. 패턴 살펴보기, 0:56:20](#anchor-image-wtip-min05620)
- [23. 테넌트 및 온 보 딩 프로 비전, 0:57:44](#anchor-image-wtip-min05744)
- [24. 테넌트 및 응용 프로그램 연결 프로 비전, 0:58:58](#anchor-image-wtip-min05858)
- [25. 단일 테넌트를 프로 비전 하는 관리 스크립트 데모, 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell 프로 비전 및 카탈로그, 1:00:02](#anchor-image-wtip-min10002)
- [27. t-sql SELECT * FROM TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. 예측할 수 없는 테넌트 워크 로드 관리, 1:04:36](#anchor-image-wtip-min10436)
- [29. 탄력적 풀 모니터링, 1:06:39](#anchor-image-wtip-min10639)
- [30. 부하 생성 및 성능 모니터링, 1:09:42](#anchor-image-wtip-min10942)
- [31. 대규모 스키마 관리, 1:10:33](#anchor-image-wtip-min11033)
- [32. 테넌트 데이터베이스 간 분산 쿼리, 1:12:21](#anchor-image-wtip-min11221)
- [33. 티켓 생성 데모, 1:12:32](#anchor-image-wtip-min11232)
- [34. SSMS 임시 분석, 1:12:46](#anchor-image-wtip-min11246)
- [35. SQL DW로 테넌트 데이터 추출, 1:16:32](#anchor-image-wtip-min11632)
- [36. 일일 판매 분포 그래프, 1:16:48](#anchor-image-wtip-min11648)
- [37. 래핑 및 호출 작업, 1:19:52](#anchor-image-wtip-min11952)
- [38. 자세한 내용은 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>동영상에서 주석이 지정된 인덱스 시간 위치

동영상의 정확한 시간 위치로 가게 해 주는 스크린 샷 이미지를 클릭합니다.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(시작)* 시작 슬라이드, 0:00:01

*MYOB에서 배움: Azure SQL Database에서 SaaS 애플리케이션에 대한 디자인 패턴 - BRK3120*

[![환영 슬라이드][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- 제목: MYOB에서 배움: Azure SQL Database에서 SaaS 애플리케이션에 대한 디자인 패턴
- Bill.Gibson@microsoft.com
- 수석 프로그램 관리자, Azure SQL 데이터베이스
- Microsoft Ignite 세션 BRK3120, Orlando, FL USA, 2017년 10월 11일


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. 세션 목표, 0:01:53
[![세션 목표][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- 장점 및 단점을 가진 다중 테넌트 앱에 대한 대체 모델.
- 개발, 관리 및 리소스 비용을 줄이기 위한 SaaS 패턴.
- 샘플 앱 + 스크립트.
- PaaS 기능 + SaaS 패턴을 사용하여 다중 테넌트 SaaS에 대한 SQL Database를 확장성이 뛰어나고, 비용 효율적인 데이터 플랫폼으로 만듭니다.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. 어 젠더, 0:04:09
[![어젠더][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. 다중 테넌트 웹 앱, 0:05:00
[![Wingtip SaaS 앱: 다중 테넌트 웹앱][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. 작동 중인 앱 웹 폼, 0:05:39
[![작동 중인 앱 웹 양식][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. 테넌트 당 비용 (크기 조정, 격리, 복구), 0:06:58
[![테넌트 당 비용(비율, 격리, 복구)][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. 다중 테넌트에 대 한 데이터베이스 모델: 장점 및 단점, 0:09:52
[![다중 테넌트에 대한 데이터베이스 모델: 장점 및 단점][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. 하이브리드 모델은 MT/ST, 0:12:29의 이점을 혼합 합니다.
[![하이브리드 모델은 MT/ST의 이점을 혼합함][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. 단일 테넌트 및 다중 테넌트: 장점 및 단점, 0:13:11
[![단일 테넌트 및 다중 테넌트: 장점 및 단점][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. 풀은 예측할 수 없는 워크 로드에 대해 비용 효율적 이며 0:17:49
[![풀은 예측할 수 없는 워크로드에 대해 비용 효율적임][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. 테넌트 당 데이터베이스 및 하이브리드 ST/MT 데모, 0:19:59
[![테넌트 당 데이터베이스 및 하이브리드 ST/MT 데모][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Dojo를 보여주는 라이브 앱 양식, 0:20:10
[![Dojo를 보여주는 라이브 웹 양식][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB는, 0:25:06의 DBA가 아닙니다.
[![보기에 MYOB이고 DBA가 아님][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. MYOB 탄력적 풀 사용 예제, 0:29:30
[![MYOB 탄력적 풀 사용 예제][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. MYOB 및 다른 Isv의 학습, 0:31:25
[![MYOB 및 기타 ISV에서 배움][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. 패턴은 E2E SaaS 시나리오, 0:31:42
[![E2E SaaS 시나리오로의 패턴 구성][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. 정식 하이브리드 다중 테넌트 SaaS 앱, 0:46:04
[![Canonical 하이브리드 다중 테넌트 SaaS 앱][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. 정문 SaaS 샘플 앱, 0:48:01
[![Wingtip SaaS 샘플 앱][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. 자습서에서 탐색 한 시나리오 및 패턴, 0:49:00
[![자습서에서 탐색한 시나리오 및 패턴][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. 자습서 및 GitHub 리포지토리 데모, 0:50:12
[![자습서 및 GitHub 리포지토리 데모][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. GitHub 리포지토리 Microsoft/WingtipSaaS, 0:50:32
[![GitHub 리포지토리 Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. 패턴 살펴보기, 0:56:15
[![패턴 탐색][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. 테넌트 및 온 보 딩 프로 비전, 0:56:19
[![테넌트 및 온보딩 프로비전][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. 테넌트 및 응용 프로그램 연결 프로 비전, 0:57:52
[![테넌트 및 애플리케이션 연결 프로비전][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. 단일 테넌트를 프로 비전 하는 관리 스크립트 데모, 0:59:36
[![단일 테넌트를 프로비전하는 관리 스크립트 데모][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell 프로 비전 및 카탈로그, 0:59:56
[![프로비전 및 카탈로그에 대한 PowerShell][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. t-sql SELECT * FROM TenantsExtended, 1:03:25
[![T-SQL SELECT * FROM TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. 예측할 수 없는 테넌트 워크 로드 관리, 1:03:34
[![예측할 수 없는 테넌트 워크로드 관리][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. 탄력적 풀 모니터링, 1:06:32
[![탄력적 풀 모니터링][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. 부하 생성 및 성능 모니터링, 1:09:37
[![부하 생성 및 성능 모니터링][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. 대규모 스키마 관리, 1:09:40
[![대량의 스키마 관리][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. 테넌트 데이터베이스 간 분산 쿼리, 1:11:18
[![테넌트 데이터베이스 간 분산 쿼리][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. 티켓 생성 데모, 1:12:28
[![티켓 생성 데모][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. SSMS 임시 분석, 1:12:35
[![SSMS 임시 분석][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. SQL DW로 테넌트 데이터 추출, 1:15:46
[![ SQL DW로 테넌트 데이터 추출][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. 일일 판매 분포 그래프, 1:16:38
[![ 일일 판매 분포 그래프][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. 래핑 및 호출 작업, 1:17:43
[![요약 및 활용 방안][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. 자세한 내용은 1:20:35
[![자세한 내용을 관한 리소스][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [블로그 게시물, 2017 년 5 월 22 일][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *개념:* [다중 테넌트 SaaS 데이터베이스 테넌트 패턴][saas-concept-design-patterns-563e]

- *자습서:* [정문 ticket SaaS 응용 프로그램][saas-how-welcome-wingtip-app-679t]

- Wingtip Tickets SaaS 테넌트 애플리케이션을 위한 GitHub 리포지토리:
    - [독립 실행형 응용 프로그램 모델에 대 한 GitHub 리포지토리][github-wingtip-standaloneapp]
    - [-테넌트 모델용-DB에 대 한 GitHub 리포지토리][github-wingtip-dbpertenant]
    - [-다중 테넌트 DB 모델에 대 한 GitHub 리포지토리][github-wingtip-multitenantdb]





## <a name="next-steps"></a>다음 단계

- [첫 번째 자습서 문서][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "환영 슬라이드"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "세션 목표"

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "어젠더."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Wingtip SaaS 앱: 다중 테넌트 웹앱"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "작동 중인 앱 웹 양식"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "테넌트 당 비용, 비율, 격리, 복구"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "다중 테넌트에 대한 데이터베이스 모델: 장점 및 단점"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "하이브리드 모델은 MT/ST의 이점을 혼합함"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "단일 테넌트 및 다중 테넌트: 장점 및 단점"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "풀은 예측할 수 없는 워크로드에 대해 비용 효율적임"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "테넌트 당 데이터베이스 및 하이브리드 ST/MT 데모"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Dojo를 보여주는 라이브 웹 양식"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "보기에 MYOB이고 DBA가 아님"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "MYOB 탄력적 풀 사용 예제"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "MYOB 및 기타 ISV에서 배움"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "E2E SaaS 시나리오로의 패턴 구성"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Canonical 하이브리드 다중 테넌트 SaaS 앱"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Wingtip SaaS 샘플 앱"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "자습서에서 탐색한 시나리오 및 패턴"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "자습서 및 GitHub 리포지토리 데모"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "GitHub 리포지토리 Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "패턴 탐색"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "테넌트 및 온보딩 프로비전"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "테넌트 및 애플리케이션 연결 프로비전"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "단일 테넌트를 프로비전하는 관리 스크립트 데모"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "프로비전 및 카탈로그에 대한 PowerShell"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * FROM TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "예측할 수 없는 테넌트 워크로드 관리"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "탄력적 풀 모니터링"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "부하 생성 및 성능 모니터링"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "대량의 스키마 관리"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "테넌트 데이터베이스 간 분산 쿼리"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "티켓 생성 데모"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "SSMS 임시 분석"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "SQL DW로 테넌트 데이터 추출"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "일일 판매 분포 그래프"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "요약 및 활용 방안"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "자세한 내용을 관한 리소스"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

