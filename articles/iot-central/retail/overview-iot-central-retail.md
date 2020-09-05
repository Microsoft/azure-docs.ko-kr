---
title: Azure IoT Central을 사용하여 소매업 솔루션 빌드 | Microsoft Docs
description: Azure IoT Central 애플리케이션 템플릿을 사용하여 연결된 물류, 디지털 배포 센터, 스토어 내 분석, 조건 모니터링, 체크 아웃, 스마트 재고 관리 및 소매업 솔루션을 빌드하는 방법을 알아봅니다.
author: avneet723
ms.author: avneets
ms.date: 01/10/2020
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 3cae2902385eb82f65243c631745df5ca48825f3
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719229"
---
# <a name="building-retail-solutions-with-azure-iot-central"></a>Azure IoT Central을 사용하여 소매업 솔루션 빌드

Azure IoT Central은 엔터프라이즈급 IoT 솔루션의 개발, 관리 및 유지 관리와 관련된 부담 및 비용을 줄이는 IoT 앱 플랫폼입니다. Azure IoT Central을 사용하여 빌드하기로 선택하면 복잡하고 지속적으로 진화하는 IoT 인프라를 단순히 유지 관리하는 데서 그치지 않고 IoT 데이터를 활용하여 비즈니스를 전환하는 데 시간, 비용 및 에너지를 집중할 수 있습니다.

이 문서에서는 몇 가지 소매업 관련 IoT Central 애플리케이션 템플릿에 대해 설명합니다. 솔루션 빌더는 이러한 템플릿을 활용하여 공급망을 최적화하고, 고객의 스토어 내 환경을 개선하고, 재고를 더 효율적으로 추적하는 IoT 솔루션을 빌드할 수 있습니다.

:::image type="content" source="media/overview-iot-central-retail/retail-app-templates.png" alt-text="Azure IoT 소매업 개요":::

다음 섹션에서는 이러한 애플리케이션 템플릿의 기능에 대해 설명합니다.

## <a name="connected-logistics"></a>연결된 물류

글로벌 물류 지출은 2020년에 10.6조 달러에 이를 것으로 예상됩니다. 상품 운송은 이 지출의 대부분을 차지하며, 상품 운송업체는 극심한 경쟁 압력과 제약을 받고 있습니다.

IoT 센서를 사용하여 온도, 습도, 기울기, 충격, 조명 및 배송 위치와 같은 주변 조건을 수집하고 모니터링할 수 있습니다. IoT 센서 및 디바이스에서 수집한 원격 분석을 다른 데이터 원본(예: 클라우드 기반 비즈니스 인텔리전스 시스템의 날씨 및 교통 정보)과 결합할 수 있습니다.

연결된 물류 솔루션의 이점은 다음과 같습니다.

* 실시간 추적 및 추적을 통한 배송 모니터링 
* 실시간 주변 조건 모니터링을 통한 배송 무결성
* 배송 도난, 분실 또는 손상으로부터의 보안
* 지오펜싱, 경로 최적화, 차량 관리 및 차량 분석
* 배송에 대한 예측 가능한 출발 및 도착 예측

다음 스크린샷에서는 애플리케이션 템플릿의 기본 대시보드를 보여 줍니다. 대시보드는 특정 솔루션 요구 사항을 충족하도록 완벽하게 사용자 지정할 수 있습니다.

:::image type="content" source="media/overview-iot-central-retail/connected-logistics-dashboard1.png" alt-text="연결된 물류 대시보드":::

:::image type="content" source="media/overview-iot-central-retail/connected-logistics-dashboard2.png" alt-text="연결된 물류 대시보드":::

자세한 내용은 [연결된 물류 애플리케이션 템플릿 배포 및 살펴보기](./tutorial-iot-central-connected-logistics.md) 자습서를 참조하세요.

## <a name="digital-distribution-center"></a>디지털 배포 센터

제조업체와 소매점이 전 세계적에 진출함에 따라 공급망도 확장되고 더 복잡해졌습니다. 소비자는 이제 사용할 수 있는 상품을 다양하게 선택할 수 있고, 해당 상품이 구매 후 1~2일 이내에 도착할 것이라고 기대합니다. 물류 센터는 기존의 비효율성을 극복하면서 이러한 추세에 적응해야 합니다.

오늘날 육체 노동에 의존하는 집품 및 포장(picking and packing)이 배포 센터 비용의 55-65%를 차지하고 있습니다. 또한 수동 집품 및 포장은 일반적으로 자동화된 시스템보다 느리고, 인력 충원 요구 사항이 급변하므로 배송량을 맞추기가 훨씬 더 어렵습니다. 이러한 계절적 변동으로 인해 직원 이직률이 높고 비용이 많이 드는 오류가 발생할 가능성이 높아집니다.

IoT 사용 카메라를 기반으로 하는 솔루션은 디지털 피드백 루프를 사용하여 혁신적인 이점을 누릴 수 있습니다. 배포 센터 전반의 데이터는 실행 가능한 인사이트를 끌어내어 결과적으로 더 나은 데이터를 생성합니다.

디지털 배포 센터의 이점은 다음과 같습니다.

* 상품이 도착하고 컨베이어 시스템을 통과할 때 카메라를 통해 상품 모니터링
* 불량 상품의 자동 식별
* 효율적인 주문 추적
* 비용 절감, 생산성 향상 및 사용량 최적화.

다음 스크린샷에서는 애플리케이션 템플릿의 기본 제공 대시보드를 보여 줍니다. 대시보드는 특정 솔루션 요구 사항을 충족하도록 완벽하게 사용자 지정할 수 있습니다. 

:::image type="content" source="media/overview-iot-central-retail/digital-distribution-center-dashboard.png" alt-text="디지털 물류 센터 대시보드":::

자세한 내용은 [디지털 배포 센터 애플리케이션 템플릿 배포 및 살펴보기](./tutorial-iot-central-digital-distribution-center.md) 자습서를 참조하세요.

## <a name="in-store-analytics---condition-monitoring"></a>스토어 내 분석 - 조건 모니터링

많은 소매점의 경우 스토어 내 환경 조건은 경쟁업체와의 주요 차별화 요소입니다. 소매점은 고객의 이익을 위해 스토어 내에서 쾌적한 환경을 유지하려고 합니다.  

솔루션 빌더는 IoT Central 스토어 내 분석 조건 모니터링 애플리케이션 템플릿을 사용하여 엔드투엔드 솔루션을 빌드할 수 있습니다. 애플리케이션 템플릿을 사용하면 다양한 종류의 센서 디바이스를 통해 소매점 스토어 환경을 디지털로 연결하고 모니터링할 수 있습니다. 이러한 센서 디바이스는 원격 분석을 생성하여 비즈니스 인사이트로 변환함으로써 소매점에서 운영 비용을 절감하고 고객에게 훌륭한 환경을 제공할 수 있습니다.

이 애플리케이션 템플릿을 사용하여 다음을 수행할 수 있습니다.

* 다양한 IoT 센서를 IoT Central 애플리케이션 인스턴스에 연결합니다.
* 센서 네트워크 및 환경의 모든 게이트웨이 디바이스의 상태를 모니터링하고 관리합니다.
* 스토어 관리자에게 경고를 트리거하도록 스토어 내 환경 조건에 대한 사용자 지정 규칙을 만듭니다.
* 스토어 내 환경 조건을 소매점 팀에서 고객 환경을 개선하는 데 사용할 수 있는 인사이트로 변환시킵니다.
* 집계된 인사이트를 기존 또는 새 비즈니스 애플리케이션으로 내보내 소매점 직원에게 유용하고 시기 적절한 정보를 제공합니다.

애플리케이션 템플릿은 디바이스 템플릿 세트와 함께 제공되며, 시뮬레이션된 디바이스 세트를 사용하여 대시보드를 채웁니다. 

다음 스크린샷에서는 애플리케이션 템플릿의 기본 제공 대시보드를 보여 줍니다. 대시보드는 특정 솔루션 요구 사항을 충족하도록 완벽하게 사용자 지정할 수 있습니다. 

:::image type="content" source="media/overview-iot-central-retail/in-store-analytics-condition-dashboard.png" alt-text="스토어 내 분석 조건 모니터링":::

자세한 내용은 [Azure IoT Central에서 스토어 내 분석 애플리케이션 만들기](./tutorial-in-store-analytics-create-app.md) 자습서를 참조하세요.

## <a name="in-store-analytics---checkout"></a>스토어 내 분석 - 체크 아웃

일부 소매점의 경우 스토어 내 체크 아웃 환경은 경쟁업체와의 주요 차별화 요소입니다. 소매점은 원활한 스토어 내 체크 아웃 환경을 제공하여 고객이 다시 방문하도록 조장하려고 합니다.  

솔루션 빌더는 IoT Central 스토어 내 분석 체크 아웃 애플리케이션 템플릿을 사용하여 소매점 직원에게 스토어의 체크 아웃 영역 주변의 인사이트를 제공하는 솔루션을 빌드할 수 있습니다. 예를 들어 센서는 각 체크 아웃 레인의 대기열 길이와 평균 대기 시간에 대한 정보를 제공할 수 있습니다.

이 애플리케이션 템플릿을 사용하여 다음을 수행할 수 있습니다.

* 다양한 IoT 센서를 IoT Central 애플리케이션 인스턴스에 연결합니다.
* 센서 네트워크 및 환경의 모든 게이트웨이 디바이스의 상태를 모니터링하고 관리합니다.
* 소매점 직원에게 경고를 트리거하도록 스토어 내 체크 아웃 조건에 대한 사용자 지정 규칙을 만듭니다.
* 스토어 내 체크 아웃 조건을 소매점 팀에서 고객 환경을 개선하는 데 사용할 수 있는 인사이트로 변환시킵니다.
* 집계된 인사이트를 기존 또는 새 비즈니스 애플리케이션으로 내보내 소매점 직원에게 유용하고 시기 적절한 정보를 제공합니다.

애플리케이션 템플릿은 디바이스 템플릿 세트와 함께 제공되며, 시뮬레이션된 디바이스 세트를 사용하여 대시보드를 레인 점유 데이터로 채웁니다. 

다음 스크린샷에서는 애플리케이션 템플릿의 기본 제공 대시보드를 보여 줍니다. 대시보드는 특정 솔루션 요구 사항을 충족하도록 완벽하게 사용자 지정할 수 있습니다. 

:::image type="content" source="media/overview-iot-central-retail/In-Store-Analytics-Checkout-Dashboard.png" alt-text="스토어 내 분석 체크 아웃":::

자세한 내용은 [Azure IoT Central에서 스토어 내 분석 애플리케이션 만들기](./tutorial-in-store-analytics-create-app.md) 자습서를 참조하세요.

## <a name="smart-inventory-management"></a>스마트 재고 관리

재고는 소매점에서 보유하고 있는 상품의 재고입니다. 재고 관리는 적절한 제품이 적시에 올바른 위치에 있는지 확인하는 데 중요합니다. 소매점은 너무 많은 재고를 저장하는 비용과 수요를 충족시키는 데 충분한 재고 품목을 유지하지 않는 비용을 균형 있게 조정해야 합니다.

RFID(전파 식별) 태그, 오류 신호(beacon) 및 카메라에서 생성된 IoT 데이터는 재고 관리 프로세스를 개선할 수 있는 기회를 제공합니다. IoT 센서 및 디바이스에서 수집한 원격 분석을 다른 데이터 원본(예: 클라우드 기반 비즈니스 인텔리전스 시스템의 날씨 및 교통 정보)과 결합할 수 있습니다.

스마트 재고 관리의 이점은 다음과 같습니다.

* 재고가 없는 품목의 위험 감소 및 원하는 고객 서비스 수준 보장 
* 거의 실시간으로 재고 정확도에 대한 심층 분석 및 인사이트
* 고객 주문에 충족시키기 위해 보관할 적절한 재고량을 결정하는 데 도움이 되는 도구

이 애플리케이션 템플릿은 디바이스를 연결하고, RFID 및 BLE(Bluetooth 저에너지) 판독기 디바이스를 구성하고 관리하는 데 중점을 두고 있습니다.

다음 스크린샷에서는 애플리케이션 템플릿의 기본 제공 대시보드를 보여 줍니다. 대시보드는 특정 솔루션 요구 사항을 충족하도록 완벽하게 사용자 지정할 수 있습니다.

:::image type="content" source="media/overview-iot-central-retail/smart-inventory-management-dashboard.png" alt-text="스마트 재고 관리 대시보드":::

자세한 내용은 [스마트 재고 관리 애플리케이션 템플릿 배포 및 살펴보기](./tutorial-iot-central-smart-inventory-management.md) 자습서를 참조하세요.

## <a name="micro-fulfillment-center"></a>마이크로 주문 처리 센터

경쟁이 점점 더 치열해지고 있는 소매 환경에서 소매점은 수요와 주문 처리 사이의 격차를 해소해야 한다는 압박에 끊임없이 직면하고 있습니다. 증가하는 소비자 수요를 처리하기 위해 등장한 새로운 추세는 최종 고객과 이들이 방문하는 스토어 근처에 재고를 비축하는 것입니다.

IoT Central 마이크로 주문 처리 센터 애플리케이션 템플릿을 사용하면 솔루션 빌더가 완전히 자동화된 주문 처리 센터의 모든 측면을 모니터링하고 관리할 수 있습니다. 템플릿에는 솔루션 개발 프로세스를 가속화하기 위한 시뮬레이션된 조건 모니터링 센서 및 로봇 캐리어 세트가 포함되어 있습니다. 이러한 센서 디바이스는 의미 있는 신호를 포착하여 비즈니스 인사이트로 변환함으로써 소매점에서 운영 비용을 절감하고 고객을 위한 환경을 제공할 수 있습니다.

이 애플리케이션 템플릿을 사용하면 다음을 수행할 수 있습니다. 

- 로봇 또는 조건 모니터링 센서와 같은 다양한 종류의 IoT 센서를 IoT Central 애플리케이션 인스턴스에 원활하게 연결합니다.
- 센서 네트워크 및 환경의 모든 게이트웨이 디바이스의 상태를 모니터링하고 관리합니다.
- 적절한 경고를 트리거하도록 주문 처리 센터 내 환경 조건에 대한 사용자 지정 규칙을 만듭니다.
- 주문 처리 센터 내 환경 조건을 소매점 스토어 팀에서 활용할 수 있는 인사이트로 변환시킵니다.
- 소매점 직원 멤버의 이익을 위해 집계된 인사이트를 기존 또는 새 비즈니스 애플리케이션으로 내보냅니다.

다음 스크린샷에서는 애플리케이션 템플릿의 기본 제공 대시보드를 보여 줍니다. 대시보드는 특정 솔루션 요구 사항을 충족하도록 완벽하게 사용자 지정할 수 있습니다.

:::image type="content" source="media/overview-iot-central-retail/MFC-Dashboard.png" alt-text="마이크로 주문 처리 센터":::

자세한 내용은 [마이크로 주문 처리 센터 애플리케이션 템플릿 배포 및 연습](./tutorial-micro-fulfillment-center.md) 자습서를 참조하세요.

## <a name="video-analytics---object-and-motion-detection"></a>비디오 분석 - 개체 및 동작 감지

*IoT Central 비디오 분석 - 개체 및 동작 감지* 애플리케이션 템플릿을 사용하면 인텔리전트 에지 카메라를 사용하여 개체 및 동작을 감지하는 솔루션을 배포, 관리 및 모니터링하는 방법을 신속하게 경험할 수 있습니다.

비디오 분석 애플리케이션은 IoT Edge에서 실행되는 [LVA(live video analytics)](#live-video-analytics) 모듈을 사용합니다. LVA 모듈은 에지 및 클라우드를 포괄하는 인텔리전트 비디오 애플리케이션을 빌드하기 위한 플랫폼을 제공합니다. 플랫폼을 사용하여 개체 및 동작 감지를 통해 비디오 분석 애플리케이션과 같은 IoT 솔루션을 향상시킬 수 있습니다.

애플리케이션 템플릿에는 네 가지 애플리케이션 대시보드가 포함되어 있습니다.

* **시작**에서는 애플리케이션 템플릿 사용을 시작하는 데 도움이 되는 리소스에 대한 링크를 제공합니다.
* **데모 대시보드**는 연결된 카메라에서 표시할 수 있는 정보 유형에 대한 설명을 제공합니다.
* **(샘플) 실제 카메라 관리**는 시뮬레이션된 카메라를 사용하여 애플리케이션에서 카메라를 관리하는 방법을 보여줍니다.
* **(샘플) 실제 카메라 모니터**는 시뮬레이션된 카메라를 사용하여 애플리케이션에서 카메라를 모니터링하는 방법을 보여줍니다.

:::image type="content" source="media/overview-iot-central-retail/live-video-analytics.png" alt-text="비디오 분석 - 개체 및 동작 감지":::

솔루션의 아키텍처에 대해 자세히 알아보려면 [비디오 분석 애플리케이션 아키텍처](architecture-video-analytics.md)를 참조하세요.

솔루션을 배포하는 방법에 대한 자세한 내용은 [Azure IoT Central에서 비디오 분석 애플리케이션 만들기](tutorial-video-analytics-deploy.md) 자습서를 참조하세요.

### <a name="live-video-analytics"></a>라이브 비디오 분석

[라이브 비디오 분석](https://github.com/Azure/live-video-analytics)은 에지 및 클라우드를 포괄하는 인텔리전트 비디오 애플리케이션을 빌드하기 위한 플랫폼을 제공합니다. 이 플랫폼은 라이브 비디오를 캡처, 녹화 및 분석하는 기능과 비디오 또는 비디오 분석일 수 있는 결과를 Azure 서비스에 게시하는 기능을 제공합니다. Azure 서비스는 클라우드 또는 에지에서 실행할 수 있습니다. 이 플랫폼을 사용하여 비디오 분석을 통해 IoT 솔루션을 개선할 수 있습니다.

## <a name="next-steps"></a>다음 단계

소매업 솔루션 빌드를 시작하려면 다음을 수행합니다.

* 스토어 내 분석 애플리케이션 템플릿 중 하나를 사용하여 솔루션을 빌드하는 방법을 안내하는 [Azure IoT Central에서 스토어 내 분석 애플리케이션 만들기](./tutorial-in-store-analytics-create-app.md) 자습서를 시작합니다.
* [연결된 물류 애플리케이션 템플릿 배포 및 살펴보기](./tutorial-iot-central-connected-logistics.md)
* [디지털 배포 센터 애플리케이션 템플릿 배포 및 살펴보기](./tutorial-iot-central-digital-distribution-center.md)
* [스마트 재고 관리 애플리케이션 템플릿 배포 및 살펴보기](./tutorial-iot-central-smart-inventory-management.md)
* [마이크로 주문 처리 센터 애플리케이션 템플릿 배포 및 연습](./tutorial-micro-fulfillment-center.md)
* [비디오 분석 애플리케이션 템플릿을 배포하고 살펴봅니다](./tutorial-video-analytics-deploy.md).
* [IoT Central 개요](../preview/overview-iot-central.md)에서 IoT Central에 대해 자세히 알아봅니다.
