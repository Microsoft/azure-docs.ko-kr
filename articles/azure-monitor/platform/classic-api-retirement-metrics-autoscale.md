---
title: Azure Monitor에서 메트릭 및 자동 크기 조정을 위한 클래식 배포 모델 API 사용 중지
description: ASM(Azure 서비스 관리) 또는 RDFE 배포 모델이라고도 하는 메트릭 및 크기 자동 조정 클래식 API의 사용이 중지됨
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: robb
ms.openlocfilehash: ce54b63aa7831ed40a8592d536c43fc83fdc5567
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709986"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor에서 메트릭 및 자동 크기 조정을 위한 클래식 배포 모델 API 사용 중지

Azure Monitor(이전에 처음 출시될 때는 Azure Insights)는 현재 클래식 VM 및 클래식 Cloud Services에 대한 자동 크기 조정 설정을 만들고 관리하며 해당 메트릭을 사용하는 기능을 제공합니다. 클래식 배포 모델 기반 API의 원래 세트는 모든 지역의 모든 Azure 공용 및 사설 클라우드에서 **2019년 6월 30일 이후로 사용 중지**됩니다.   

1년여 동안 Azure Resource Manager 기반 API 세트를 통해 동일한 작업이 지원되었습니다. Azure Portal은 자동 크기 조정 및 메트릭 둘 다에 새로운 REST API를 사용합니다. 이러한 Resource Manager API를 기반으로 하는 새로운 SDK, PowerShell 및 CLI도 사용할 수 있습니다. 모니터링 파트너 서비스는 Azure Monitor의 새로운 Resource Manager 기반 REST API를 사용합니다.  

## <a name="who-is-not-affected"></a>영향을 받지 않는 사람

Azure Portal, [새로운 Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), PowerShell, CLI 또는 Resource Manager 템플릿을 통해 자동 크기 조정을 관리하는 경우 아무 작업도 필요하지 않습니다.  

Azure Portal 또는 다양한 [모니터링 파트너 서비스](../../azure-monitor/platform/partners.md)를 통해 메트릭을 사용하는 경우 아무 작업도 필요하지 않습니다. Microsoft는 새 API로 마이그레이션하기 위해 모니터링 파트너와 협력하고 있습니다.

## <a name="who-is-affected"></a>영향을 받는 사람

이 문서는 다음 구성 요소를 사용하는 경우에 적용됩니다.

- **클래식 Azure Insights SDK** - [클래식 Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)를 사용하는 경우 [.NET](https://github.com/azure/azure-libraries-for-net#download) 또는 [Java](https://github.com/azure/azure-libraries-for-java#download)용 새 Azure Monitor SDK를 사용하도록 전환합니다. [Azure Monitor SDK NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)를 다운로드합니다.

- **클래식 자동 크기 조정** - 사용자 지정 빌드된 도구 또는 [클래식 Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)를 사용하여 [클래식 자동 크기 조정 설정 API](https://msdn.microsoft.com/library/azure/mt348562.aspx)를 호출하는 경우 [Resource Manager Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings)를 사용하도록 전환해야 합니다.

- **클래식 메트릭** - 사용자 지정 빌드된 도구에서 [클래식 REST API](https://msdn.microsoft.com/library/azure/dn510374.aspx) 또는 [클래식 Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)를 통해 메트릭을 사용하는 경우 [Resource Manager Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings)를 사용하도록 전환해야 합니다. 

코드 또는 사용자 지정 도구에서 클래식 API를 호출하는지 확실하지 않은 경우 다음을 확인합니다.

- 코드 또는 도구에서 참조된 URI를 검토합니다. 클래식 API는 URI https://management.core.windows.net를 사용합니다. Resource Manager 기반 API의 경우 https://management.azure.com/으로 시작하는 최신 URI를 사용해야 합니다.

- 머신의 어셈블리 이름을 비교합니다. 이전 클래식 어셈블리는 https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/에 있습니다.

- 인증서 인증을 사용하여 메트릭 또는 자동 크기 조정 API에 액세스하는 경우 클래식 엔드포인트 및 라이브러리를 사용하고 있습니다. 최신 Resource Manager API에는 서비스 주체 또는 사용자 계정을 통한 Azure Active Directory 인증이 필요합니다.

- 다음 링크 중 하나의 문서에서 참조된 호출을 사용하는 경우 이전 클래식 API를 사용하고 있습니다.

  - [Windows.Azure.Management.Monitoring 클래스 라이브러리](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [(클래식) .NET 모니터링](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations 인터페이스](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>전환해야 하는 이유

기존의 모든 자동 크기 조정 및 메트릭 기능은 새 API를 통해 계속 작동합니다.  

최신 API로 마이그레이션하면 모든 모니터링 서비스에 대한 일관성 있는 RBAC(역할 기반 액세스 제어) 지원과 같은 Resource Manager 기반 기능이 제공됩니다. 메트릭에 대한 추가 기능도 얻을 수 있습니다. 

- 차원 지원
- 모든 서비스에서 일관성 있는 1분 메트릭 세분성 적용 
- 향상된 쿼리
- 데이터 보존 기간 증가(93일 메트릭 대비 30일) 

전반적으로, Azure의 다른 모든 서비스처럼 Resource Manager 기반 Azure Monitor API는 성능, 확장성 및 안정성을 개선합니다. 

## <a name="what-happens-if-you-do-not-migrate"></a>마이그레이션하지 않으면 어떻게 되나요?

### <a name="before-retirement"></a>사용 중지 이전

Azure 서비스나 해당 워크로드에 대한 직접적인 영향은 없습니다.  

### <a name="after-retirement"></a>사용 중지 이후

앞에 나열된 클래식 API에 대한 모든 호출이 실패하고 다음과 유사한 오류 메시지가 반환됩니다.

자동 크기 조정의 경우: *이 API는 더 이상 사용되지 않습니다. Azure Portal, Azure Monitor SDK, PowerShell, CLI 또는 Resource Manager 템플릿을 사용하여 자동 크기 조정 설정을 관리합니다.*  

메트릭의 경우: *이 API는 더 이상 사용되지 않습니다. Azure Portal, Azure Monitor SDK, PowerShell, CLI 등을 사용하여 메트릭을 쿼리합니다.*

## <a name="email-notifications"></a>전자 메일 알림

다음 계정 역할의 메일 주소로 사용 중지 알림이 전송되었습니다. 

- 계정 및 서비스 관리자
- 공동 관리자  

질문이 있으면 MonitorClassicAPIhelp@microsoft.com으로 문의하세요.  

## <a name="references"></a>참조

- [Azure Monitor용 최신 REST API](https://docs.microsoft.com/rest/api/monitor/) 
- [최신 Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
