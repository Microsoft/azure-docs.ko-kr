---
title: Azure Cloud Services 정의 스키마(.cscfg 파일) | Microsoft Docs
ms.custom: ''
origin.date: 12/07/2016
ms.date: 11/06/2017
ms.prod: azure
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 3ddc7fea-3339-4fc0-bdf9-853c32b25f69
caps.latest.revision: 35
author: thraka
ms.author: v-yiso
manager: timlt
ms.openlocfilehash: 424381e2c243420cc2a68dc776d249cb17574f98
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130316"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Azure Cloud Services 구성 스키마(.cscfg 파일)
서비스 구성 파일은 서비스의 각 역할에 대해 배포할 역할 인스턴스의 수, 구성 설정의 값 및 역할에 연결된 인증서의 지문을 지정합니다. 서비스가 Virtual Network의 일부인 경우, 가상 네트워킹 구성 파일 뿐 아니라 서비스 구성 파일에도 네트워크에 대한 구성 정보를 제공해야 합니다. 서비스 구성 파일의 기본 확장명은 .cscfg입니다.

서비스 모델은 [Cloud Service(클래식) 정의 스키마](schema-csdef-file.md)에 설명되어 있습니다.

기본적으로 Azure Diagnostics 구성 스키마 파일은 `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` 디렉터리에 설치됩니다. `<version>`을 [Azure SDK](https://azure.microsoft.com/downloads/)의 설치된 버전으로 바꿉니다.

서비스의 역할 구성에 관한 자세한 내용은 [Cloud Service 모델이란?](cloud-services-model-and-package.md)을 참조하세요.

## <a name="basic-service-configuration-schema"></a>기본 서비스 구성 스키마
서비스 구성 파일의 기본 형식은 다음과 같습니다.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>스키마 정의
다음 토픽에서는 `ServiceConfiguration` 요소에 대한 스키마를 설명합니다.

- [역할 스키마](schema-cscfg-role.md)
- [NetworkConfiguration 스키마](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>서비스 구성 네임스페이스
서비스 구성 파일에 대한 XML 네임스페이스는 `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`입니다.

##  <a name="ServiceConfiguration"></a> ServiceConfiguration 요소
`ServiceConfiguration` 요소는 서비스 구성 파일의 최상위 요소입니다.

다음 표에서는 `ServiceConfiguration` 요소의 특성을 설명합니다. 모든 특성 값은 문자열 유형입니다.

| 특성 | 설명 |
| --------- | ----------- |
|serviceName|필수 사항입니다. 클라우드 서비스의 이름입니다. 여기서 지정된 이름은 서비스 정의 파일에서 지정된 이름과 일치해야 합니다.|
|osFamily|선택 사항입니다. 클라우드 서비스의 역할 인스턴스에서 실행될 게스트 OS를 지정합니다. 지원되는 게스트 OS 릴리스에 대한 정보는 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](cloud-services-guestos-update-matrix.md)를 참조하세요.<br /><br /> `osFamily` 값을 포함하지 않고 특정 게스트 OS 버전에 `osVersion` 특성을 설정하지 않은 경우 기본값인 1이 사용됩니다.|
|osVersion|선택 사항입니다. 클라우드 서비스의 역할 인스턴스에서 실행될 게스트 OS의 버전을 지정합니다. 게스트 OS 버전에 대한 자세한 내용은 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](cloud-services-guestos-update-matrix.md)를 참조하세요.<br /><br /> 게스트 OS가 최신 버전으로 자동으로 업그레이드되도록 지정할 수 있습니다. 이를 수행하려면 `osVersion` 특성의 값을 `*`로 설정합니다. `*`로 설정하면, 역할 인스턴스는 지정된 OS 제품군을 위한 게스트 OS의 최신 버전을 사용하여 표시되고, 게스트 OS의 새 버전이 릴리스되면 자동으로 업그레이드됩니다.<br /><br /> 특정 버전을 수동으로 지정하려면 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](cloud-services-guestos-update-matrix.md)의 **향후, 현재 및 과도기 게스트 OS 버전** 섹션에 나온 테이블의 `Configuration String`을 사용합니다.<br /><br /> `osVersion` 특성에 대한 기본 값은 `*`입니다.|
|schemaVersion|선택 사항입니다. 서비스 구성 스키마의 버전을 지정합니다. 스키마 버전을 사용하면 SDK가 둘 이상의 버전이 동시에 설치된 경우 Visual Studio에서 스키마 유효성 검사에 사용할 올바른 SDK 도구를 선택할 수 있습니다. 스키마 및 버전 호환성에 대한 자세한 내용은 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](cloud-services-guestos-update-matrix.md)를 참조하세요.|

서비스 구성 파일에는 `ServiceConfiguration` 요소가 하나 있어야 합니다. `ServiceConfiguration` 요소는 `Role` 요소를 개수에 관계 없이, `NetworkConfiguration` 요소는 없거나 1개를 포함할 수 있습니다.