---
title: Virtual Machines, Cloud Services 및 Web Apps의 자동 크기 조정 개요
description: Microsoft Azure의 자동 크기 조정입니다. Virtual Machines, 가상 머신 확장 집합, Cloud Services 및 Web Apps에 적용됩니다.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: robb
ms.component: autoscale
ms.openlocfilehash: 05f20aec536ebdb702caea37051a65af9bbc659f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787600"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Microsoft Azure Microsoft Azure Virtual Machines, Cloud Services 및 Web Apps에서 자동 크기 조정 개요
이 문서에서는 Microsoft Azure 자동 크기 조정에 대해 설명하고 그 이점과 사용 방법을 소개합니다.  

Azure Monitor 자동 크기 조정은 [가상 컴퓨터 확장 집합](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/) 및 [API Management 서비스](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)에만 적용됩니다.

> [!NOTE]
> Azure에는 두 개의 자동 크기 조정 메서드가 있습니다. 이전 버전의 자동 크기 조정은 Virtual Machines(가용성 집합)에 적용됩니다. 이 기능은 제한적으로 지원하므로 빠르고 안정적인 자동 크기 조정 지원을 위해 가상 머신 확장 집합으로 마이그레이션하는 것이 좋습니다. 이전 기술을 사용하는 방법에 대한 링크는 이 문서에 포함됩니다.  
>
>

## <a name="what-is-autoscale"></a>자동 크기 조정이란?
자동 크기 조정을 사용하면 애플리케이션의 부하를 처리하기 위해 적절한 양의 리소스가 실행되도록 할 수 있습니다. 그러면 늘어난 부하를 처리하기 위해 리소스를 추가하고 유휴 상태의 리소스를 제거하여 비용을 절약할 수 있습니다. 실행할 최소/최대 인스턴스 수를 지정하고 규칙 집합을 기준으로 VM을 자동으로 추가 또는 제거합니다. 최소값을 설정하면 애플리케이션이 항상 부하가 적은 상태에서 실행될 수 있습니다. 최대값을 설정하면 가능한 총 시간 비용이 제한됩니다. 만든 규칙을 사용하여 이러한 두 극한값 사이를 자동으로 조정합니다.

 ![자동 크기 조정이 설명되었습니다. VM 추가 및 제거](./media/autoscale-overview/AutoscaleConcept.png)

규칙 조건이 충족되면 하나 이상의 자동 크기 조정 동작이 트리거됩니다. VM을 추가 및 제거하거나 다른 작업을 수행할 수 있습니다. 다음 개념 다이어그램에서는 이 프로세스를 보여 줍니다.  

 ![자동 크기 조정 흐름 다이어그램](./media/autoscale-overview/Autoscale_Overview_v4.png)

다음 설명은 이전 다이어그램의 부분에 적용됩니다.   

## <a name="resource-metrics"></a>리소스 메트릭
리소스에서 메트릭을 내보내며, 이러한 메트릭은 나중에 규칙에 따라 처리됩니다. 메트릭은 다른 메서드를 통해 제공됩니다.
Web Apps 및 클라우드 서비스의 원격 분석은 Azure 인프라에서 직접 제공되는 반면 가상 머신 확장 집합에서는 Azure 진단 에이전트의 원격 분석 데이터를 사용합니다. 일부 자주 사용되는 통계는 CPU 사용량, 메모리 사용량, 스레드 수, 큐 길이 및 디스크 사용량을 포함합니다. 사용할 수 있는 원격 분석 데이터 목록은 [자동 크기 조정 공통 메트릭](../../azure-monitor/platform/autoscale-common-metrics.md)을 참조하세요.

## <a name="custom-metrics"></a>사용자 지정 메트릭
애플리케이션에서 내보낼 수 있는 사용자 지정 메트릭을 활용할 수도 있습니다. Application Insights에 메트릭을 보내도록 애플리케이션을 구성한 경우 이러한 메트릭을 활용하여 크기 조정 여부를 결정할 수 있습니다.

## <a name="time"></a>Time
일정 기반 규칙은 UTC 기준으로 합니다. 규칙을 설정할 때 표준 시간대를 올바르게 설정해야 합니다.  

## <a name="rules"></a>규칙
다이어그램에서는 자동 크기 조정 규칙을 하나만 표시하지만 여러 개 있을 수도 있습니다. 상황에 필요한 대로 겹치는 복잡한 규칙을 만들 수 있습니다.  규칙 형식은 다음을 포함합니다.  

* **메트릭 기반** - 예를 들어 CPU 사용률이 50%보다 높으면 이 작업을 수행합니다.
* **시간 기반** - 예를 들어 지정된 표준 시간대에 토요일 오전 8시마다 웹후크를 트리거합니다.

메트릭 기반의 규칙은 애플리케이션 부하를 측정하고 해당 부하를 기반으로 VM을 추가 또는 제거합니다. 일정 기반의 규칙을 사용하면 부하 시간에서 패턴이 있는 경우 부하가 늘어나거나 줄어들기 전에 크기를 조정할 수 있습니다.  

## <a name="actions-and-automation"></a>작업 및 자동화
규칙은 하나 이상의 형식을 가진 작업을 트리거할 수 있습니다.

* **크기 조정** - VM을 감축 또는 확장합니다.
* **메일** - 구독 관리자, 공동 관리자 및 지정한 추가 메일 주소로 메일을 보냅니다.
* **Webhook 통해 자동화** - Azure의 내부 또는 외부에서 여러 복잡한 작업을 트리거할 수 있는 웹후크를 호출합니다. Azure에서 Azure Automation runbook, Azure Function 또는 Azure Logic App을 시작할 수 있습니다. Azure 외부의 타사 URL 예제는 Slack 및 Twilio와 같은 서비스를 포함합니다.

## <a name="autoscale-settings"></a>자동 크기 조정 설정
자동 크기 조정에서는 다음 용어와 구조를 사용합니다.

- **자동 크기 조정 설정** 은 자동 크기 조정 엔진에서 크기를 조정할지 여부를 결정하는 데 사용됩니다. 여기에는 하나 이상의 프로필, 대상 리소스에 대한 정보 및 알림 설정이 포함됩니다.

  - **자동 크기 조정 프로필**은 다음 항목의 조합입니다.

    - **용량 설정**은 인스턴스 수의 최소값, 최대값 및 기본값을 나타냅니다.
    - **일련의 집합**은 각 트리거(시간 또는 미터법) 및 크기 조정 작업(위쪽 또는 아래쪽)을 포함합니다.
    - **되풀이**는 자동 크기 조정에서 이 프로필을 적용할 시기를 지정합니다.

      여러 프로필을 설정하여 겹치는 다양한 요구 사항을 해결할 수 있습니다. 예를 들어 서로 다른 날짜 또는 요일에 대해 서로 다른 자동 크기 조정 프로필을 적용할 수 있습니다.

  - **알림 설정** 은 자동 크기 조정 설정의 한 프로필에 대한 조건을 만족하여 자동 크기 조정 이벤트가 발생할 때 발생하는 알림을 정의합니다. 자동 크기 조정은 하나 이상의 메일 주소에 알리거나 하나 이상의 웹후크를 호출할 수 있습니다.


![Azure 자동 크기 조정 설정, 프로필 및 규칙 구조](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

구성 가능한 필드 및 설명의 전체 목록은 [자동 크기 조정 REST API](https://msdn.microsoft.com/library/dn931928.aspx)에서 사용할 수 있습니다.

코드 예제는 다음을 참조하세요.

* [Resource Manager 템플릿을 사용하여 VM 확장 집합에 대한 고급 자동 크기 조정 구성](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md)  
* [자동 크기 조정 REST API](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>수평 및 수직적 크기 조정
자동 규모 조정은 수평적으로만 규모를 조정하며 이는 VM 인스턴스 수의 증가("규모 확장") 또는 감소("규모 축소")입니다.  수평적 규모 조정은 부하를 처리하기 위해 잠재적으로 수천 개의 VM을 실행할 수 있으므로 클라우드 상황에서 더 유연합니다.

반대로 수직적 규모 조정은 다릅니다. 수직적 규모 조정은 동일한 수의 VM을 유지하지만 VM을 강력하게("강화") 만들거나 약하게("축소") 만듭니다. 전원은 메모리, CPU 속도, 디스크 공간 등에서 측정됩니다.  수직적 크기 조정에 더 많은 제한이 있습니다. 큰 하드웨어의 가용성에 따라 다르고 신속하게 상한값에 도달하며 지역에 따라 달라질 수 있습니다. 또한 수직적 규모 조정은 일반적으로 VM 중지 및 재시작이 필요합니다.

자세한 내용은 [Azure Automation을 사용하여 Azure 가상 머신을 수직으로 확장](../../virtual-machines/linux/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

## <a name="methods-of-access"></a>액세스 방법
자동 크기 조정은 다음을 통해 설정할 수 있습니다.

* [Azure Portal](../../azure-monitor/platform/autoscale-get-started.md)
* [PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)
* [플랫폼 간 CLI(명령줄 인터페이스)](../../azure-monitor/platform/cli-samples.md#autoscale)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>자동 크기 조정이 지원되는 서비스

| 서비스 | 스키마 및 문서 |
| --- | --- |
| Web Apps |[Web Apps 크기 조정](../../azure-monitor/platform/autoscale-get-started.md) |
| Cloud Services |[클라우드 서비스 자동 크기 조정](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| 가상 머신: 클래식 |[클래식 Virtual Machine 가용성 집합 크기 조정](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| 가상 머신: Windows 확장 세트 |[Windows에서 가상 머신 확장 집합 크기 조정](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| 가상 머신: Linux 확장 세트 |[Linux에서 가상 머신 확장 집합 크기 조정](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| 가상 머신: Windows 예제 |[Resource Manager 템플릿을 사용하여 VM 확장 집합에 대한 고급 자동 크기 조정 구성](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| API Management 서비스|[Azure API Management 인스턴스 자동 크기 조정](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>다음 단계
자동 크기 조정에 대한 자세한 내용은 앞에 나열된 자동 크기 조정 연습을 사용하거나 다음 리소스를 참조하세요.

* [Azure Monitor 자동 크기 조정 공용 메트릭](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Azure Monitor 자동 크기 조정에 대한 모범 사례](../../azure-monitor/platform/autoscale-best-practices.md)
* [크기 자동 조정 작업을 사용하여 전자 메일 및 웹후크 경고 알림 보내기](../../azure-monitor/platform/autoscale-webhook-email.md)
* [자동 크기 조정 REST API](https://msdn.microsoft.com/library/dn931953.aspx)
* [Virtual Machine Scale Sets 자동 크기 조정 문제 해결](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
