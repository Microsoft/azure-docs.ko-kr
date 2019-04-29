---
title: Azure Service Fabric에서 여러 환경에 대한 애플리케이션 관리 | Microsoft Docs
description: 한 컴퓨터에서 수천 개의 컴퓨터에 이르는 클러스터에서 Azure Service Fabric 애플리케이션을 실행할 수 있습니다. 어떤 경우에 해당하는 다양한 환경에 대해 다르게 애플리케이션을 구성하려 합니다. 이 문서에서는 환경 당 다른 애플리케이션 매개 변수를 정의하는 방법을 설명합니다.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: dac96ef6fce38a0557444e181fa6eccb649cfb9a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719229"
---
# <a name="manage-applications-for-multiple-environments"></a>여러 환경에 대한 애플리케이션 관리

Azure Service Fabric 클러스터를 사용하면 하나의 컴퓨터에서 수천 대의 컴퓨터를 사용하여 클러스터를 만들 수 있습니다. 대부분의 경우, 로컬 개발 클러스터, 공유 개발 클러스터 및 프로덕션 클러스터를 비롯한 여러 클러스터 구성에서 애플리케이션을 배포해야 할 수 있습니다. 이러한 모든 클러스터는 코드가 실행되어야 하는 다양한 환경으로 간주됩니다. 애플리케이션 이진 파일은 이러한 다양한 환경에서 수정 없이 실행될 수 있지만, 애플리케이션을 다르게 구성하려는 경우가 많습니다.

다음과 같은 두 가지 간단한 예를 살펴봅니다.
  - 서비스는 정의된 포트에서 수신 대기하지만 환경 간에 포트가 달라야 합니다.
  - 환경 간에 데이터베이스에 대해 다른 바인딩 자격 증명을 제공해야 합니다.

## <a name="specifying-configuration"></a>구성 지정

제공한 구성은 다음 두 범주로 구분될 수 있습니다.

- 서비스가 실행되는 방법에 적용되는 구성
  - 예: 엔드포인트에 대한 포트 번호 또는 서비스의 인스턴스 수
  - 이 구성은 애플리케이션 또는 서비스 매니페스트 파일에 지정됩니다.
- 애플리케이션 코드에 적용되는 구성
  - 예: 데이터베이스에 대한 바인딩 정보
  - 이 구성은 구성 파일 또는 환경 변수를 통해 제공될 수 있습니다.

> [!NOTE]
> 애플리케이션 및 서비스 매니페스트 파일의 모든 특성이 매개 변수를 지원하지는 않습니다.
> 해당 경우 배포 워크플로의 일부로 문자열을 대체해야 합니다. Azure DevOps에서는 토큰 바꾸기 https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens와 같은 확장을 사용할 수 있고, Jenkins에서는 값을 바꾸는 스크립트 태스크를 실행할 수 있습니다.
>

## <a name="specifying-parameters-during-application-creation"></a>애플리케이션 생성 중에 매개 변수 지정

Service Fabric에서 명명된 애플리케이션 인스턴스를 만들 때 매개 변수를 전달하는 옵션을 사용할 수 있습니다. 이렇게 하는 방법은 애플리케이션 인스턴스를 만드는 방법에 따라 달라집니다.

  - PowerShell에서 [`New-ServiceFabricApplication`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet은 애플리케이션 매개 변수를 해시 테이블로 사용합니다.
  - sfctl를 사용하여 [`sfctl application create`](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) 명령은 매개 변수를 JSON 문자열로 사용합니다. install.sh 스크립트는 sfctl을 사용합니다.
  - Visual Studio에서는 애플리케이션 프로젝트의 Parameters 폴더에 매개 변수 파일 집합을 제공합니다. 이러한 매개 변수 파일은 Azure DevOps Services 또는 Team Foundation Server를 사용하여 Visual Studio에서 게시할 때 사용됩니다. Visual Studio에서 매개 변수 파일은 Deploy-fabricapplication.ps1 스크립트에 전달됩니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 여기에 설명된 개념 중 일부를 사용하는 방법을 보여 줍니다.

- [Service Fabric에서 서비스에 대한 환경 변수를 지정하는 방법](service-fabric-how-to-specify-environment-variables.md)
- [Service Fabric에서 매개 변수를 사용하는 서비스의 포트 번호를 지정하는 방법](service-fabric-how-to-specify-port-number-using-parameters.md)
- [구성 파일을 매개 변수화하는 방법](service-fabric-how-to-parameterize-configuration-files.md)

- [환경 변수 참조](service-fabric-environment-variables-reference.md)
