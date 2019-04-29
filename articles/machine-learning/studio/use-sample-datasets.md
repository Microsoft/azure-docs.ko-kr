---
title: 샘플 데이터 세트 사용
titleSuffix: Azure Machine Learning Studio
description: Machine Learning Studio에 포함된 샘플 모델에서 사용되는 데이터 세트를 설명합니다. 실험에 대해 이 샘플 데이터 세트를 사용할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: f86ae4977621927a09d9b83287a00dfa3bc17196
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736618"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio에서 샘플 데이터 세트 사용
[top]: #machine-learning-sample-datasets

Azure Machine Learning Studio에서 새 작업 영역을 만들 때 다양 한 샘플 데이터 집합 및 실험이 기본적으로 포함 됩니다. 이 샘플 데이터 세트 중 다수는 [Azure AI 갤러리](https://gallery.azure.ai/)의 샘플 모델에서 사용됩니다. 나머지는 Machine Learning에서 일반적으로 사용되는 다양한 유형의 데이터 예로 포함됩니다.

일부 데이터 세트는 Azure Blob Storage에서 사용할 수 있습니다. 이러한 데이터 세트의 경우 다음 표에서 직접 링크가 제공됩니다. [데이터 가져오기][import-data] 모듈을 사용하여 이 데이터 세트를 실험에 사용할 수 있습니다.

이러한 샘플 데이터 세트의 나머지는 작업 영역의 **저장된 데이터 세트** 아래에서 확인할 수 있습니다. Machine Learning Studio의 실험 캔버스 왼쪽에 있는 모듈 팔레트에서 찾을 수 있습니다.
실험 캔버스로 끌어와 이러한 데이터 세트를 실험에서 사용할 수 있습니다.

## <a name="datasets"></a>데이터 세트

<table>

<tr>
  <th>데이터 세트 이름</th>
  <th>데이터 세트 설명</th>
</tr>

<tr>
  <td>성인 인구 조사 소득 이진 분류 데이터 세트</td>
  <td>
조정 소득 지수가 100보다 큰 16세 이상 취업한 성인을 대상으로 한 1994 인구 조사 데이터베이스의 하위 집합입니다.
<p></p>
<b>사용:</b> 인구 통계를 기반으로 사람을 분류하여 개인의 소득이 연간 50,000을 초과할지 예측합니다.
<p></p>
<b>관련 조사:</b> Kohavi, R., Becker, B.,(1996). UCI Machine Learning 리포지토리 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>공항 코드 데이터 세트</td>
  <td>
미국 공항 코드.
<p></p>
이 데이터 세트에는 각 미국 공항에 대해 공항 ID 번호 및 이름을 도시/주 위치와 함께 제공하는 하나의 행이 포함됩니다.
  </td>
</tr>

<tr>
  <td>자동차 가격 데이터(원시)</td>
  <td>
가격, 실린더 및 MPG 수와 같은 기능, 보험 위험 점수를 포함하여 상표 및 모델별 자동차에 대한 정보입니다.
<p></p>
위험 점수는 처음에 자동차 가격과 연관이 있습니다. 그런 다음 보험 회계사에게 기호화로 알려진 프로세스에서 실제 위험에 맞게 조정됩니다. +3 값은 자동차가 위험함을 나타내고 -3 값은 안전함을 나타냅니다.
<p></p>
<b>사용:</b> 회귀 및 다변수 분류를 사용하여 기능별 위험 점수를 예측합니다. 
<p></p>
<b>관련 조사:</b> Schlimmer, J.C. (1987). UCI Machine Learning 리포지토리 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>자전거 임대 UCI 데이터 세트</td>
  <td>
Washington DC에서 자전거 임대망을 유지 관리하는 Capital Bikeshare 회사의 실제 데이터를 기반으로 하는 UCI 자전거 임대 데이터 세트.
<p></p>
데이터 세트에는 2011년 및 2012년의 매일 매시간에 대한 행 하나가 있고, 전체 17379개의 행이 있습니다. 매시간 자전거 임대 범위는 1-977입니다.

  </td>
</tr>

<tr>
  <td>Bil Gates RGB 이미지</td>
  <td>
CSV 데이터로 변환된 공개 사용 가능한 이미지 파일.
<p></p>
이미지 변환 코드는 <strong>K-Means 클러스터링을 사용한 색상 양자화</strong> 모델 세부 정보 페이지에 제공됩니다.
  </td>
</tr>

<tr>
  <td>헌혈 데이터</td>
  <td>
대만, Hsin-Chu City, Blood Transfusion Service Center 헌혈자 데이터베이스의 데이터 하위 집합입니다.
<p></p>
현혈자 데이터에는 마지막 헌혈 이후 지난 개월 수, 빈도 또는 총 헌혈 횟수, 마지막 헌혈 이후 시간, 헌혈량이 포함됩니다.
<p></p>
<b>사용:</b> 목표는 분류를 통해 헌혈자가 2007년 3월 헌혈을 했는지 예측하는 것입니다. 여기서 1은 목표 기간 중 헌혈자를 나타내고 0은 비헌혈자를 나타냅니다. 
<p></p>
<b>관련 조사:</b> Yeh, I.C.,(2008). UCI Machine Learning 리포지토리 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
<p></p>
Yeh, I-Cheng, Yang, King-Jang, 및 Ting, Tao-Ming, "Bernoulli 시퀀스를 사용한 RFM 모델에 대한 정보 검색 "2008 년 애플리케이션을 이용한 전문가 시스템 <a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>유방암 데이터</td>
  <td>
기계 학습 문헌에서 자주 나타나는 Oncology Institute에서 제공하는 세 가지 암 관련 데이터 세트의 하나입니다. 진단 정보를 300여 개 조직 샘플에 대한 실험실 분석의 기능과 결합합니다.
<p></p>
<b>사용:</b> 9개 특성에 따라 암 유형을 분류합니다. 이들 중 몇몇은 선형이고 몇몇은 범주입니다. 
<p></p>
<b>관련 조사:</b> Wohlberg, W.H., Street, W.N., & Mangasarian, O.L. (1995). UCI Machine Learning 리포지토리 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>유방암 기능 <td>
데이터 세트에서는 X선 이미지의 102,000개 의심 영역(후보)에 대한 정보가 117가지 기능에 의해 설명됩니다. 이 기능은 독점적이며, 해당 의미를 데이터 세트 작성자(Siemens Healthcare)가 공개하지 않습니다. 
  </td>
</tr>

<tr>
  <td>유방암 정보</td>
  <td>
데이터 세트에는 X선 이미지의 의심스러운 영역 각각에 대한 추가 정보가 포함됩니다. 각 예제는 Breast Cancer Features 데이터 세트에 있는 해당 행 번호에 대한 정보(예: 레이블, 환자 ID, 전체 이미지를 기준으로 한 패치 좌표)를 제공합니다. 환자별로 많은 예제가 있습니다. 암이 있는 환자의 경우 일부 예제는 양성이고 일부는 음성입니다. 암이 없는 환자의 경우 모든 예제는 음성입니다. 데이터 세트에는 102,000개의 예제가 있습니다. 데이터 세트가 편향적이며, 요소의 0.6%가 양성이고 나머지는 음성입니다. 데이터 세트는 Siemens Healthcare가 제공했습니다.
  </td>
</tr>

<tr>
  <td>CRM 욕구 레이블 공유</td>
  <td>
KDD Cup 2009 고객 관계 예측 챌린지의 레이블(<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM 이탈 레이블 공유</td>
  <td>
KDD Cup 2009 고객 관계 예측 챌린지의 레이블(<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM 데이터 세트 공유</td>
  <td>
이 데이터는 KDD Cup 2009 고객 관계 예측 챌린지(<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>)에서 제공됩니다.
<p></p>
데이터 세트에는 French Telecom 회사인 Orange의 고객 50,000명이 포함됩니다. 각 고객은 익명으로 처리되는 230개 기능을 가지며, 이 중 190개는 숫자이고 40개는 범주입니다. 기능이 매우 희박합니다.
  </td>
</tr>

<tr>
  <td>CRM 상향 판매 레이블 공유</td>
  <td>
KDD Cup 2009 고객 관계 예측 챌린지의 레이블(<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>에너지 효율 회귀 데이터</td>
  <td>
12가지 건물 형태에 따라 시뮬레이트된 에너지 프로필의 컬렉션입니다. 건물은 8개의 특징으로 구분됩니다. 여기에는 창호 면적, 창호 면적 분포 및 방향이 포함됩니다.
<p></p>
<b>사용:</b> 회귀 또는 분류를 사용하여 두 가지 실제 값 응답의 하나를 기반으로 에너지 효율 등급을 예측합니다. 다중 클래스 분류에 대해서는 가장 가까운 정수로 응답 변수를 반올림합니다. 
<p></p>
<b>관련 조사:</b> Xifara, A. & Tsanas, A. (2012). UCI Machine Learning 리포지토리 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>비행 지연 데이터</td>
  <td>
미국 운수부 TranStats 데이터 컬렉션에서 가져온 여객기 운항정시성 데이터(<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">정시</a>)
<p></p>
데이터 세트는 2013년 4월-10월 기간에 해당합니다. Azure Machine Learning Studio로 업로드하기 전에 데이터 세트가 다음과 같이 처리됩니다.
<ul>
  <li>데이터 세트는 미국 본토에서 비행이 가장 많은 공항 70곳만을 포함하도록 필터링되었습니다.</li>
  <li>취소된 비행은 15분 초과 지연으로 레이블이 지정되었습니다.</li>
  <li>우회 비행은 필터링되었습니다.</li>
  <li>다음 열이 선택되었습니다. Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled</li>
</ul>
</td>
</tr>

<tr>
  <td>비행 운항정시성(원시)</td>
  <td>
2011년 10월부터 미국 내의 비행 도착 및 출발 레코드입니다.
<p></p>
<b>사용:</b> 비행 지연을 예측합니다. 
<p></p>
<b>관련 조사:</b> 미국 교통부에서 <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td>산불 데이터</td>
  <td>
기온 및 습도 지수, 풍속 같은 날씨 데이터를 포함합니다. 산불 레코드와 결합된 포르투갈 북동부 지역에서 데이터를 가져옵니다.
<p></p>
<b>사용:</b> 이 작업은 산불 피해지를 예측하기 위한 어려운 회귀 작업입니다. 
<p></p>
<b>관련 조사:</b> Cortez, P., & Morais, A. (2008). UCI Machine Learning 리포지토리 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
<p></p>
[Cortez and Morais, 2007] P. Cortez and A. Morais. A Data Mining Approach to Predict Forest Fires using Meteorological Data. In J. Neves, M. F. Santos 및 J. Machado Eds., 인공 지능의 새로운 추세, 13회 EPIA 2007 - 인공 지능에 관한 포르투갈 컨퍼런스, 12월, 포르투갈 기마랑이스, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. 사용 가능: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr>
  <td>독일 신용 카드 UCI 데이터 세트</td>
  <td>
german.data 파일을 사용한 UCI Statlog(독일 신용 카드) 데이터 세트(<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data</a>).
<p></p>
데이터 세트는 특성 집합으로 설명된 사람을 낮은 신용 위험 또는 높은 신용 위험으로 분류합니다. 각 예제는 개인을 나타냅니다. 숫자 및 범주의 기능 20개와 이진 레이블(신용 위험 값)이 있습니다. 높은 신용 위험 항목의 레이블은 2이고, 낮은 신용 위험 항목의 레이블은 1입니다. 낮은 위험 예제를 높은 위험으로 잘못 분류한 비용은 1이지만, 높은 위험 예제를 낮은 위험으로 분류한 비용은 5입니다.
  </td>
</tr>

<tr>
  <td>IMDB 영화 제목</td>
  <td>
이 데이터 세트에는 Twitter 트윗에서 평가된 영화에 대한 정보가 포함됩니다. (IMDB 영화 ID, 영화 제목, 장르 및 제작 연도) 이 데이터 세트에는 170,000 개의 영화가 있습니다. 데이터 세트는 논문 "S. Dooms, T. De Pessemier 및 L. Martens. MovieTweetings: a Movie Rating Dataset Collected From Twitter. Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013"에서 소개되었습니다.
  </td>
</tr>

<tr>
  <td>붓꽃 2종 데이터</td>
  <td>
이 데이터베이스는 패턴 인식 문헌에서 찾을 수 있는 가장 잘 알려진 데이터베이스일 것입니다. 데이터 세트는 비교적 작으며, 세 가지 붓꽃 품종의 꽃잎 측정값별로 예제 50개를 포함합니다.
<p></p>
<b>사용:</b> 측정값에서 붓꽃 종류를 예측합니다.  
<p></p>
<b>관련 조사:</b> Fisher, R.A. (1988). UCI Machine Learning 리포지토리 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>영화 트윗</td>
  <td>
이 데이터 세트는 Movie Tweetings 데이터 세트의 확장된 버전입니다. 이 데이터 세트에는 잘 구성된 Twitter 트윗에서 추출한 170,000개의 영화 등급이 있습니다. 각 인스턴스는 트 윗을 나타내는 이며 튜플을: 사용자 ID, IMDB 영화 ID, 등급, 타임 스탬프,이 트 윗에 대 한 즐겨찾기 수 및 리 트이 윗의 수입니다. 이 데이터 세트는 A. Said, S. Dooms, B. Loni 및 D. Tikk가 Recommender Systems Challenge 2014를 위해 제공했습니다.
  </td>
</tr>

<tr>
  <td>다양한 자동차에 대한 MPG 데이터</td>
  <td>
이 데이터 세트는 Carnegie Mellon University의 StatLib 도서관에서 제공한 데이터 세트의 약간 수정된 버전입니다. 이 데이터 세트는 1983 American Statistical Association Exposition에서 사용되었습니다.
<p></p>
데이터는 다양한 자동차의 연료 소비량(m.p.g)을 나열합니다. 실린더 수, 엔진 배기량, 마력, 총 중량, 가속도 등의 정보도 포함됩니다.
<p></p>
<b>사용:</b> 다중 값 개별 특성 3개와 연속 특성 5개를 기반으로 연비를 예측합니다. 
<p></p>
<b>관련 조사:</b> StatLib, 카네기멜론대학교, (1993). UCI Machine Learning 리포지토리 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>피마 인디언 당뇨병 이진 분류 데이터 세트</td>
  <td>
NIDDK(National Institute of Diabetes and Digestive and Kidney Diseases) 데이터베이스의 데이터 하위 집합입니다. 데이터 세트는 피마 인디언 혈통의 여성 환자에게 초점을 맞추도록 필터링되었습니다. 데이터에는 포도당 및 인슐린 수준 같은 의료 데이터와 생활양식 요인이 포함됩니다.
<p></p>
<b>사용:</b> 대상에게 당뇨병이 있는지를 예측합니다(이진 분류). 
<p></p>
<b>관련 조사:</b> Sigillito, V. (1990). UCI Machine Learning 리포지토리 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml"</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>음식점 고객 데이터</td>
  <td>
인구 통계 및 선호도를 비롯한 고객에 대한 메타데이터 집합입니다.
<p></p>
<b>사용:</b> 이 데이터 세트를 다른 두 가지 음식점 데이터 세트와 함께 사용하여 추천 시스템을 학습 및 테스트합니다. 
<p></p>
<b>관련 조사:</b> Bache, K. 및 Lichman, M. (2013). UCI Machine Learning 리포지토리 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>음식점 기능 데이터</td>
  <td>
음식점 및 음식 종료, 식사 스타일, 위치 같은 기능에 대한 메타데이터 집합입니다.
<p></p>
<b>사용:</b> 이 데이터 세트를 다른 두 가지 음식점 데이터 세트와 함께 사용하여 추천 시스템을 학습 및 테스트합니다. 
<p></p>
<b>관련 조사:</b> Bache, K. 및 Lichman, M. (2013). UCI Machine Learning 리포지토리 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>음식점 등급</td>
  <td>
0-2점 사이에 사용자가 제공한 음식점 등급을 포함합니다.
<p></p>
<b>사용:</b> 이 데이터 세트를 다른 두 가지 음식점 데이터 세트와 함께 사용하여 추천 시스템을 학습 및 테스트합니다. 
<p></p>
<b>관련 조사:</b> Bache, K. 및 Lichman, M. (2013). UCI Machine Learning 리포지토리 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>강철 가열 냉각 다중 클래스 데이터 세트</td>
  <td>
이 데이터 세트는 강철 가열 냉각 시도에서 얻은 일련의 레코드를 포함합니다. 여기에는 결과 강철 유형의 물리적 특성(너비, 두께, 유형(코일, 시트 등))이 포함됩니다.
<p></p>
<b>사용:</b> 두 가지 숫자 클래스 특성인 경도 또는 강도 중 하나를 예측합니다. 특성 간의 상관 관계를 분석할 수도 있습니다.
<p></p>
강철 등급은 SAE 및 기타 조직에서 정의된 집합 표준을 따릅니다. 특정 '등급'(클래스 변수)을 찾고 있고 필요한 값을 파악하려고 합니다. 
<p></p>
<b>관련 조사:</b> Sterling, D. & Buntine, W. (NA). UCI Machine Learning 리포지토리 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
<p></p>
여기에서 강철 등급에 대한 유용한 지침을 확인할 수 있습니다. <a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>망원경 데이터</td>
  <td>
배경 소음과 함께 고에너지 감마 미립자 폭발에 대한 레코드로서, 두 항목 모두 Monte Carlo 프로세스를 사용하여 시뮬레이트됩니다.
<p></p>
시뮬레이션의 의도는 지상 대기 체렌코프 감마 망원경을 향상하는 것이었습니다. 통계적 방법을 사용하여 원하는 신호(체렌코프 방사선 샤워)와 배경 소음(상층부 대기의 우주 방사선에서 시작된 하드론 샤워)을 구분하면 됩니다.
<p></p>
데이터는 긴 축이 카메라 중심을 향하도록 하여 가늘고 긴 클러스터를 만들도록 전처리되었습니다. 이 타원의 특성(Hillas 매개 변수라고도 함)은 구분에 사용할 수 있는 이미지 매개 변수 중 하나입니다.
<p></p>
<b>사용:</b> 샤워 이미지가 신호 또는 배경 소음을 나타내는지를 예측합니다.
<p></p>
<b>참고:</b> 배경 이벤트를 신호로 분류하는 것은 신호 이벤트를 배경으로 분류하는 것보다 비효율적이므로 단순 분류 정확도는 이 데이터에 대해 의미가 없습니다. 다양한 분류자를 비교하려면 ROC Graph를 사용해야 합니다. 배경 이벤트를 신호로 수락할 가능성은 다음 임계값 중 하나보다 낮아야 합니다. 0.01, 0.02, 0.05, 0.1 또는 0.2.
<p></p>
또한 배경 이벤트 수(하드론 샤워의 경우 h)는 과소 예측되지만 실제 측정값에서 h 또는 소음 클래스가 대부분의 이벤트를 나타냅니다. 
<p></p>
<b>관련 조사:</b> Bock, R.K. (1995). UCI Machine Learning 리포지토리 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information </td>
</tr>

<tr>
  <td>날씨 데이터 세트</td>
  <td>
NOAA에서 제공한 시간별 지상 기상 관측(<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">201304부터 201310까지의 병합된 데이터</a>).
<p></p>
기상 데이터는 2013년 4월-10월 기간에 공항 기상 관측소에서 수행된 관측을 포함합니다. Azure Machine Learning Studio로 업로드하기 전에 데이터 세트가 다음과 같이 처리됩니다.
<ul>
  <li>기상 관측소 ID가 해당 공항 ID에 매핑되었습니다.</li>
  <li>비행이 가장 많은 공항 70곳과 연계되지 않은 기상 관측소가 필터링되었습니다.</li>
  <li>Date 열은 Year, Month 및 Day 열로 분할되었습니다.</li>
  <li>다음 열이 선택되었습니다. AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 데이터 세트</td>
  <td>
데이터는 XML 데이터로 저장되는 각 S&P 500 회사의 자료에 따라 Wikipedia(<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>)에서 파생됩니다.
<p></p>
Azure Machine Learning Studio로 업로드하기 전에 데이터 세트가 다음과 같이 처리됩니다.
<ul>
  <li>각 특정 회사에 대한 텍스트 콘텐츠 추출</li>
  <li>위치 형식 지정 제거</li>
  <li>영숫자가 아닌 문자 제거</li>
  <li>모든 텍스트를 소문자로 변환</li>
  <li>알려진 회사 범주가 추가됨</li>
</ul>
<p></p>
일부 회사의 경우 문서를 찾을 수 없으므로 레코드 수가 500개 미만입니다.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
데이터 세트에 고객 데이터 및 다이렉트 메일 캠페인 회신에 대한 내용이 포함됩니다. 각 행은 고객을 나타냅니다. 데이터 세트에 사용자 인구 통계와 과거 동작에 대한 9개의 기능 및 3개의 레이블 열(방문, 전환, 지출)이 포함됩니다.  방문은 마케팅 캠페인 이후 고객이 방문했음을 나타내는 이진 열입니다. 전환은 고객이 무언가를 구입했음을 나타냅니다. 지출은 소비된 금액입니다.  이 데이터 세트는 Kevin Hillstrom이 MineThatData E-Mail Analytics And Data Mining Challenge를 위해 제공했습니다.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
RCV1-V2 Reuters 뉴스 데이터 세트에 있는 테스트 예제 기능입니다. 이 데이터 세트에는 781,000개의 뉴스 기사가 해당 ID(데이터 세트의 첫 번째 열)와 함께 나옵니다. 각 기사는 토큰화, 중지 단어 지정, 형태소 분석됩니다. 이 데이터 세트는 David. D. Lewis가 제공했습니다.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
RCV1-V2 Reuters 뉴스 데이터 세트에 있는 학습 예제의 기능입니다. 이 데이터 세트에는 23,000개의 뉴스 기사가 해당 ID(데이터 세트의 첫 번째 열)와 함께 나옵니다. 각 기사는 토큰화, 중지 단어 지정, 형태소 분석됩니다. 이 데이터 세트는 David. D. Lewis가 제공했습니다.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
KDD Cup 1999 지식 검색 및 데이터 마이닝 도구 경합(<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>)의 데이터 세트.
<p></p>
데이터 세트가 다운로드되어 Azure Blob Storage(<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>)에 저장되었고 학습 및 테스트 데이터 세트를 둘 다 포함합니다. 학습 데이터 세트에는 레이블을 포함하여 약 126,000개 행과 43개 열이 있습니다. 3개 열은 레이블 정보 부분이며 숫자 및 문자열/범주 기능으로 구성된 40개 열은 모델 학습에 사용할 수 있습니다. 테스트 데이터에는 22,500개 정도 테스트 예제가 있고 학습 데이터에는 같은 열 43개가 있습니다.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
RCV1-V2 Reuters 뉴스 데이터 세트의 뉴스 기사에 대한 토픽 할당입니다. 하나의 뉴스 기사를 여러 항목에 할당할 수 있습니다. 각 행의 형식은 "&lt;항목 이름&gt; &lt;문서 ID&gt; 1"입니다. 이 데이터 세트에는 260만 개의 항목 할당이 포함됩니다. 이 데이터 세트는 David. D. Lewis가 제공했습니다.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
이 데이터는 KDD Cup 2010 학생 성취도 평가 챌린지(<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">학생 성취도 평가</a>)에서 가져옵니다. 사용된 데이터는 Algebra_2008_2009 학습 집합(Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010)입니다. Algebra I 2008-2009. KDD Cup 2010 교육 데이터 마이닝 챌린지의 챌린지 데이터 세트. <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>에서 찾아보세요.
<p></p>
데이터 집합이 다운로드되어 Azure Blob Storage(<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>)에 저장되었고 학생 교육 시스템의 로그 파일을 포함합니다. 제공된 기능에는 문제 ID 및 간략한 설명, 학생 ID, 타임스탬프, 올바른 방법으로 문제를 해결하기 전에 학생이 시도한 횟수가 포함됩니다. 원본 데이터 세트에는 8,900,000개 레코드가 있고 이 데이터 세트는 처음 100,000개 행으로 샘플을 줄였습니다. 데이터 세트에는 숫자, 범주, 타임스탬프 형식의 탭으로 구분된 23개 열이 있습니다.
  </td>
</tr>

</table>

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Kickstart 예제를 사용 하 여 실험](sample-experiments.md)

<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
