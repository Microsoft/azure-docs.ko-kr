---
title: Azure 분석에 대한 공용 데이터 집합 | Microsoft Docs
description: Azure 분석 서비스 및 솔루션의 프로토타입을 생성하고 테스트를 수행하는 데 사용할 수 있는 공용 데이터 집합에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: WenJason
ms.author: v-jay
ms.reviewer: ''
manager: digimobile
origin.date: 10/01/2018
ms.date: 04/08/2019
ms.openlocfilehash: e7d01a6512c2d39c86da9f7020aa3988c9680c6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584457"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>테스트 및 프로토타입 생성을 위한 공용 데이터 집합

저장소 및 분석 서비스/솔루션의 프로토타입을 생성하는 데 사용할 수 있는 데이터를 이 공용 데이터 집합에서 찾아봅니다.

## <a name="us-government-and-agency-data"></a>미국 정부 및 에이전시 데이터

| 데이터 원본 | 데이터 정보 | 파일 정보 |
|---|---|---|
| [미국 정부 데이터](https://www.census.gov/data.html) | 미국의 농업, 기후, 소비자, 에코시스템, 교육, 에너지, 금융, 건강, 지방 정부, 제조, 제조, 해양, 대양, 공공 안전 및 과학 다루는 넘는 190,000 데이터 집합 상태, 정부, 제조, 해양, 바다, 공공 안전, 과학, 연구를 다루는 190,000가지 이상의 데이터 집합을 제공합니다. | 다양한 크기의 파일이 HTML, XML, CSV, JSON, Excel 등의 여러 형식으로 제공됩니다. 사용할 수 있는 데이터 집합을 파일 형식별로 필터링할 수 있습니다. |
| [미국 인구 조사 데이터](https://www.census.gov/data.html) | 미국 모집단에 대한 통계 데이터 | 데이터 집합은 다양한 형식으로 제공됩니다. |
| [NASA에서 지구 과학 데이터](https://earthdata.nasa.gov/) | 농업, 대기, 생물권, 기후, 빙설권, 인간 차원, 수권, 지표면, 대양, 태양-지구 상호 작용 등을 포괄하는 32,000가지 이상의 데이터 컬렉션입니다. | 데이터 집합은 다양한 형식으로 제공됩니다. |
| [항공사 항공편 지연 및 기타 교통 데이터](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "미국 DOT(운수부) BTS(교통 통계국)에서는 대형 항공사가 운영하는 국내 항공편의 정시 운항성을 추적합니다. 정시 운항, 취소 또는 우회 항공편의 수에 대한 요약 정보가 표로 요약되어 이 웹 사이트에 게시됩니다." | 파일은 CSV 형식입니다. |
| [교통 사고 사망률 - 미국 FARS(사망률 분석 보고 시스템)](https://www.nhtsa.gov/FARS) | "FARS는 교통 사고로 인한 심각한 부상과 관련된 연간 데이터를 NHTSA, 의회 및 미국인에게 제공하는 전국 인구 조사입니다." | "FARS 쿼리 시스템을 사용하여 온라인으로 실행되는 사망률 데이터를 만들 수 있습니다. 또는 FTP 사이트에서 1975부터 현재까지의 모든 FARS 데이터를 다운로드할 수 있습니다." |
| [유해 화학물 데이터 - EPA Toxicity ForeCaster(ToxCast™) 데이터](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "EPA에서 제공하는 수천 가지 화학물에 대한 최신의 업데이트된 고효율 독성물 데이터입니다. 이 데이터는 EPA의 ToxCast 연구를 통해 생성된 것입니다." | 데이터 집합은 스프레드시트, R 패키지 및 MySQL 데이터베이스 파일을 비롯한 다양한 형식으로 제공됩니다. |
| [유해 화학물 데이터 - NIH Tox21 데이터 챌린지 2014](https://tripod.nih.gov/tox21/challenge/) | "2014 Tox21 데이터 챌린지는 과학자들이 Toxicology in the 21st Century(21세기 독성학 이니셔티브)를 통해 테스트되는 화학물 및 화합물이 독성 효과를 통해 생물학적 경로를 중단시킬 수 있는 가능성을 이해하도록 하기 위해 고안되었습니다." | 데이터 집합은 SMILES 및 SDF 형식으로 사용할 수 있습니다. 데이터는 "10,000개 이하의 합성물(Tox21 10K)로 이루어진 Tox21 컬렉션에 대한 분석 활동 데이터 및 화학물 구조"를 제공합니다. |
| [NCBI의 생명 공학 및 게놈 데이터](https://www.ncbi.nlm.nih.gov/guide/data-software/) | 유전자, 게놈 및 단백질을 포함하는 여러 데이터 집합입니다. | 데이터 집합은 텍스트, XML, BLAST 및 기타 형식으로 제공됩니다. BLAST 앱을 사용할 수 있습니다. |

## <a name="other-statistical-and-scientific-data"></a>기타 통계 및 과학 데이터

| 데이터 원본 | 데이터 정보 | 파일 정보 |
|---|---|---|
| [뉴욕시 택시 데이터](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | “택시 여정 레코드에는 승차 및 하차 날짜/시간, 승차 및 하단 위치, 이동 거리, 항목별 요금, 요율 종류, 지불 유형 및 운전자가 보고한 승객 수를 캡처하는 필드가 포함됩니다.” | 데이터 집합은 월별 CSV 파일 형식입니다. |
| [Microsoft Research 데이터 집합 - "연구를 위한 데이터 과학"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | 인간-컴퓨터 간 상호 작용, 오디오/비디오, 데이터 마이닝/정보 검색, 지리 공간/위치, 자연어 처리 및 로봇 공학/컴퓨터 비전을 아우르는 여러 데이터 집합이 제공됩니다. | 데이터 집합은 다운로드할 수 있게 다양한 형식으로 압축되어 있습니다. |
| [공용 게놈 데이터](https://www.completegenomics.com/public-data/) | "일반 사용자가 전체 인간 게놈을 포함하는 다양한 데이터 집합을 무료로 사용하여 게놈 연구에 활용할 수 있습니다." 공급업체 Complete Genomics는 영리를 추구하는 개인 기업입니다. | 압축을 푼 데이터 집합은 UNIX 텍스트 형식입니다. 분석 도구를 사용할 수도 있습니다. |
| [개방형 과학 데이터 클라우드 데이터](https://www.opensciencedatacloud.org/) | "개방형 과학 데이터 클라우드는 테라바이트 및 페타바이트 크기의 과학 데이터 세트를 저장, 공유 및 분석하기 위한 리소스를 포함하는 과학 커뮤니티를 제공합니다."| 데이터 집합은 다양한 형식으로 제공됩니다. |
| [전 세계 기후 데이터 - WorldClim](https://worldclim.org/) | "WorldClim은 약 1km2의 공간 해상도를 갖는 글로벌 기후 계층(표 형식의 날씨 데이터) 집합입니다. 이러한 데이터는 매핑 및 공간 모델링에 사용할 수 있습니다." | 이러한 파일에는 지리 공간 데이터가 포함됩니다. 자세한 내용은 [데이터 형식](https://worldclim.org/formats1)을 참조하세요. |
| [인간 사회 관련 데이터 - GDELT 프로젝트](https://www.gdeltproject.org/data.html) | "GDELT 프로젝트는 현존하는 가장 크고 가장 포괄적이고 해상도가 가장 높은 개방형 인간 사회 데이터베이스입니다." | 원시 데이터 파일은 CSV 형식입니다. |
| [Criteo의 Machine Learning을 위한 광고 클릭 예측 데이터](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "공개적으로 출시된 가장 큰 ML 데이터 세트입니다." 자세한 내용은 [Criteo의 1TB 클릭 예측 데이터 세트](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/)를 참조하세요. | |
| [Lemur 프로젝트이 ClueWeb09 텍스트 마이닝 데이터 집합](https://www.lemurproject.org/clueweb09.php/) | "ClueWeb09 데이터 세트는 정보 검색 및 관련된 인간 언어 기술에 대한 연구를 지원하기 위해 만들어졌습니다. 이 데이터 집합은 2009년 1월과 2월에 수집된 10억 개의 웹 페이지를 10개 언어로 제공합니다." | [데이터 세트 정보](https://www.lemurproject.org/clueweb09/datasetInformation.php)를 참조하세요.|

## <a name="online-service-data"></a>온라인 서비스 데이터

| 데이터 원본 | 데이터 정보 | 파일 정보 |
|---|---|---|
| [GitHub 보관](https://www.githubarchive.org/) | "GitHub 보관은 [이벤트의] 공용 GitHub 타임라인을 기록하고, 보관하고, 추가 분석을 위해 쉽게 액세스할 수 있도록 하는 프로젝트입니다." | 웹 클라이언트에서.gz(Gzip) 형식의 JSON 인코딩 이벤트 보관 파일을 다운로드합니다. |
| [GHTorrent 프로젝트의 GitHub 작업 데이터](http://ghtorrent.org/) | "GHTorrent 프로젝트는 GitHub REST API를 통해 제공되는 데이터의 확장 및 쿼리 가능한 오프라인 미러를 만들기 위한 작업입니다. GHTorrent는 GitHub 공용 이벤트 타임라인을 모니터링합니다. 각 이벤트에 대해 해당 콘텐츠 및 종속성을 철저히 검색합니다." | MySQL 데이터베이스 덤프는 CSV 형식입니다. |
| [Stack Overflow 데이터 덤프](https://archive.org/details/stackexchange) | "[Stack Overflow를 포함하여] 스택 교환 네트워크에서 모든 사용자가 만든 콘텐츠의 익명 처리된 덤프입니다." | "[Stack Overflow 등의] 각 사이트는 bzip2 압축을 사용하여 7-zip을 통해 압축된 XML 파일로 구성되는 별도 아카이브로 형식이 지정됩니다. 각 사이트 보관 파일에는 Posts, Users, Votes, Comments, PostHistory 및 PostLinks가 포함됩니다." |
