---
title: Azure Service Fabric Explorer를 사용하여 클러스터 시각화 | Microsoft Docs
description: Service Fabric Explorer는 Microsoft Azure Service Fabric 클러스터에서 클라우드 애플리케이션 및 노드를 검사 및 관리하기 위한 애플리케이션입니다.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 358ebfa601ff8e4d2fb6ae91e51516cb0a933af7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716577"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>서비스 패브릭 탐색기로 클러스터 시각화

SFX(Service Fabric Explorer)는 Azure Service Fabric 클러스터를 검사하고 관리하기 위한 오픈 소스 도구입니다. Service Fabric Explorer는 Windows, macOS 및 Linux용 데스크톱 응용 프로그램입니다.

## <a name="service-fabric-explorer-download"></a>Service Fabric Explorer 다운로드

Service Fabric Explorer를 데스크톱 애플리케이션으로 다운로드하려면 다음 링크를 사용합니다.

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> 데스크톱 버전의 Service Fabric Explorer는 클러스터 지원 버전보다 더 많거나 적은 기능을 제공할 수 있습니다. 전체 기능 호환성을 위해 클러스터에 배포된 Service Fabric Explorer 버전으로 대체할 수 있습니다.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>클러스터에서 Service Fabric Explorer 실행

Service Fabric Explorer는 Service Fabric 클러스터의 HTTP 관리 엔드포인트에도 호스트됩니다. 웹 브라우저에서 SFX를 시작 하려면 클러스터의 HTTP 관리 끝점에서 이동 브라우저용-예: https:\//clusterFQDN:19080 합니다.

개발자 워크스테이션 설정의 경우 https://localhost:19080/Explorer로 이동하여 로컬 클러스터에서 Service Fabric Explorer를 시작할 수 있습니다. 이 문서를 참조하여 [개발 환경을 준비](service-fabric-get-started.md)하세요.

> [!NOTE]
> 클러스터가 자체 서명된 인증서로 보호되면 웹 브라우저에서 "이 사이트는 안전하지 않습니다"라는 오류 메시지가 표시됩니다. 대부분의 최신 웹 브라우저에서는 이 경고를 무시하고 계속 진행할 수 있습니다. 프로덕션 환경의 클러스터는 일반 이름과 인증 기관에서 발급한 인증서를 사용하여 보호해야 합니다. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Service Fabric 클러스터에 연결
Service Fabric 클러스터를 연결하려면 클러스터 관리 엔드포인트(FQDN/IP) 및 HTTP 관리 엔드포인트 포트(기본적으로 19080)가 필요합니다. 예를 들어 https\:/ / mysfcluster.westus.cloudapp.azure.com:19080 합니다. "localhost에 연결" 확인란을 사용하여 워크스테이션에서 로컬 클러스터에 연결합니다.

### <a name="connect-to-a-secure-cluster"></a>보안 클러스터에 연결
인증서 또는 AAD(Azure Active Directory)를 사용하여 서비스 패브릭 클라이언트에 대한 클라이언트 액세스를 제어할 수 있습니다.

보안 클러스터에 연결하려는 경우 클러스터의 구성에 따라 클라이언트 인증서를 제시하거나 AAD를 사용하여 로그인해야 합니다.

## <a name="understand-the-service-fabric-explorer-layout"></a>서비스 패브릭 탐색기 레이아웃 이해
왼쪽의 트리를 사용하여 서비스 패브릭 탐색기를 탐색할 수 있습니다. 트리의 루트에서 클러스터 대시보드는 애플리케이션 및 노드 상태에 대한 요약을 포함하여 클러스터에 대한 개요를 제공합니다.

![서비스 패브릭 탐색기 클러스터 대시보드][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>클러스터의 레이아웃 보기
서비스 패브릭 클러스터의 노드는 장애 도메인 및 업그레이드 도메인의 2차원 그리드에 배치됩니다. 이렇게 배치하면 하드웨어 오류와 애플리케이션 업그레이드가 있는 상태에서 애플리케이션을 계속 사용할 수 있습니다. 클러스터 맵을 사용하여 현재 클러스터의 레이아웃 방식을 볼 수 있습니다.

![서비스 패브릭 탐색기 클러스터 맵][sfx-cluster-map]

### <a name="view-applications-and-services"></a>애플리케이션 및 서비스 보기
클러스터에는 두 개의 하위 트리가 포함되어 있습니다. 하나는 애플리케이션용이고 다른 하나는 노드용입니다.

애플리케이션 보기를 사용하여 서비스 패브릭의 논리 계층 구조인 애플리케이션, 서비스, 파티션 및 복제를 탐색할 수 있습니다.

아래 예제에서 애플리케이션 **MyApp**은 두 개의 서비스, **MyStatefulService**와 **WebService**로 구성되어 있습니다. **MyStatefulService** 는 상태 저장이므로 한 개의 주 복제본과 두 개의 보조 복제본이 있는 파티션을 포함합니다. 이와 반대로 WebSvcService는 상태 비저장이며 단일 인스턴스가 들어 있습니다.

![Service Fabric Explorer 애플리케이션 보기][sfx-application-tree]

트리의 각 수준에서 주 창은 항목에 대한 관련 정보를 표시합니다. 예를 들어 특정 서비스에 대한 상태 및 버전을 볼 수 있습니다.

![서비스 패브릭 탐색기 기능 창][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>클러스터의 노드 보기
노드 보기는 클러스터의 물리적 레이아웃을 보여 줍니다. 지정된 노드의 경우 해당 노드에 배포된 코드를 가진 애플리케이션을 검사할 수 있습니다. 특히 현재 실행되고 있는 복제본을 확인할 수 있습니다.

## <a name="actions"></a>작업
Service Fabric Explorer는 클러스터 내에서 노드, 애플리케이션 및 서비스에 대한 작업을 호출하는 빠른 방법을 제공합니다.

예를 들어 애플리케이션 인스턴스를 삭제하려면 왼쪽 트리에서 애플리케이션을 선택한 다음 **작업** > **애플리케이션 삭제**로 이동하여 로컬 클러스터에서 Service Fabric 탐색기를 시작할 수 있습니다.

![Service Fabric Explorer에서 애플리케이션 삭제][sfx-delete-application]

> [!TIP]
> 각 요소 옆의 줄임표를 클릭하여 동일한 작업을 수행할 수 있습니다.
>
> 서비스 패브릭 탐색기를 통해 수행할 수 있는 모든 작업은 PowerShell 또는 REST API를 통해 수행할 수 있으므로 자동화를 사용하도록 설정할 수 있습니다.
>
>

또한 Service Fabric Explorer를 사용하여 지정된 애플리케이션 형식 및 버전에 대한 애플리케이션 인스턴스를 만들 수도 있습니다. 트리 보기에서 애플리케이션 형식을 선택하고 오른쪽 창에서 원하는 버전 옆의 **앱 인스턴스 만들기** 링크를 클릭합니다.

![Service Fabric Explorer에서 애플리케이션 인스턴스 만들기][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer는 애플리케이션 인스턴스를 만들 때 매개 변수를 지원하지 않습니다. 애플리케이션 인스턴스는 기본 매개 변수 값을 사용합니다.
>
>

## <a name="event-store"></a>EventStore
EventStore는 플랫폼에서 Service Fabric Explorer와 REST API를 통해 사용할 수 있는 Service Fabric 플랫폼 이벤트를 제공하는 기능입니다. 이벤트 시간을 기반으로 각 엔터티(예: 노드, 서비스, 애플리케이션 및 쿼리)에 대해 클러스터에서 진행 중인 작업에 대한 스냅숏 보기를 볼 수 있습니다. 또한 EventStore에 대한 자세한 내용은 [EventStore 개요](service-fabric-diagnostics-eventstore.md)에서 참조할 수 있습니다.   

![EventStore][sfx-eventstore]

>[!NOTE]
>Service Fabric 버전 6.4부터 EventStore는 기본적으로 사용하도록 설정되지 않으므로 Resource Manager 템플릿에서 사용하도록 설정해야 합니다.

>[!NOTE]
>Service Fabric 버전 6.4부터 EventStore API는 Azure에서만 실행되는 Windows 클러스터에 제공됩니다. 이 기능을 Linux 및 독립 실행형 클러스터에도 이식하려고 노력하고 있습니다.


## <a name="next-steps"></a>다음 단계
* [Visual Studio에서 Service Fabric 애플리케이션 관리](service-fabric-manage-application-in-visual-studio.md)
* [PowerShell을 사용하여 Service Fabric 애플리케이션 배포](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
