---
title: 고객 이탈 분석
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (클래식)를 사용 하 여 고객 이탈을 분석 하 고 점수를 매기는 통합 모델을 개발 하는 사례 연구.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/18/2017
ms.openlocfilehash: 79a7b157d5daf8940aba454808c65ed433cfe29c
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169003"
---
# <a name="analyze-customer-churn-using-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (클래식)를 사용 하 여 고객 이탈 분석
## <a name="overview"></a>개요
이 문서에서는 Azure Machine Learning Studio (클래식)를 사용 하 여 빌드된 고객 이탈 분석 프로젝트의 참조 구현을 제공 합니다. 이 문서에서는 산업 고객 이탈 문제를 전체적으로 해결하기 위한 관련된 일반 모델을 알아봅니다. 또한 Machine Learning을 사용하여 빌드된 모델의 정확도를 측정하고 향후 배포를 위한 방향을 평가합니다.  

### <a name="acknowledgements"></a>감사의 말
이 실험은 Serge Berger, Microsoft의 Principal Data 과학자, Microsoft의 Roger Barga, 이전에 Microsoft Azure Machine Learning Studio (클래식)에 대 한 제품 관리자에 의해 개발 되 고 테스트 되었습니다. Azure 설명서 팀은 담당자들의 전문 지식을 인정하고 이 백서를 공유한 것에 대해 감사해하고 있습니다.

> [!NOTE]
> 이 실험에 사용된 데이터는 공개적으로 사용할 수 없습니다. 이탈 분석을 위한 Machine Learning 모델을 작성하는 방법의 예제를 보려면 [Azure AI 갤러리](https://gallery.azure.ai/Collection/Retail-Customer-Churn-Prediction-Template-1)의 [소매 변동 모델 템플릿](https://gallery.azure.ai/)을 참조하세요.
> 
> 



## <a name="the-problem-of-customer-churn"></a>고객 이탈 문제
고객 시장과 모든 기업 부문의 비즈니스에서는 이탈을 처리해야 합니다. 때때로 이탈이 지나치고 정책 의사 결정에 영향을 미칠 수도 있습니다. 기존 솔루션에서는 이탈 가능성이 큰 고객을 예측하고 안내자 서비스, 마케팅 캠페인 또는 특별 관리 적용을 통해 해당 고객의 요구를 해결합니다. 이러한 접근 방식은 업계에 따라 달라질 수 있습니다. 또한 한 업계(예: 통신) 내의 특정 고객 클러스터에 따라 달라질 수 있습니다.

공통적인 요인은 비즈니스에서 이러한 특별 고객 유지 노력을 최소화해야 한다는 점입니다. 따라서 모든 고객에 대해 이탈 가능성을 점수로 매겨 상위 N명에 초점을 맞추는 것이 자연스러운 방법입니다. 상위 고객은 이윤이 가장 높은 고객일 수도 있습니다. 예를 들어 더 정교한 시나리오에서는 특별 관리 후보를 선택할 때 이윤 함수를 사용합니다. 하지만 이러한 고려 사항은 변동을 다루는 전체 전략의 일부일 뿐입니다. 비즈니스에서는 위험(및 관련 위험 허용 범위), 개입 수준 및 비용, 타당한 고객 구분도 고려해야 합니다.  

## <a name="industry-outlook-and-approaches"></a>업계 전망 및 접근법
정교한 이탈 처리는 성숙한 산업의 표시입니다. 대표적인 예로는 가입자가 공급자를 빈번히 전환하는 것으로 알려진 통신 업계를 들 수 있습니다. 이 자발적인 이탈이 주된 관심사입니다. 또한 공급자는 고객 전환을 유도하는 요인인 *이탈 동인*에 대한 상당한 정보를 축적했습니다.

예를 들어 송수화기나 디바이스 선택은 휴대폰 비즈니스에서 잘 알려진 이탈 동인입니다. 따라서 신규 가입자를 위해 송수화기 보조금을 지급하고 업그레이드 시 기존 고객에게 전체 가격을 청구하는 정책이 널리 사용됩니다. 역사적으로 이 정책은 고객이 공급자를 변경할 때 새로운 할인을 얻을 것이라고 기대하게 합니다. 결국 이에 따라 공급자는 해당 전략을 다듬게 됩니다.

송수화기 제품의 높은 변동성은 현재 송수화기 모델을 기반으로 한 변동 모델을 빠르게 무효화하는 요소입니다. 또한 휴대 전화는 통신 디바이스일 뿐만 아니라 패션이기도 합니다(예: iPhone). 이러한 소셜 예측자는 일반적인 통신 데이터 집합의 범위를 벗어납니다.

모델링의 최종적인 결론은 이탈에 대한 알려진 이유를 제거함으로써 타당한 정책을 고안할 수 없다는 것입니다. 실제로 의사 결정 트리와 같이 범주형 변수를 수량화하는 대표적인 모델을 비롯하여 연속 모델링 전략은 **필수**입니다.

조직에서는 고객에 대한 빅데이터 집합을 사용하여 효과적인 문제 접근법으로 빅데이터 분석, 특히 빅데이터를 기반으로 한 이탈 감지를 수행하고 있습니다. ETL 권장 사항 섹션에서 이탈 문제에 대한 빅데이터 접근법을 자세히 알아볼 수 있습니다.  

## <a name="methodology-to-model-customer-churn"></a>고객 이탈 모델링 방법
그림 1~3에서는 고객 이탈을 해결하기 위한 일반적인 문제 해결 프로세스를 보여 줍니다.  

1. 위험 모델에서는 작업이 가능성과 위험에 어떤 영향을 미치는지를 고려할 수 있습니다.
2. 개입 모델에서는 개입 수준이 이탈 가능성과 CLV(Customer Lifetime Value) 크기에 어떤 영향을 미치는지를 고려할 수 있습니다.
3. 이 분석은 최적의 제품을 제공하기 위한 고객층을 대상으로 하는 사전 마케팅 캠페인으로 확대되는 정성 분석에 적합합니다.  

![위험 허용 오차와 의사 결정 모델이 실행 가능한 인사이트를 산출하는 방법을 보여주는 다이어그램](./media/azure-ml-customer-churn-scenario/churn-1.png)

이러한 미래 예측 접근법은 이탈을 처리하는 가장 좋은 방법이지만 복잡성 문제가 있습니다. 따라서 다중 모델 원형을 개발하고 모델 간의 종속성을 추적해야 합니다. 모델 간의 조작은 다음 다이어그램과 같이 캡슐화할 수 있습니다.  

![이탈 모델 상호 작용 다이어그램](./media/azure-ml-customer-churn-scenario/churn-2.png)

*그림 4: 통합 다중 모델 원형*  

고객 유지에 대한 전체적 접근법을 제공하려면 모델 간의 상호 작용이 핵심 요소입니다. 각 모델은 시간에 지나면서 가치가 저하되므로 아키텍처는 암시적 루프입니다(CRISP-DM 데이터 마이닝 표준, [***3***]에 의해 설정된 원형과 비슷함).  

위험 의사 결정 마케팅 구분/해체의 전체 주기는 대부분 비즈니스 문제에 적용할 수 있는 범용화된 구조입니다. 이탈 분석은 간소화된 예측 솔루션을 허용하지 않는 복잡한 비즈니스 문제의 모든 특성을 보이므로 이 문제 그룹의 강력한 대표 사례입니다. 이탈에 대한 현대적 접근법의 사회적 측면은 접근법에서 특별히 강조되지 않지만 사회적 측면은 모든 모델에서와 같이 모델링 원형에서 캡슐화됩니다.  

여기서 또 다른 흥미로운 요소는 빅데이터 분석입니다. 오늘날 통신 및 소매 비즈니스에서는 고객에 대한 방대한 데이터를 수집하며, 사물 인터넷(Internet of Things) 및 유비쿼터스 디바이스와 같이 새롭게 떠오르는 추세를 고려해 볼 때 비즈니스가 여러 계층에서 스마트 솔루션을 채택할 수 있도록 하는 다중 모델 연결에 대한 필요가 일반적인 추세가 될 것임을 쉽게 예측할 수 있습니다.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio-classic"></a>Machine Learning Studio 모델링 원형 (클래식) 구현
설명된 문제를 감안할 때 통합 모델링 및 채점 방식을 구현하는 가장 좋은 방법은 무엇일까요? 이 섹션에서는 Azure Machine Learning Studio (클래식)를 사용 하 여이를 수행 하는 방법을 설명 합니다.  

다중 모델 접근법은 이탈에 대한 전역적인 원형을 디자인할 때 필수 요소입니다. 접근법의 점수 매기기(예측) 부분도 다중 모델이어야 합니다.  

다음 다이어그램에서는 변동를 예측 하기 위해 Machine Learning Studio (클래식)에서 4 개의 점수 매기기 알고리즘을 채택 하는 만든 프로토타입을 보여 줍니다. 다중 모델 접근법은 전체 분류자를 만들어 정확도를 높일 뿐만 아니라 과잉 맞춤을 방지하고 예측 기능 선택을 개선하기 위해 사용합니다.  

![상호 연결 된 모듈이 많은 복잡 한 Studio (클래식) 작업 영역을 보여 주는 스크린샷](./media/azure-ml-customer-churn-scenario/churn-3.png)

*그림 5: 일탈 모델링 접근법의 프로토타입*  

다음 섹션에서는 Machine Learning Studio (클래식)를 사용 하 여 구현한 프로토타입 점수 매기기 모델에 대 한 자세한 정보를 제공 합니다.  

### <a name="data-selection-and-preparation"></a>데이터 선택 및 준비
모델을 빌드하고 고객 점수를 매기는 데 사용되는 데이터는 CRM Vertical 솔루션에서 얻고 고객 개인 정보를 보호하도록 데이터가 난독 처리됩니다. 이 데이터에는 미국의 구독 회원 8,000명에 대한 정보가 포함되어 있으며, 세 가지 소스 데이터, 즉 프로비저닝 데이터(구독 메타데이터), 활동 데이터(시스템 사용) 및 고객 지원 데이터가 통합되어 있습니다. 로열티 메타데이터 또는 신용 점수와 같은 고객에 대한 비즈니스 관련 정보는 데이터에 포함되지 않습니다.  

간단히 말해 데이터 준비가 이미 다른 곳에서 완료되었다고 가정하므로 ETL 및 데이터 정리 프로세스는 범위를 벗어납니다.

모델링에 대한 기능 선택은 임의 포리스트 모듈을 사용하는 프로세스에 포함된 예측 변수 집합에 대한 사전 중요도 점수 매기기를 기반으로 합니다. Machine Learning Studio (클래식)의 구현에서는 대표 기능의 평균, 중앙값 및 범위를 계산 했습니다. 예를 들어 사용자 활동에 대한 최소값 및 최대값 같은 정성 데이터에 대한 집계를 추가했습니다.

또한 최근 6개월 동안의 임시 정보를 캡처했습니다. 1년 동안의 데이터를 분석하고 통계상 중요한 추세라도 6개월 후에는 이탈에 대한 영향이 크게 감소하도록 설정했습니다.  

가장 중요 한 점은 ETL, 기능 선택 및 모델링을 비롯 한 전체 프로세스가 Microsoft Azure의 데이터 원본을 사용 하 여 Machine Learning Studio (클래식)에서 구현 된다는 것입니다.   

다음 다이어그램에서는 사용된 데이터를 보여 줍니다.  

![원시 값으로 사용된 데이터 샘플을 보여주는 스크린샷](./media/azure-ml-customer-churn-scenario/churn-4.png)

*그림 6: 데이터 원본 발췌(난독 처리됨)*  

![데이터 원본에서 추출한 통계 기능을 보여주는 스크린샷](./media/azure-ml-customer-churn-scenario/churn-5.png)

*그림 7: 데이터 원본에서 추출된 기능*
 

> 이 데이터는 프라이빗 데이터이므로 모델 및 데이터를 공유할 수 없습니다.
> 그러나 공개적으로 사용할 수 있는 데이터를 사용하는 유사한 모델의 경우 [Azure AI 갤러리](https://gallery.azure.ai/): [Telco 고객 변동](https://gallery.azure.ai/Experiment/31c19425ee874f628c847f7e2d93e383)에서 이 샘플 실험을 참조하세요.
> 
> Cortana Intelligence 제품군을 사용하여 변동 분석을 구현하는 방법에 대한 자세한 내용을 알아보려면 선임 프로그램 관리자인 Wee Hyong Tok의 [이 비디오](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) 를 시청하는 것이 좋습니다. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>프로토타입에 사용된 알고리즘
프로토타입(사용자 지정 아님)을 빌드하는 데 사용한 네 가지 기계 학습 알고리즘은 다음과 같습니다.  

1. LR(로지스틱 회귀)
2. BT(향상된 의사 결정 트리)
3. AP(Averaged Perceptron)
4. SVM(Support Vector Machine)  

다음 다이어그램에서는 모델이 생성된 시퀀스를 나타내는 실험 디자인 화면의 일부를 보여 줍니다.  

![스튜디오 실험의 작은 섹션의 스크린샷 캔버스](./media/azure-ml-customer-churn-scenario/churn-6.png)  

*그림 8: Machine Learning Studio에서 모델 만들기 (클래식)*  

### <a name="scoring-methods"></a>점수 매기기 방법
레이블이 지정된 학습 데이터 세트를 사용하여 네 가지 모델의 점수를 매겼습니다.  

또한 데스크톱 버전의 SAS Enterprise Miner 12를 사용하여 빌드한 비교 가능한 모델에 점수 매기기 데이터 세트를 제출했습니다. SAS 모델 및 4 개의 Machine Learning Studio (클래식) 모델의 정확도를 측정 했습니다.  

## <a name="results"></a>결과
이 섹션에서는 점수 데이터 세트에 따라 모델 정확도에 대한 결과를 제공합니다.  

### <a name="accuracy-and-precision-of-scoring"></a>점수 매기기의 정확도 및 정밀도
일반적으로 Azure Machine Learning Studio (클래식)의 구현은 정확도가 10-15% (곡선 아래의 영역 또는 내부 영역)를 기준으로 합니다.  

그러나 이탈의 가장 중요한 메트릭은 오분류 비율입니다. 즉, 분류자를 통해 예측된 상위 N명의 이탈자 가운데 실제로 이탈하지 **않았지만** 특별 대우를 받은 비율(%)입니다. 다음 다이어그램에서는 모든 모델에 대한 이 오분류 비율을 비교합니다.  

![4가지 알고리즘의 성능을 비교하는 곡선 그래프 아래 영역](./media/azure-ml-customer-churn-scenario/churn-7.png)

*그림 9: Passau 프로토타입 AUC(Area Under Curve)*

### <a name="using-auc-to-compare-results"></a>AUC를 사용하여 결과 비교
AUC(Area Under Curve)는 긍정적 및 부정적 모집단에 대한 점수 분포 간에 *분리성* 의 전역 측정값을 나타내는 메트릭입니다. 이는 기존 ROC(Receiver Operator Characteristic) 그래프와 비슷하지만 한 가지 중요한 차이는 AUC 메트릭은 임계값을 선택할 필요가 없다는 것입니다. 대신에 AUC 메트릭은 **모든** 가능한 선택 항목에 대한 결과를 요약합니다. 이와 달리 기존 ROC 그래프는 세로축의 긍정적 비율, 가로축의 긍정적 오류 비율 및 분류 임계값을 보여 줍니다.   

AUC 값을 통해 모델을 비교할 수 있으므로 AUC는 다양한 알고리즘(또는 다양한 시스템)에 대한 가치의 측정값으로 사용됩니다. 이는 기상학 및 생물공학과 같은 산업에서 일반적인 접근법입니다. 따라서 AUC는 분류자 성능을 평가하는 데 널리 사용되는 대표적인 도구입니다.  

### <a name="comparing-misclassification-rates"></a>오분류 비율 비교
8,000여 개 구독에 대한 CRM 데이터를 사용하여 해당 데이터 세트에 대한 오분류 비율을 비교했습니다.  

* SAS 오분류 비율은 10~15%였습니다.
* Machine Learning Studio (클래식) 잘못 된 분류 요금은 상위 200-300 탈자의 15-20%입니다.  

통신 업계에서는 안내자 서비스나 기타 특별 대우를 제공하여 이탈 위험이 가장 큰 고객에게만 초점을 맞춰야 합니다. 이와 관련 하 여 Machine Learning Studio (클래식) 구현은 SAS 모델과의 결과를 달성 합니다.  

주요 관심사는 잠재 이탈자를 제대로 분류하는 것이었기 때문에 같은 이유로 정확도가 정밀도보다 더 중요합니다.  

Wikipedia의 다음 다이어그램에서는 효과적이고 이해하기 쉬운 그래픽을 보여 줍니다.  

![과녁이 두 개 있습니다. 한 과녁에는 적중 표시가 서로 떨어져 있지만 과녁의 중앙에 가까이 있고, “Low accuracy: good trueness, poor precision”(낮은 정확도: 양호한 충실도, 낮은 정밀도)라고 표시되어 있습니다. 다른 과녁에는 적중 표시가 가까이 모여 있지만 과녁의 중앙에서 멀리 있고 "Low accuracy: poor trueness, good precision"(낮은 정확도: 낮은 충실도, 양호한 정밀도)라고 표시되어 있습니다.](./media/azure-ml-customer-churn-scenario/churn-8.png)

*그림 10: 정확도와 정밀도 간의 균형 유지*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>향상된 의사 결정 트리 모델의 정확도 및 정밀도 결과
다음 차트에는 네 가지 모델 가운데 가장 정확할 수 있는 향상된 의사 결정 트리 모델에 대해 Machine Learning 프로토타입을 사용한 점수 매기기의 원시 결과가 나와 있습니다.  

![네 가지 알고리즘에 대한 정확도, 정밀도, 회수율, F-점수, AUC, 평균 로그 손실 및 교육 로그 손실을 보여주는 표](./media/azure-ml-customer-churn-scenario/churn-9.png)

*그림 11: 향상된 의사 결정 트리 모델 특성*

## <a name="performance-comparison"></a>성능 비교
Machine Learning Studio (클래식) 모델을 사용 하 여 데이터 점수가 매겨진 속도와 SAS Enterprise 마이너 12.1 데스크톱 버전을 사용 하 여 만든 동급 모델을 비교 했습니다.  

다음 표에서는 알고리즘의 성능을 간략히 설명합니다.  

*표 1. 알고리즘의 일반 성능(정확도)*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| 평균 모델 |최적 모델 |성능 미만 |평균 모델 |

Machine Learning Studio (클래식)에서 호스트 되는 모델은 실행 속도에 대해 15-25% 에%를 수행 하지만 정확도는 거의 동일 합니다.  

## <a name="discussion-and-recommendations"></a>설명 및 권장 사항
통신 업계에는 다음과 같이 이탈을 분석하기 위한 여러 가지 사례가 나타났습니다.  

* 네 가지 기본 범주에 대한 메트릭을 도출합니다.
  * **엔터티(예: 구독)** . 이탈할 가능성이 있는 구독 및/또는 고객에 대한 기본 정보를 프로비전합니다.
  * **활동**. 엔터티와 관련된 가능한 모든 사용 정보(예: 로그인 횟수)를 얻습니다.
  * **고객 지원**. 구독에 고객 지원에 대한 조작 또는 문제가 있는지를 나타내기 위해 고객 지원 로그에서 정보를 수집합니다.
  * **경쟁 및 비즈니스 데이터**. 고객에 대해 가능한 모든 정보를 얻습니다(예: 사용할 수 없거나 추적하기 어려운 정보).
* 기능 선택의 기준으로는 중요도를 사용합니다. 이는 향상된 의사 결정 트리 모델이 항상 유망한 접근법임을 의미합니다.  

이러한 네 가지 범주 사용은 이탈 위험이 있는 고객을 식별하는 데 있어 범주별로 타당한 요소에 대해 형성된 인덱스를 기반으로 하는 단순 *결정적* 접근법만으로 충분하다는 착각을 일으킵니다. 불행히도 이 생각은 타당한 것처럼 보이지만 잘못된 이해입니다. 이탈은 일시적 영향이고 일반적으로 이탈을 가져오는 요인은 과도 상태이기 때문입니다. 오늘 고객이 이탈을 고려하게 하는 요인은 내일은 달라질 수 있고 지금부터 6개월 후에는 확실히 달라집니다. 따라서 *확률적* 모델이 필요합니다.  

비즈니스 인텔리전스 지향 접근법이 주로 판매가 더 쉽고 직관적인 자동화를 허용하기 때문에 분석보다 이 접근법을 선호하는 비즈니스에서는 이 중요한 관찰 결과가 종종 간과됩니다.  

그러나 Machine Learning Studio (클래식)를 사용 하 여 셀프 서비스 분석을 사용 하는 것은 부서와 부서 별로 등급이 변동 된 네 가지 정보 범주가 변동에 대 한 기계 학습에 중요 한 원본이 될 수 있다는 것입니다.  

Azure Machine Learning Studio (클래식)에서 제공 하는 또 다른 흥미로운 기능은 이미 사용 가능한 미리 정의 된 모듈의 리포지토리에 사용자 지정 모듈을 추가 하는 기능입니다. 이 기능은 기본적으로 라이브러리를 선택하고 수직적 시장에 대한 템플릿을 만들 기회를 제공합니다. 이는 시장에서 Azure Machine Learning Studio (클래식)의 중요 한 차이점입니다.  

특히 빅데이터 분석과 관련된 이 토픽은 나중에 설명할 기회가 있을 것입니다.
  

## <a name="conclusion"></a>결론
이 문서에서는 일반 프레임워크를 사용하여 일반적인 문제인 고객 이탈을 방지하기 위한 합리적인 접근법에 대해 설명합니다. 모델 점수 매기기를 위해 프로토타입을 고려 하 고 Azure Machine Learning Studio (클래식)를 사용 하 여 구현 했습니다. 마지막으로 SAS의 비슷한 알고리즘과 비교하여 프로토타입 솔루션의 정확도와 성능을 평가했습니다.  

 

## <a name="references"></a>참조
[1] 예측 분석: 예측, W McKnight, 정보 관리, 7 월/8 월 2011, p. 18-20.  

[2] 위키백과 문서: [정확도 및 전체 자릿수](https://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Step-by-Step Data Mining Guide](https://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Big Data Marketing: Engage Your Customers More Effectively and Drive Value](https://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [Azure AI 갤러리](https://gallery.azure.ai/Experiment/Telco-Customer-Churn-5)의 [Telco 변동 모델 템플릿](https://gallery.azure.ai/) 
 

## <a name="appendix"></a>부록
![이탈 프로토타입에 대한 프레젠테이션 스냅샷](./media/azure-ml-customer-churn-scenario/churn-10.png)

*그림 12: 이탈 프로토타입에 대한 프레젠테이션 스냅샷*
