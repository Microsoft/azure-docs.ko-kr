---
title: Azure Service Fabric 컨테이너 서비스에 대한 gMSA 설정 | Microsoft Docs
description: Azure Service Fabric에서 실행 중인 컨테이너에 대해 gMSA를 설정하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo, subramar
ms.openlocfilehash: ae8c5c8ec1e16669b3cbdde8b3eaa3d5dbb7c4de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837462"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Service Fabric에서 실행 중인 Windows 컨테이너에 대한 gMSA 설정

gMSA(그룹 관리 서비스 계정)를 설정하려면 자격 증명 사양 파일(`credspec`)을 클러스터의 모든 노드에 배치합니다. VM 확장을 사용하여 모든 노드에서 파일을 복사할 수 있습니다.  `credspec` 파일은 gMSA 계정 정보를 포함해야 합니다. 에 대 한 자세한 합니다 `credspec` 파일을 참조 하세요 [자격 증명 사양 만들기](https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec)합니다. 애플리케이션 매니페스트에서 자격 증명 사양 및 `Hostname` 태그가 지정됩니다. `Hostname` 태그는 컨테이너를 실행하는 gMSA 계정 이름과 일치해야 합니다.  `Hostname` 태그를 사용하면 컨테이너를 Kerberos 인증을 사용하여 도메인에 있는 다른 서비스에 인증할 수 있습니다.  애플리케이션 매니페스트에서 `Hostname` 및 `credspec`를 지정하는 샘플은 다음 코드 조각에 표시됩니다.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
다음 단계는 아래 문서를 참조하세요.

* [Windows Server 2016에서 Windows 컨테이너를 Service Fabric에 배포](service-fabric-get-started-containers.md)
* [Linux에서 Docker 컨테이너를 Service Fabric에 배포](service-fabric-get-started-containers-linux.md)
