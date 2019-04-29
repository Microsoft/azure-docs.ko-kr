---
title: 렌더링 개요 - Azure Batch
description: 렌더링에 Azure 사용 및 Azure Batch 렌더링 기능 개요 소개
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: d4423b22c4c8afea5afa9c7040e081665b17ba87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774032"
---
# <a name="rendering-using-azure"></a>Azure를 사용한 렌더링

렌더링은 3D 모델을 가져와 2D 이미지를 변환하는 프로세스입니다. 3D 장면은 Autodesk 3ds Max, Autodesk Maya, Blender 같은 애플리케이션에서 제작됩니다.  Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray, Blender Cycles 등의 렌더링 애플리케이션은 2D 이미지를 생성합니다.  경우에 따라 장면 파일에서 단일 이미지가 생성됩니다. 그러나 여러 이미지를 모델링 및 렌더링한 다음, 애니메이션으로 결합하는 것이 일반적입니다.

렌더링 워크로드는 미디어 및 엔터테인먼트 산업에서 특수 효과(VFX)에 널리 사용되고 있습니다. 렌더링도 광고, 소매, 오일 및 가스, 제조와 같은 다른 많은 산업에서 사용 됩니다.

렌더링 프로세스는 계산이 많은 가정으로, 많은 프레임/이미지를 생성할 수 있고 각각의 이미지는 렌더링에 몇 시간이 걸릴 수도 있습니다.  따라서 렌더링은 Azure와 Azure Batch를 활용하여 여러 렌더링을 병렬로 처리할 수 있는 완벽한 일괄 처리 워크로드입니다.

## <a name="why-use-azure-for-rendering"></a>렌더링에 Azure를 사용하는 이유

렌더링이 Azure와 Azure Batch에 완벽하게 부합하는 워크로드인데는 여러 가지 이유가 있습니다.

* 렌더링 작업은 여러 VM을 사용하여 병렬로 실행할 수 있는 많은 부분으로 분할할 수 있습니다.
  * 애니메이션은 여러 프로그램으로 구성되며 각각의 프레임은 병렬로 렌더링할 수 있습니다.  각각의 프레임 처리에 사용할 수 있는 VM이 많을수록 모든 프레임과 애니메이션이 생성되는 속도는 더 빨라집니다. 
  * 일부 렌더링 소프트웨어에서는 단일 프레임을 타일이나 조각 같은 여러 부분으로 분할할 수 있습니다.  각 부분을 별도로 렌더링한 다음, 모든 부분이 완료되면 최종 이미지로 결합합니다.  사용할 수 있는 VM이 더 많을수록 프레임 렌더링 속도는 더 빨라집니다.
* 렌더링 프로젝트에는 엄청난 규모가 필요할 수 있습니다.
  * 개별 프레임이 복잡하여 최고 사양의 하드웨어로도 렌더링에 많은 시간이 소요될 수 있고, 애니메이션은 수십 만 프레임으로 구성될 수 있습니다.  합당한 시간에 고품질 애니메이션을 렌더링하려면 엄청난 계산 규모가 필요합니다.  경우에 따라 100,000개가 넘는 코어를 사용하여 수천 개의 프레임을 병렬로 렌더링하기도 했습니다.
* 렌더링 프로젝트는 프로젝트 기반이며 필요한 컴퓨팅 시간이 제각각입니다.
  * 필요 시 계산 및 스토리지 용량을 할당하고, 프로젝트 중에 로드에 따라 크기를 조정하여, 프로젝트가 완료되면 제거합니다.
  * 할당 시에는 용량을 결제하지만 프로젝트 사이 등과 같이 로드가 없을 때는 결제하지 않습니다.
  * 예기치 않은 변경에 따른 급증 상황을 서비스합니다. 프로젝트 후반에 예기치 않은 변화가 있고 그러한 변화를 빡빡한 일정에 맞게 처리해야 하는 경우 확대합니다.
* 애플리케이션, 워크로드, 기간에 따라 광범위한 하드웨어를 선택할 수 있습니다.
  * Azure에서 Batch로 할당 및 관리할 수 있는 하드웨어의 선택 사항은 다양합니다.
  * 프로젝트에 따라 요구 사항이 최적 가격/성능 또는 최적의 전체 성능에 대한 것이 될 수 있습니다.  서로 다른 장면 및/또는 렌더링 애플리케이션에서는 메모리 요구 사항이 각기 다릅니다.  일부 렌더링 애플리케이션은 GPU를 최고 성능을 위해 또는 특정 기능에 활용할 수 있습니다. 
* 우선 순위가 낮은 VM으로 비용 절감:
  * 우선 순위가 낮은 VM은 정식 주문형 VM에 비해 크게 낮은 가격으로 사용할 수 있고 일부 작업 형식에 적합합니다.
  * 우선 순위가 낮은 VM은 Azure Batch를 통해 할당할 수 있고 광범위한 요구 사항 집합을 서비스하기 위해 사용하는 방식에 대해서는 유연성이 제공됩니다.  Batch 풀은 전용 및 우선 순위가 낮은 VM 모두로 구성될 수 있고 언제든 VM 형식을 변경할 수 있습니다.

## <a name="options-for-rendering-on-azure"></a>Azure의 렌더링 옵션

다양한 Azure 기능을 렌더링 워크로드에 사용할 수 있습니다.  사용하는 기능은 기존 환경과 요구 사항에 따라 다릅니다.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>렌더링 관리 애플리케이션을 사용하는 기존 온-프레미스 렌더링 환경

가장 일반적인 경우는 PipelineFX Qube, Royal Render, Thinkbox Deadline 같은 렌더링 관리 애플리케이션에서 관리하는 기존 온-프레미스 렌더링 팜이 있는 경우입니다.  Azure VM을 사용하여 온-프레미스 렌더링 팜 용량을 확장하기 위한 요구 사항입니다.

렌더링 관리 소프트웨어는 Azure 지원을 기본으로 포함하거나 Azure 지원을 추가하는 플러그인을 제공합니다. 지원되는 렌더링 관리자 및 사용하는 기능에 대한 자세한 내용은 [렌더링 관리자 사용](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers)을 참조하세요.

### <a name="custom-rendering-workflow"></a>사용자 지정 렌더링 워크플로

VM이 기존 렌더링 팜을 확장하기 위한 요구 사항입니다.  Azure Batch 풀은 대규모 VM을 할당하고, 우선 순위가 낮은 VM을 사용하고 전체 가격 VM을 통해 동적으로 규모를 자동 조정하며, 인기 렌더링 애플리케이션에 대한 종량제 라이선스를 제공할 수 있습니다.

### <a name="no-existing-render-farm"></a>기존 렌더링 팜 없음

클라이언트 워크스테이션이 렌더링을 수행할 수 있지만 렌더링 워크로드가 증가하고 워크스테이션 기능만으로는 시간이 너무 오래 걸립니다.  Azure Batch를 사용하여 요청 시 렌더링 팜 계산을 할당하고 Azure 렌더링 팜에 렌더링 작업을 예약할 수 있습니다.

## <a name="azure-batch-rendering-capabilities"></a>Azure Batch 렌더링 기능

Azure Batch를 사용하면 Azure에서 병렬 워크로드를 실행할 수 있습니다.  애플리케이션이 설치 및 실행되는 VM을 많이 만들고 관리할 수 있습니다.  또한 그러한 애플리케이션의 인스턴스를 실행하는 종합적인 작업 예약 기능을 제공하므로 VM에 작업 할당, 큐에 넣기, 애플리케이션 모니터 등을 제공합니다.

Azure Batch은 많은 워크로드에 사용되지만 다음 기능은 특히 렌더링 워크로드를 더 빠르고 신속하게 처리하는 데 사용할 수 있습니다.

* 그래픽 및 렌더링 애플리케이션이 미리 설치된 VM 이미지:
  * 인기 그래픽 및 렌더링 애플리케이션을 포함하는 Azure Marketplace VM 이미지를 사용할 수 있으므로 애플리케이션을 직접 설치하거나 설치된 애플리케이션으로 자체 사용자 지정 이미지를 만들 필요가 없습니다. 
* 렌더링 애플리케이션에 대한 종량제 라이선스
  * 컴퓨팅 VM에 대한 결제 외에 분 단위로 애플리케이션을 결제하도록 선택할 수 있습니다. 이 경우 라이선스 구매나 애플리케이션에 대한 라이선스 서버 구성이 필요하지 않습니다.  사용에 대해 결제한다는 것은 고정 라이선스 수가 없으므로, 변화하는 예기치 않은 로드를 서비스할 수 있다는 뜻입니다.
  * 종량제 라이선스를 사용하지 않고 자체 라이선스로 사전 설치된 애플리케이션을 사용할 수도 있습니다. 이렇게 하려는 경우 대개 온-프레미스 또는 Azure 기반 라이선스 서버를 설치한 다음 Azure Virtual Network를 사용하여 렌더링 풀을 라이선스 서버에 연결합니다.
* 클라이언트 디자인 및 모델링 애플리케이션에 대한 플러그 인:
  * 플러그 인을 통해 최종 사용자는 Autodesk Maya 같은 클라이언트 애플리케이션에서 직접 Azure Batch를 사용하여 풀을 만들고, 작업을 제출하며, 더 많은 계산 용량을 사용하여 렌더링을 더 빠르게 수행할 수 있습니다.
* 렌더링 관리자 통합:
  * Azure Batch는 렌더링 관리 애플리케이션에 통합되며 또는 플러그인을 사용하여 Azure Batch 통합을 제공할 수 있습니다.

몇 가지 방법으로 Azure Batch를 사용할 수 있으며 모두 Azure Batch 렌더링에도 적용됩니다.

* API:
  * [REST](https://docs.microsoft.com/rest/api/batchservice), [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch), [Python](https://docs.microsoft.com/python/api/overview/azure/batch), [Java](https://docs.microsoft.com/java/api/overview/azure/batch) 또는 다른 지원되는 API를 통해 코드를 작성합니다.  개발자는 클라우드나 온-프레미스 여부에 관계없이 Azure Batch 기능을 기존 애플리케이션이나 워크플로에 통합할 수 있습니다.  예를 들어 [Autodesk Maya 플러그 인](https://github.com/Azure/azure-batch-maya)은 Batch Python API를 사용하여 Batch를 호출하고 풀을 만들어 관리하며 작업과 과제를 제출하고 상태를 모니터합니다.
* 명령줄 도구
  * [Azure 명령줄](https://docs.microsoft.com/cli/azure/) 또는 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)을 사용하여 Batch 사용을 스크립트로 작성할 수 있습니다.
  * 특히 Batch CLI 템플릿 지원을 사용하면 풀을 만들고 작업을 제출하는 것이 훨씬 쉽습니다.
* UI:
  * [Batch Explorer](https://github.com/Azure/BatchExplorer)는 Batch 계정의 관리 및 모니터를 허용하는 플랫폼 교차 지원 클라이언트 도구로, Azure Portal UI에 비해 더 풍부한 몇 가지 기능을 제공합니다.  풀 및 작업 템플릿 집합은 지원되는 각각의 애플리케이션에 맞추어져 제공되며 이를 통해 간편하게 풀을 만들고 작업을 제출할 수 있습니다.
  * Azure Portal을 사용하여 Azure Batch를 관리 및 모니터할 수 있습니다.
* 클라이언트 애플리케이션 플러그 인:
  * 플러그 인을 통해 클라이언트 디자인 및 모델링 애플리케이션 안에서 직접 Batch 렌더링을 사용하도록 할 수 있습니다. 플러그 인은 주로 현재 3D 모델에 대한 컨텍스트 정보를 통해 Batch Explorer 애플리케이션을 호출합니다.
  * 다음과 같은 플러그 인을 사용할 수 있습니다.
    * [Azure Batch for Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Azure Batch 렌더링 시작

Azure Batch 렌더링을 사용해 보려면 다음 소개 자습서를 참조하세요.

* [Batch 탐색기를 사용하여 Blender 장면 렌더링](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [Batch CLI를 사용하여 Autodesk 3ds Max 장면 렌더링](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>다음 단계

[이 문서에서](https://docs.microsoft.com/azure/batch/batch-rendering-applications) Azure Marketplace VM 이미지에 포함된 렌더링 애플리케이션 및 버전 목록을 결정합니다.