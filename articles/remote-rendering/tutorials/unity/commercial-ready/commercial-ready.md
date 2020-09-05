---
title: 상용 준비된 Azure Remote Rendering 애플리케이션 만들기
description: Azure Remote Rendering을 사용하여 상용 준비된 애플리케이션을 만들기 위한 전략 및 고려 사항입니다.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 0dad78ad76a870ea9f1db28a3cb5ccace5cd804f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510932"
---
# <a name="tutorial-creating-a-commercial-ready-azure-remote-rendering-application"></a>자습서: 상용 준비된 Azure Remote Rendering 애플리케이션 만들기

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 상용 애플리케이션 세션 관리
> * 청구 세션 추적
> * 세션 로드 시간에 대한 사용자 환경 최적화
> * 네트워크 대기 시간에 대한 고려 사항

## <a name="prerequisites"></a>필수 구성 요소

* 이 자습서는 [자습서: Azure Remote Rendering 및 모델 스토리지 보안](../security/security.md)을 기반으로 합니다.

## <a name="introduction-to-commercial-readiness"></a>상용 준비에 대한 소개

Azure Remote Rendering은 혼합 현실에서 수행할 수 있는 작업을 확장합니다. 기본 사항이 솔루션에 통합되면 솔루션이 안전하고 확장 가능하며 가치를 제공할 준비가 되도록 여러 가지 사항을 추가로 고려해야 합니다.

이 모듈에서는 상용 애플리케이션에 대해 고려해야 할 몇 가지 추가 기능을 소개합니다.

시스템 수준의 아키텍처 모범 사례에 대한 광범위한 개요는 다음을 참조하세요.

* [Azure 아키텍처 센터](https://docs.microsoft.com/azure/architecture/)
* [Azure 개발자를 위한 시작 가이드](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide)

## <a name="analytics"></a>분석

분석 도구를 통합하면 솔루션을 관리, 추적 및 개선하는 도움이 됩니다.

사용 가능한 분석 리소스에 대한 포괄적인 목록은 다음을 참조하세요.

* [Azure Analytics Services](https://azure.microsoft.com/product-categories/analytics/)

### <a name="tracking-usage-for-billing"></a>청구 사용량 추적

여러 내부 팀 또는 외부 클라이언트에서 Azure Remote Rendering 사용량을 추적하는 것은 특히 다중 테넌트 상황에서 중요한 고려 사항입니다.

이를 위해 Azure는 Azure Remote Rendering 서비스의 사용량을 각 클라이언트와 연결하는 리소스 태그 지정이라는 서비스를 제공합니다.

리소스 명명 및 태그 지정에 대한 자세한 내용을 알아보려면 다음을 시작하는 것이 좋습니다.

* [리소스 명명 및 태그 지정 의사 결정 가이드](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

### <a name="diagnostics"></a>진단

ETW(Windows용 이벤트 추적) 및 ETL(이벤트 추적 로깅)과 같은 강력한 도구를 사용하면, 애플리케이션 내에서 추적 이벤트를 쉽게 생성할 수 있으며, 상용 솔루션 배포에서 발생할 수 있는 네트워크, 콘텐츠 수집, 세션, 애플리케이션 및 기타 문제를 진단하는 데 도움이 됩니다.

자세한 내용은 다음을 참조하세요.

* [클라이언트 쪽 성능 추적 만들기](https://docs.microsoft.com/azure/remote-rendering/how-tos/performance-tracing)
* [ETW(Windows용 이벤트 추적) 데이터를 수집하는 방법](https://docs.microsoft.com/visualstudio/profiling/how-to-collect-event-tracing-for-windows-etw-data)
* [Windows 디바이스 포털 사용: 로깅](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal)

### <a name="usage-analysis"></a>사용량 분석

Azure Application Insights는 사용자가 Azure Remote Rendering 애플리케이션을 사용하는 방법을 이해하는 데 도움이 됩니다. 앱을 업데이트할 때마다 해당 앱이 사용자에게 효율적으로 작동한 정도를 평가하고 이에 따라 솔루션을 향상시킬 수 있습니다. 이 정보를 사용하면 다음 개발 주기에 대한 데이터 기반 결정을 내릴 수 있습니다.

자세한 내용은 다음을 참조하세요.

* [Application Insights를 사용하여 사용량 분석](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)

## <a name="fast-startup-time-strategies"></a>빠른 시작 시간 전략

사용 사례에는 애플리케이션 시작에서 3D 모델 보기까지 빠른 시작이 필요할 수 있습니다. 예를 들어 중요한 모임에서는 모든 것을 미리 준비하여 제대로 실행해야 합니다. 또 다른 예로, CAD 애플리케이션과 혼합 현실 간의 빠른 디자인 반복이 효율성의 핵심인 CAD 3D 모델 검토 단계가 있습니다.

Azure Remote Rendering에는 전처리된 3D 모델이 필요하며, Azure에서는 현재 세션을 만들고 렌더링할 모델을 로드하는 데 몇 분 정도 걸립니다. 이 프로세스를 가능한 한 원활하고 빠르게 수행하려면 3D 모델 데이터 및 ARR 세션을 미리 준비해야 합니다.

여기서 공유되는 제안 사항은 현재 표준 Azure Remote Rendering의 일부가 아니지만 더 빠른 시작 시간을 위해 직접 구현할 수 있습니다.

### <a name="initiate-early"></a>초기 시작

시작 시간을 줄이기 위해 가장 간단한 해결 방법은 사용자 워크플로에서 세션 만들기 및 초기화를 최대한 빨리 이동하는 것입니다. 한 가지 전략은 ARR 세션이 필요하다고 알려지는 즉시 세션을 초기화하는 것입니다. 사용자가 Azure Remote Rendering에서 사용할 3D 모델을 Azure Blob Storage에 업로드하기 시작하는 경우가 많습니다. 이 경우 세션 만들기 및 초기화를 3D 모델 업로드와 동시에 시작하여 두 작업 스트림이 병렬로 실행되도록 할 수 있습니다.

이 프로세스는 선택한 Azure Blob Storage 입력 및 출력 컨테이너가 Azure Remote Rendering 세션과 동일한 지역 데이터 센터에 있도록 하여 더 효율적으로 간소화할 수 있습니다.

### <a name="scheduling"></a>일정 계획

향후 Azure Remote Rendering이 필요하다고 생각되면 Azure Remote Rendering 세션을 시작하도록 특정 날짜와 시간을 예약할 수 있습니다.

이 옵션은 사용자가 3D 모델을 업로드하고 나중에 볼 시간을 예약할 수 있는 웹 포털을 통해 제공될 수 있습니다. 또한 [*표준*](../../../reference/vm-sizes.md) 또는 [*프리미엄*](../../../reference/vm-sizes.md) 렌더링과 같은 다른 기본 설정도 요청하는 데 적합합니다. 이상적인 크기를 자동으로 결정하기 어렵거나 Azure 지역에서 지정된 시간에 VM을 사용할 수 있는지 확인해야 하는 자산을 혼합하여 표시하려는 경우 *프리미엄* 렌더링이 적합할 수 있습니다.

### <a name="session-pooling"></a>세션 풀링

가장 까다로운 상황의 또 다른 옵션으로 하나 이상의 세션이 항상 만들어지고 초기화되는 세션 풀링이 있습니다. 이 경우 요청하는 사용자가 즉시 사용할 세션 풀을 만듭니다. 이 방법의 단점은 VM이 초기화되면 서비스에 대한 청구가 시작된다는 것입니다. 세션 풀을 항상 실행 상태로 유지하는 것이 비용 효율적이지 않을 수 있지만, 분석에 따라 최대 부하를 예측하거나 위의 일정 전략과 결합하여 세션이 필요한 시기를 예측하고 이에 따라 세션 풀을 확장/축소할 수 있습니다.

또한 이 전략은 *프리미엄* 복잡성 모델을 먼저 확인한 후에 *표준* 내에서 작동할 수 있는 경우와 같이 단일 사용자 세션 내에서 두 유형 사이를 전환하는 것이 훨씬 더 빠르므로 *표준* 및 *프리미엄* 세션 간의 선택을 더 역동적인 방식으로 최적화하는 데 도움이 됩니다. 이러한 사용자 세션이 매우 긴 경우 상당한 비용을 절감할 수 있습니다.

Azure Remote Rendering 세션에 대한 자세한 내용은 다음을 확인하세요.

* [Remote Rendering 세션](https://docs.microsoft.com/azure/remote-rendering/concepts/sessions)

## <a name="standard-vs-premium-server-size-routing-strategies"></a>표준 및 프리미엄 서버 크기 라우팅 전략

*표준* 또는 *프리미엄* 서버 크기를 만들지 여부를 선택해야 하는 경우 사용자 환경 및 엔드투엔드 시스템을 설계하는 데 어려움이 있습니다. *프리미엄* 세션만 사용할 수도 있지만, *표준* 세션은 Azure 컴퓨팅 리소스를 훨씬 적게 사용하고 *프리미엄*보다 저렴합니다. 이는 가능할 때마다 *표준* 세션을 사용하고 필요한 경우에만 *프리미엄*을 사용하도록 강력하게 동기를 부여합니다.

여기서는 세션 선택을 관리하려는 요구 사항을 해결하기 위해 최소에서 최대까지의 포괄적인 몇 가지 옵션을 제공합니다.

### <a name="use-only-standard-or-premium"></a>표준 또는 프리미엄만 사용

사용자의 요구 사항이 *항상* *표준* 및 *프리미엄* 사이의 임계값 아래로 떨어질 것이라고 확실한 경우 이를 통해 의사 결정이 크게 간소화됩니다. *표준*만 사용합니다. 로드된 자산의 총 복잡성 합계가 *표준* 세션에 비해 너무 복잡하여 거부되는 경우 사용자 환경에 미치는 영향은 매우 중요합니다.

마찬가지로 사용량의 많은 부분이 *표준* 및 *프리미엄* 사이의 임계값을 초과할 것이라고 예상되거나 비용이 사용 사례의 핵심 요소가 아닌 경우 항상 *프리미엄*을 선택하는 것도 간단하게 유지하기 위한 옵션입니다.

### <a name="ask-the-user"></a>사용자에게 문의

*표준* 및 *프리미엄*을 모두 지원하려는 경우 인스턴스화할 세션 유형을 결정하는 가장 쉬운 방법은 보려는 3D 자산을 선택할 때 사용자에게 요청하는 것입니다. 이 방법을 사용하는 경우 사용자는 3D 자산 또는 볼 수 있는 여러 자산의 복잡성을 이해해야 합니다. 일반적으로 이러한 이유로 추천되지 않습니다. 사용자가 잘못 선택하고 *표준*을 선택하면 결과 사용자 환경이 부적절한 순간에 손상될 수 있습니다.

### <a name="analyze-the-3d-model"></a>3D 모델 분석

비교적 간단한 또 다른 방법은 선택한 3D 자산의 복잡성을 분석하는 것입니다. 모델 복잡성이 *표준*에 대한 임계값보다 낮으면 *표준* 세션을 시작하고, 그렇지 않으면 *프리미엄* 세션을 시작합니다. 여기서는 궁극적으로 단일 세션을 사용하여 *표준* 세션의 복잡성 임계값을 초과하는 여러 모델을 볼 수 있으므로 동일한 세션을 여러 3D 자산의 시퀀스에 원활하게 사용할 수 없습니다.

### <a name="automatic-switching"></a>자동 전환

*표준* 및 *프리미엄* 세션 간의 자동 전환은 세션 풀링도 포함된 시스템 설계에서 많은 의미가 있을 수 있습니다. 이 전략은 리소스 사용률의 추가 최적화를 허용합니다. 사용자가 보기 위해 모델을 로드할 때 복잡성이 결정되고 세션 풀링 서비스에서 올바른 세션 크기가 요청됩니다.

## <a name="working-with-networks"></a>네트워크 작업

### <a name="diagnostics"></a>진단

Azure Remote Rendering에는 대기 시간이 짧은 고속 인터넷 연결이 필요합니다. 사용자 네트워크의 품질은 환경의 품질에 상당한 영향을 줄 수 있습니다. 클라이언트의 네트워크 구성이 서로 다를 수 있고 가금 네트워크 대기 시간이 적절하지 않은 경우에는 진단 도구가 중요합니다.  

일관된 고품질 환경을 제공할 수 있도록 하려면 서버 쪽 및 클라이언트 쪽 분석 도구를 Azure Remote Rendering 애플리케이션에 통합하는 것이 좋습니다. 이렇게 하면 클라이언트에서 발생할 수 있는 네트워크 문제를 진단하고 완화하는 데 필요한 정보를 얻을 수 있습니다.

### <a name="client-network-configurations"></a>클라이언트 네트워크 구성

다양한 엔터프라이즈 환경에 배포되는 강력한 협업 솔루션을 개발하는 데 있어 가장 큰 과제 중 하나는 클라이언트에서 사용할 수 있는 다양한 네트워크 토폴로지 및 엔터프라이즈 방화벽 구성에 대비하는 것입니다.

많은 기업에서 LAN 내의 모든 피어 투 피어 트래픽을 차단합니다. 이로 인해 자동 LAN 검색의 간단하고 간소화된 UX를 활용하여 혼합 현실 애플리케이션의 모든 검색된 인스턴스 간에 로컬 공유 세션을 설정하기가 어렵습니다.

다른 잠재적인 실패 지점으로 대부분의 TCP/IP 포트를 차단하는 대역폭 및 방화벽을 의도적으로 제한하도록 구성된 라우터가 있습니다.

익숙하지 않은 네트워크에서 Azure Remote Rendering을 사용하려는 경우 다음을 추천합니다.

* 사전 회의 검사 목록을 제공하여 네트워크 준비 상태를 평가합니다.
* 적절한 지역 데이터 센터에서 요청을 처리할 수 있는지 확인합니다.
* 모든 문제를 진단하는 데 충분한 시간을 허용합니다.
* 고대역폭 데이터 요금제를 사용하는 모바일 핫스폿을 백업으로 가져옵니다.

### <a name="end-to-end-bandwidth"></a>엔드투엔드 대역폭

Azure Remote Rendering VM과 최종 클라이언트 사이에 있을 수 있는 네트워크의 각 레그에 대한 대역폭 기능을 평가해야 합니다. Azure 데이터 센터에서 클라이언트의 ISP까지의 네트워크 세그먼트는 ISP에서 클라이언트까지의 제한 요소보다 더 제한적일 수 있습니다. 이러한 문제는 Blob 다운로드 속도 테스트를 사용하여 진단할 수 있습니다.

### <a name="bandwidth-competition"></a>대역폭 경쟁

혼합 현실 애플리케이션을 설계하는 경우 앱의 다양한 기능이 대역폭에 대해 Azure Remote Rendering과 경쟁할 수 있습니다. 가장 예기치 않은 예는 단일 회의실의 많은 참가자가 ARR을 동시에 사용하여 3D 자산을 볼 것으로 예상되는 경우입니다. 네트워크 데이터 흐름의 각 레그에는 결합된 모든 ARR 데이터 스트림의 총 합계를 전송할 수 있는 용량이 있어야 합니다.

다른 예로 스트림된 비디오, 다른 관련 콘텐츠의 동시 백그라운드 업로드 및 음성 채팅이 있습니다. 특히 참가자가 많고 시스템에서 중간 수준 접근 방식의 오디오 믹싱 서버가 아닌 분산된 피어 투 피어 방식을 사용하고 있는 경우가 있습니다.

네트워크 분석에 대한 자세한 내용은 다음을 확인하세요.

* [Azure Storage Blob 다운로드 속도 테스트](https://www.azurespeed.com/Azure/Download)
* [Azure 네트워크 왕복 대기 시간 통계](https://docs.microsoft.com/azure/networking/azure-network-latency)
* [서버 쪽 성능 추적](https://docs.microsoft.com/azure/remote-rendering/overview/features/performance-queries)
* [클라이언트 쪽 성능 추적](https://docs.microsoft.com/azure/remote-rendering/how-tos/performance-tracing)

## <a name="collaboration-considerations"></a>협업 고려 사항

Azure Remote Rendering의 가장 중요한 사용 중 일부는 동일한 3D 환경을 동시에 보는 여러 참가자 간의 협업과 관련이 있습니다. 이러한 공유 세션에서 각 참가자는 동일한 네트워크의 동일한 위치에 있는지 여부에 관계없이 각 참가자에게 고유한 Azure Remote Rendering 세션이 필요하다는 것을 인식해야 합니다.

이는 각 참가자가 실제로 서로 다른 유리한 관점에서 동일한 환경을 보고 있기 때문입니다. 즉, 이러한 각각의 관점에서 동일한 3D 자산을 동시에 렌더링해야 합니다.

### <a name="multiple-azure-remote-rendering-sessions"></a>여러 Azure Remote Rendering 세션

Azure Remote Rendering과의 공유 환경을 지원하려면 ARR 세션을 만들고 관리하기 위해 적절히 배치하는 시스템에서 여러 세션을 시작하도록 준비해야 합니다. 참가자가 지리적으로 분산되어 있으면 이러한 세션을 다른 Azure 데이터 센터에서 초기화해야 할 수 있습니다.

또한 시스템에서 하나 이상의 참가자가 현재 Azure Remote Rendering에서 지원하지 않거나 현재 사용 가능한 Azure Remote Rendering VM 인스턴스가 없는 지리적 지역에 있을 수 있는 가능성을 관리해야 합니다.

이 문서에서 설명하는 세션 풀링 및 기타 전략과 결합하면 여러 개의 동시 세션 관리를 더 효율적으로 간소화할 수 있습니다.

### <a name="azure-blob-storage-considerations"></a>Azure Blob Storage 고려 사항

모든 동시 ARR 세션은 변환된 모델을 보기 위해 동일한 SAS URI를 참조할 수 있습니다. 이렇게 하면 원하는 3D 자산을 한 번에 업로드하고 변환한 다음, 모든 세션에서 공유할 수 있습니다. 이는 참가자가 동일한 위치에 있고 Azure Remote Rendering 서버 및 사용자가 아닌 다른 데이터 센터에 있는 Azure Blob Storage와 관련된 성능 문제가 없는 동일한 데이터 센터를 사용하는 경우에 특히 그렇습니다.

3D 자산이 일반적으로 단일 보기 세션에 대해 업로드된 다음, 디자인 검토 세션에서와 같이 삭제되는 경우 Azure Remote Rendering 서버를 기준으로 하는 Azure Blob Storage의 지리적 지역도 그다지 중요하지 않습니다.

그러나 학습 사용 사례와 같이 반복적으로 사용되는 3D 자산의 경우 사용할 준비가 된 3D 자산을 Azure Remote Rendering을 사용하려는 각 지역 데이터 센터의 Blob 스토리지에 유지하는 것이 좋습니다. 이는 Azure Storage 중복성을 사용하여 자동화할 수 있습니다. CDN은 이러한 용도로도 사용되는 경우가 많지만 아직 Azure Remote Rendering을 위한 옵션이 아닙니다.

자세한 내용은 다음을 참조하세요.

* [혼합 현실의 공유 환경](https://docs.microsoft.com/windows/mixed-reality/shared-experiences-in-mixed-reality)
* [Azure Storage 중복성](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

## <a name="managing-model-access"></a>모델 액세스 관리

Azure Remote Rendering을 완벽하게 활용하려면 3D 모델을 관리하는 엔드투엔드 인프라를 신중하게 고려해야 합니다.

Azure Remote Rendering을 사용하면 큰 3D 자산을 보기 전에 혼합 현실 디바이스로 직접 전송할 필요가 없다는 장점이 있습니다.  또한 Azure Remote Rendering에서 사용할 수 있도록 3D 자산이 업로드되어 변환되면 여러 사용자가 3D 모델의 단일 인스턴스를 공유할 수 있습니다.

### <a name="considerations-for-3d-model-access"></a>3D 모델 액세스에 대한 고려 사항

모델 액세스 전략을 결정할 때 고려해야 할 몇 가지 주요 사항은 다음과 같습니다.

예상되는 사용 사례에 따라 사용자가 보려는 3D 자산을 선택할 수 있는 최상의 위치 또는 위치 조합을 결정합니다. 몇 가지 일반적인 옵션은 다음과 같습니다.

* 혼합 현실 환경 내에서 직접
* 도우미 웹 포털을 통해
* 도우미 데스크톱 또는 모바일 애플리케이션에서

동일한 3D 자산을 여러 번 업로드할 수 있는 사용 패턴이 있는 사용 사례의 경우 모델이 나중의 여러 선택에 대해 한 번만 전처리되도록 백 엔드는 ARR에서 사용하기 위해 이미 변환된 모델을 추적합니다. 디자인 검토의 예로 팀에서 공통의 원래 3D 자산에 액세스할 수 있는 경우가 있습니다. 각 팀 멤버는 작업 스트림의 특정 지점에서 ARR을 사용하여 모델을 검토해야 합니다. 그런 다음, 첫 번째 보기에서만 전처리 단계를 트리거합니다. 이후 보기에서는 SAS 출력 컨테이너에서 연결된 후처리 관련 파일을 조회합니다.

사용 사례에 따라 동일한 세션에서 함께 볼 각 3D 자산 또는 자산 그룹에 대해 올바른 Azure Remote Rendering 서버 크기(*표준* 또는 *프리미엄*)를 결정하고 잠재적으로 유지할 수 있습니다.  

### <a name="on-device-model-selection-list"></a>디바이스 내 모델 선택 목록

교육, 작업 지침 또는 마케팅 애플리케이션과 같은 많은 사용 사례의 경우 Azure Remote Rendering에서 일반적으로 볼 수 있는 3D 자산 세트는 매우 정적일 수 있습니다. 이러한 상황에서는 큐레이팅된 3D 자산 세트를 미리 변환하여 큐레이팅된 자산의 선택 목록을 채우는 데 필요한 정보가 포함된 데이터베이스를 통해 사용할 수 있습니다. 그런 다음, 혼합 현실 애플리케이션에서 이 데이터를 검색하여 선택 메뉴를 채울 수 있습니다.

또한 이 경우 각 개인 또는 그룹에 고유한 프라이빗 3D 자산을 업로드하는 방법을 제공하여 한 단계 더 수행할 수 있습니다. 그러면 보려는 3D 자산을 선택하는 사용자 환경에서 해당 프라이빗 자산 목록을 공통의 큐레이팅된 자산 목록과 결합할 수 있습니다.

### <a name="on-device-onedrive-access"></a>디바이스 내 OneDrive 액세스

OneDrive 파일 선택기가 기본적으로 Microsoft의 혼합 현실 디바이스에 제공되면 OneDrive의 디바이스 내에서 3D 자산을 선택하는 것이 다르거나 수정된 3D 모델을 일반적으로 로드하는 사용 사례에 특히 유용합니다. 이 시나리오에서 사용자는 혼합 현실 애플리케이션 내에서 OneDrive 파일 선택기를 통해 하나 이상의 3D 자산을 선택합니다. 그런 다음, 3D 자산은 SAS 입력 컨테이너로 마이그레이션되고, SAS 출력 컨테이너로 변환되며, ARR 세션에 연결됩니다. 이상적으로는 혼합 현실 애플리케이션에서 모든 비트를 OneDrive에서 디바이스로 이동하고 Azure Blob Storage로 다시 이동하는 것이 아니라 클라우드 기반 프로세스를 호출하여 이러한 단계를 수행합니다.

이 방법은 OneDrive에서 동일한 모델을 다시 선택하면 애플리케이션에서 변환 프로세스를 무시하고 SAS URI를 통해 변환된 관련 3D 자산을 직접 로드할 수 있도록 이전에 본 3D 자산 간의 연결을 유지하여 한 단계를 더 수행할 수 있습니다.

자세한 내용은 다음을 참조하세요.

* [OneDrive를 Azure Storage에 복제하는 Microsoft Power Automate 템플릿](https://flow.microsoft.com/galleries/public/templates/2f90b5d3-029b-4e2e-ad37-1c0fe6d187fe/when-a-file-is-uploaded-to-onedrive-copy-it-to-azure-storage-container/)
* [OneDrive 파일 스토리지 API 개요](https://docs.microsoft.com/graph/onedrive-concept-overview)

### <a name="direct-cad-access"></a>직접 CAD 액세스

혼합 현실에 대한 한 가지 매력적인 사용 사례는 진행 중인 CAD 작업의 디자인 검토입니다. 이 시나리오의 경우 데스크톱에서 혼합 현실로 가장 빨리 로드하는 시간이 중요합니다. 이상적인 솔루션에는 특정 CAD 애플리케이션용 플러그 인을 개발하는 것과 관련이 있습니다. 이러한 플러그 인은 로드, 변환 및 보기 프로세스의 모든 측면을 직접 관리합니다.

* UX를 제공하여 다음을 수행합니다.
  * CAD 애플리케이션을 특정 혼합 현실 디바이스와 페어링합니다(1회).
  * 선택한 기하 도형을 해당 혼합 현실 디바이스에서 볼 수 있도록 요청합니다.
* 아직 실행되지 않은 경우 CAD 파일을 업로드 및 변환하는 동안 병렬로 처리할 수 있도록 Azure Remote Rendering 세션을 스핀업합니다.
* CAD 기하 도형 데이터를 Azure Remote Rendering에서 지원하는 형식 중 하나로 정규화합니다.
* 정규화된 데이터를 Azure Blob Storage 입력 컨테이너로 직접 전송합니다.
* 모델 변환 프로세스를 시작합니다.
* 모델의 출력 컨테이너 SAS URI를 Azure Remote Rendering 세션에 연결합니다.
* 모델을 사용할 수 있고 볼 준비가 되었음을 페어링된 혼합 현실 애플리케이션에 알리고, 애플리케이션에서 세션에 연결할 수 있도록 출력 컨테이너 SAS URI를 제공합니다.

훨씬 간단하지만 약간 비효율적으로 간소화된 방법은 3D 모델을 로컬 하드 드라이브에 저장하는 프로세스를 자동화한 다음, 저장된 파일을 SAS 입력 컨테이너로 전송하는 프로세스를 시작할 수 있습니다.

### <a name="azure-marketplace"></a>Azure Marketplace

많은 엔터프라이즈 클라이언트에서는 보안상의 이유로 자체의 Azure 계정 및 자격 증명으로 Azure Stack을 배포할 수 있어야 합니다. 이를 가능하게 하기 위해 Azure Marketplace에서 Azure 관리형 애플리케이션을 Azure 애플리케이션 제안으로 게시할 수 있도록 해당 애플리케이션을 패키지하는 것이 좋습니다.

자세한 내용은 다음을 참조하세요.

* [Azure Marketplace](https://azure.microsoft.com/marketplace/)
* [자습서: Marketplace에서 Azure 관리형 애플리케이션 게시](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-marketplace-app)

### <a name="security"></a>보안

처음부터 보안을 위해 엔드투엔드 Azure Remote Rendering 솔루션을 구축해야 합니다. 엔드투엔드 솔루션을 설계할 때 고려해야 할 많은 보안 측면은 다음과 같습니다.

* 인증 전략
* 액세스 관리 – 그룹, 정책 및 권한
* 다중 테넌트 지원
* 데이터 저장 및 전송 암호화
* 임시 사용 토큰
* DDoS(분산 서비스 거부) 공격
* 위협 감지
* VPN 및 보안 네트워크
* 방화벽
* 인증서 및 비밀 키 관리
* 애플리케이션 취약성 및 악용

인증을 위해 가능한 한 많은 ARR 인증 및 세션 관리를 Azure Web Service로 이동하는 것이 좋습니다. 이렇게 하면 더 효율적으로 관리되고 더 안전한 솔루션이 생성됩니다.

자세한 내용은 다음을 참조하세요.

* [Azure AD 서비스 인증](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-service-authentication)
* [Azure를 사용하여 보안 상태 강화](https://azure.microsoft.com/overview/security/)
* [클라우드 보안](https://azure.microsoft.com/product-categories/security/)
