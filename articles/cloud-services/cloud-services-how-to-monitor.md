---
title: Azure 클라우드 서비스 모니터링 | Microsoft Docs
description: Azure 클라우드 서비스에 포함되는 모니터링 기능과 사용 가능한 일부 옵션에 대해 설명합니다.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: jeconnoc
ms.openlocfilehash: 844fef9a87c1db06c6415c59d4be26caf928382b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61432913"
---
# <a name="introduction-to-cloud-service-monitoring"></a>클라우드 서비스 모니터링 소개

클라우드 서비스의 주요 성능 메트릭을 모니터링할 수 있습니다. 모든 클라우드 서비스 역할은 CPU 사용량, 네트워크 사용량, 디스크 사용률 등의 최소 데이터를 수집합니다. `Microsoft.Azure.Diagnostics` 확장이 적용된 클라우드 서비스 역할은 추가 데이터 요소를 수집할 수 있습니다. 이 문서에서는 Cloud Services용 Azure Diagnostics 기능을 소개합니다.

기본 모니터링을 사용할 경우 역할 인스턴스의 성능 카운터 데이터는 3분 간격으로 샘플링되고 수집됩니다. 이 기본 모니터링 데이터는 저장소 계정에 저장되지 않으며, 추가 비용도 부과되지 않습니다.

고급 모니터링을 사용할 경우 5분, 1시간 및 12시간 간격으로 추가 메트릭이 샘플링되고 수집됩니다. 집계된 데이터는 저장소 계정에 테이블로 저장되고, 10일 후에 삭제됩니다. 사용되는 저장소 계정은 역할별로 구성됩니다. 따라서 역할마다 다른 저장소 계정을 사용할 수 있습니다. 이러한 내용은 [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) 및 [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) 파일에 연결 문자열로 구성됩니다.


## <a name="basic-monitoring"></a>기본 모니터링

소개에서 언급한 것처럼, 클라우드 서비스는 호스트 가상 컴퓨터에서 기본 모니터링 데이터를 자동으로 수집합니다. 이 데이터에는 CPU 비율, 네트워크 입/출력 및 디스크 읽기/쓰기가 포함됩니다. 수집된 모니터링 데이터는 Azure Portal에서 클라우드 서비스의 개요 및 메트릭 페이지에 자동으로 표시됩니다. 

기본 모니터링에는 저장소 계정이 필요하지 않습니다. 

![기본 클라우드 서비스 모니터링 타일](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>고급 모니터링

고급 모니터링에서는 모니터링하려는 역할에 **Azure Diagnostics** 확장(및 필요에 따라 Application Insights SDK)을 사용합니다. 진단 확장은 **diagnostics.wadcfgx**라는 구성 파일(역할당)을 사용하여 모니터링되는 진단 메트릭을 구성합니다. Azure 진단 확장은 데이터를 수집하고 Azure Storage 계정에 저장합니다. 이러한 설정은 **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) 및 [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) 파일에서 구성됩니다. 따라서 고급 모니터링에는 추가 비용이 부과됩니다.

각 역할을 만들 때 Visual Studio는 역할에 Azure Diagnostics 확장을 추가합니다. 이 진단 확장은 다음과 같은 유형의 정보를 수집할 수 있습니다.

* 사용자 지정 성능 카운터
* 애플리케이션 로그
* Windows 이벤트 로그
* .NET 이벤트 원본
* IIS 로그
* 매니페스트 기반 ETW
* 크래시 덤프
* 고객 오류 로그

> [!IMPORTANT]
> 이러한 모든 데이터가 저장소 계정에 집계되지만, 포털은 데이터를 차트로 작성하는 기본적인 방법을 제공하지 **않습니다**. 애플리케이션에 Application Insights와 같은 다른 서비스를 통합하는 것이 좋습니다.

## <a name="setup-diagnostics-extension"></a>진단 확장 설정

먼저 **클래식** 저장소 계정이 없는 경우 [하나 만듭니다](../storage/common/storage-quickstart-create-account.md). **클래식 배포 모델**을 지정하여 저장소 계정을 만듭니다.

다음으로, **저장소 계정(클래식)** 리소스로 이동합니다. **설정** > **액세스 키**를 선택하고 **기본 연결 문자열** 값을 복사합니다. 클라우드 서비스에 대해 이 값이 필요합니다. 

고급 진단을 사용하려면 두 개의 구성 파일인 **ServiceDefinition.csdef** 및 **ServiceConfiguration.cscfg**를 변경해야 합니다.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

**ServiceDefinition.csdef** 파일에는 고급 진단을 사용하는 각 역할에 대해 `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`라는 새 설정을 추가합니다. Visual Studio는 새 프로젝트를 만들 때 파일에 이 값을 추가합니다. 이 값이 누락된 경우 지금 추가해도 됩니다. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

이렇게 하면 모든 **ServiceConfiguration.cscfg** 파일에 추가해야 하는 새 설정이 정의됩니다. 

Azure에 배포하기 위한 **ServiceConfiguration.cloud.cscfg** 및 에뮬레이트된 환경에서 로컬 배포에 사용되는 **ServiceConfiguration.local.cscfg**라는 두 개의 **.cscfg** 파일이 있을 것입니다. 각 **.cscfg** 파일을 열고 변경합니다. `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`이라는 설정을 추가합니다. 클래식 저장소 계정의 **기본 연결 문자열**로 값을 설정합니다. 개발 컴퓨터에서 로컬 저장소를 사용하려는 경우 `UseDevelopmentStorage=true`를 사용합니다.

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Application Insights 사용

Visual Studio에서 클라우드 서비스를 게시할 때는 Application Insights로 진단 데이터를 보내는 옵션이 제공됩니다. 이때 Application Insights Azure 리소스를 만들거나 기존 Azure 리소스에 데이터를 보낼 수도 있습니다. Application Insights를 사용하여 클라우드 서비스의 가용성, 성능, 실패 및 사용 현황을 모니터링할 수 있습니다. 사용자 지정 차트를 Application Insights에 추가하여 가장 중요한 데이터를 볼 수 있습니다. Application Insights SDK를 사용하여 클라우드 서비스 프로젝트에서 역할 인스턴스 데이터를 수집할 수 있습니다. Application Insights를 통합하는 방법에 대한 자세한 내용은 [클라우드 서비스와 Application Insights](../azure-monitor/app/cloudservices.md)를 참조하세요.

Application Insights를 사용하여 Microsoft Azure Diagnostics 확장을 통해 지정한 성능 카운터(및 기타 설정)를 표시할 수 있지만, Application Insights SDK를 작업자 및 웹 역할에 통합해야만 보다 풍부한 환경을 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- [Application Insights 및 Cloud Services에 대한 자세한 정보](../azure-monitor/app/cloudservices.md)
- [성능 카운터 설정](diagnostics-performance-counters.md)

