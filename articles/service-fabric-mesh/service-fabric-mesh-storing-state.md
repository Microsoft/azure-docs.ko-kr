---
title: Azure Service Fabric Mesh의 상태 저장소 옵션 | Microsoft Docs
description: Azure Service Fabric Mesh에서 실행되는 Service Fabric Mesh 애플리케이션에 상태를 안정적으로 저장하는 방법을 알아봅니다.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: ef51040d1bad74ee74d5901d1f5acbe875c02a07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810519"
---
# <a name="state-management-with-service-fabric"></a>Service Fabric을 사용한 상태 관리

Service Fabric은 상태 저장소에 대한 다양한 옵션을 지원합니다. 상태 관리 패턴 및 Service Fabric의 개념적인 개요는 [Service Fabric 개념: 상태](/azure/service-fabric/service-fabric-concepts-state)를 참조하세요. 이러한 모든 동일한 개념은 Service Fabric Mesh 내부 또는 외부에서 서비스를 실행할지 여부에 관계없이 적용됩니다. 

Service Fabric Mesh를 사용하면 새 애플리케이션을 쉽게 배포하고 Azure에 호스트되는 기존 데이터 저장소에 연결할 수 있습니다. 원격 데이터베이스를 사용하는 것 외에도 서비스에 로컬 또는 원격 저장소가 필요한지 여부에 따라 다양한 데이터 저장 옵션이 있습니다. 

## <a name="volumes"></a>볼륨

컨테이너는 종종 임시 디스크를 이용합니다. 그러나 임시 디스크는 사용 후 삭제되므로 컨테이너가 충돌하면 새 임시 디스크를 가져와 정보를 잃게 됩니다. 임시 디스크에 대한 정보를 다른 컨테이너와 공유하는 것도 어렵습니다. 볼륨은 상태를 유지하는 데 사용할 수 있는 컨테이너 인스턴스 내에 탑재된 디렉터리입니다. 볼륨은 범용 파일 스토리지를 제공하며 이를 통해 일반 디스크 I/O 파일 API를 사용하여 파일을 읽고 쓸 수 있습니다. 볼륨 리소스는 디렉터리를 탑재하는 방법 및 사용할 지원 스토리지를 설명합니다. Azure File Storage 또는 Service Fabric 볼륨 디스크에 데이터를 저장하도록 선택할 수 있습니다.

![볼륨][image3]

### <a name="service-fabric-reliable-volume"></a>Service Fabric 신뢰할 수 있는 볼륨

Service Fabric 신뢰할 수 있는 볼륨은 로컬 볼륨을 컨테이너에 탑재하는 데 사용된 Docker 볼륨 드라이버입니다. 읽기 및 쓰기는 로컬 작업이며 빠릅니다. 데이터는 보조 노드로 복제되어 가용성이 높습니다. 장애 조치(failover)도 빠릅니다. 컨테이너가 충돌하면 데이터의 복사본이 이미 있는 노드로 장애 조치됩니다. 예를 들어 [Service Fabric 신뢰할 수 있는 볼륨을 사용하여 앱을 배포하는 방법](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md)을 참조하세요.

### <a name="azure-files-volume"></a>Azure Files 볼륨

Azure Files 볼륨은 Azure Files 공유를 컨테이너에 탑재하는 데 사용된 Docker 볼륨 드라이버입니다. Azure File Storage는 네트워크 스토리지를 사용하므로 네트워크를 통해 읽기 및 쓰기가 수행됩니다. Service Fabric 신뢰할 수 있는 볼륨에 비해, Azure Files Storage는 성능은 떨어지지만 보다 저렴하고 완전히 신뢰할 수 있는 데이터 옵션을 제공합니다. 예를 들어 [Azure Files 볼륨으로 앱 배포 방법](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

애플리케이션 모델에 대한 자세한 내용은 [Service Fabric 리소스](service-fabric-mesh-service-fabric-resources.md)를 참조하세요.

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
