---
title: Azure Cloud Services 역할 스키마 | Microsoft Docs
ms.custom: ''
origin.date: 12/07/2016
ms.date: 11/06/2017
ms.prod: azure
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: e4fbffc1-98eb-449c-971c-de415e45ab34
caps.latest.revision: 12
author: ''
ms.author: adegeo
manager: timlt
ms.openlocfilehash: aa6f8a821edea6261d64bb411154e82fdf212a8d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130248"
---
# <a name="azure-cloud-services-config-role-schema"></a>Azure Cloud Services 역할 구성 스키마

구성 파일의 `Role` 요소는 서비스의 각 역할에 대해 배포할 역할 인스턴스의 수, 구성 설정의 값 및 역할에 연결된 인증서의 지문을 지정합니다.

Azure Service 구성 스키마에 대한 자세한 내용은 [Cloud Service(클래식) 구성 스키마](schema-cscfg-file.md)를 참조하세요. Azure Service 정의 스키마에 대한 자세한 내용은 [Cloud Service(클래식) 정의 스키마](schema-csdef-file.md)를 참조하세요.

##  <a name="Role"></a> Role 요소
다음 예제는 `Role` 요소와 해당 자식 요소를 보여 줍니다.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

다음 표에서는 `Role` 요소의 특성을 설명합니다.

| 특성 | 설명 |
| --------- | ----------- |
| 이름   | 필수 사항입니다. 역할 이름을 지정합니다. 이름은 서비스 정의 파일에서 역할에 대해 제공된 이름과 일치해야 합니다.|
| vmName | 선택 사항입니다. Virtual Machine에 대한 DNS 이름을 지정합니다. 이름은 10자 이하여야 합니다.|

다음 테이블에서는 `Role` 요소의 자식 요소에 대해 설명합니다.

| 요소 | 설명 |
| ------- | ----------- |
| 인스턴스 | 필수 사항입니다. 역할에 대해 배포할 인스턴스 수를 지정합니다. 인스턴스의 수는 `count` 특성에 대한 정수로 정의됩니다.|
| 설정   | 선택 사항입니다. 역할에 대한 설정의 컬렉션에서 설정 이름 및 값을 지정합니다. 설정 이름은 `name` 특성에 대한 문자열로 정의되며, 설정 값은 `value` 특성에 대한 문자열로 정의됩니다.|
| 인증서 | 선택 사항입니다. 역할에 연결될 서비스 인증서의 이름, 지문 및 알고리즘을 지정합니다. 인증서 이름은 `name` 특성에 대한 문자열로 정의됩니다. 인증서 지문은 공백을 포함하지 않고 `thumbprint` 특성에 대한 16진수 숫자의 문자열로 정의됩니다. 16진수 숫자는 숫자 및 알파벳 대문자를 사용해야 합니다. 인증서 알고리즘은 `thumbprintAlgorithm` 특성에 대한 문자열로 정의됩니다.|

## <a name="see-also"></a>관련 항목
[Cloud Service(클래식) 구성 스키마](schema-cscfg-file.md)