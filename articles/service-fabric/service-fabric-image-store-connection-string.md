---
title: Azure Service Fabric 이미지 저장소 연결 문자열 | Microsoft Docs
description: 이미지 저장소 연결 문자열 이해
services: service-fabric
documentationcenter: .net
author: alexwun
manager: chackdan
editor: ''
ms.assetid: 00f8059d-9d53-4cb8-b44a-b25149de3030
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: 4a56b48c0041e963b89312c59335b45cabacc1bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720198"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>ImageStoreConnectionString 설정 이해

일부 문서에서 의미를 설명하지 않고 "ImageStoreConnectionString"라는 매개 변수가 있는지 간단하게 언급합니다. 그리고 [PowerShell을 사용하여 응용 프로그램 배포 및 제거][10]와 같은 문서를 살펴본 후에 대상 클러스터의 클러스터 매니페스트에 표시된 대로 값을 복사/붙여넣기만 하면 됩니다. 따라서 설정은 클러스터당 구성될 수 있지만 [Azure Portal][11]을 통해 클러스터를 만드는 경우 이 설정을 구성하는 옵션이 없고 "fabric:ImageStore"은 항상 있습니다. 이 설정의 용도는 무엇인가요?

![클러스터 매니페스트][img_cm]

Service Fabric은 다양한 팀에서 내부 Microsoft 사용을 위한 플랫폼으로 시작되었습니다. 따라서 "이미지 저장소"와 같은 일부 측면은 자유롭게 사용자 지정할 수 있습니다. 기본적으로, 이미지 저장소는 애플리케이션 패키지를 저장하기 위한 플러그형 리포지토리입니다. 애플리케이션을 클러스터의 노드에 배포할 경우 해당 노드는 이미지 저장소에서 애플리케이션 패키지의 콘텐츠를 다운로드합니다. ImageStoreConnectionString은 클라이언트와 노드 모두에 필요한 정보를 모두 포함하는 설정으로 지정된 클러스터에 대한 올바른 이미지 저장소를 찾습니다.

현재 세 가지 종류의 가능한 이미지 저장소 공급자가 있고 해당하는 연결 문자열은 다음과 같습니다.

1. 이미지 저장소 서비스: "fabric:ImageStore"

2. 파일 시스템: "file:[file system path]"

3. Azure Storage: "xstore:DefaultEndpointsProtocol=https;AccountName=[...];AccountKey=[...];Container=[...]"

프로덕션에 사용되는 공급자 유형은 이미지 저장소 서비스이며 Service Fabric Explorer에서 확인할 수 있는 지속형 상태 저장 시스템 서비스입니다. 

![이미지 저장소 서비스][img_is]

클러스터 자체 내의 시스템 서비스에서 이미지 저장소를 호스팅하면 패키지 리포지토리에 대한 외부 종속성을 제거하고 저장소의 위치를 제어할 수 있게 됩니다. 단독으로 그렇지 않으면 이미지 저장소에 대한 향후 개선은 먼저 이미지 저장소 공급자를 대상으로 할 가능성이 큽니다. 클라이언트가 대상 클러스터에 이미 연결되어 있으므로 이미지 저장소 서비스 공급자에 대한 연결 문자열에는 고유한 정보가 없습니다. 클라이언트는 시스템 서비스를 대상으로 하는 프로토콜을 사용해야 함을 알고 있어야 합니다.

개발 중에 one-box 로컬 클러스터에 이미지 저장소 서비스가 아닌 파일 시스템 공급자를 사용하여 클러스터를 약간 더 빠르게 부트스트랩합니다. 차이점은 일반적으로 작지만 개발 중인 사용자 대부분에게 유용한 최적화입니다. 다른 저장소 공급자 유형도 one-box 로컬 클러스터를 배포할 수 있지만 개발/테스트 워크플로가 공급자에 관계 없이 동일하게 유지되기 때문에 일반적으로 작업을 수행할 필요가 없습니다. Azure Storage 공급자는 오직 Image Store Service 공급자가 도입되기 전에 배포된 기존 클러스터의 레거시 지원을 위해 존재합니다.

또한 파일 시스템 공급자 또는 Azure Storage 공급자는 여러 클러스터 간에 Image Store를 공유하는 방법으로 사용하면 안됩니다. 그렇지 않으면 각 클러스터에서 충돌하는 데이터를 Image Store에 쓸 수 있으므로 클러스터 구성 데이터가 손상됩니다. 프로비전된 애플리케이션 패키지를 여러 클러스터 간에 공유하려면 [sfpkg][12] 파일을 사용해야 하며, 이 파일은 다운로드 URI와 함께 아무 외부 저장소에 업로드할 수 있습니다.

따라서 ImageStoreConnectionString은 구성할 수 있지만 기본 설정만 사용하면 됩니다. Visual Studio를 통해 Azure에 게시할 경우 매개 변수가 적절하게 자동으로 설정됩니다. Azure에서 호스트된 클러스터에 프로그래밍 방식으로 배포하는 경우 연결 문자열은 항상 "fabric:ImageStore"입니다. 확실하지 않은 경우에도 [PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx) 또는 [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest)에서 클러스터 매니페스트를 검색하여 해당 값을 언제든지 확인할 수 있습니다. 온-프레미스 테스트 및 프로덕션 클러스터는 모두 항상 이미지 저장소 서비스 공급자를 사용하도록 구성되어야 합니다.

### <a name="next-steps"></a>다음 단계
[PowerShell을 사용하여 애플리케이션 배포 및 제거][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
