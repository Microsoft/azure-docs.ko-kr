---
title: Azure Cloud Services란 | Microsoft Docs
description: 특히 확장 가능 하 고 안정적 이며 운영 비용이 저렴 한 응용 프로그램을 지원 하도록 설계 된 Azure Cloud Services에 대해 알아보세요.
services: cloud-services
author: tgore03
ms.service: multiple
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 0013a3a29bae9d2dde7896b3ae23d0d358946f2b
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224291"
---
# <a name="overview-of-azure-cloud-services"></a>Azure Cloud Services 개요
Azure Cloud Services는 [PaaS(Platform-as-a-Service)](https://azure.microsoft.com/overview/what-is-paas/)의 예입니다. [Azure App Service](../app-service/overview.md)와 마찬가지로 이 기술은 확장성이 있고 안정적이며 운영 비용이 저렴한 애플리케이션을 지원하도록 설계되었습니다. App Service가 VM(가상 머신)에서 호스팅되는 것과 마찬가지로 Azure Cloud Services도 동일합니다. 하지만, VM보다 자세히 제어해야 합니다. 자체 소프트웨어를 Azure Cloud Services를 사용하는 VM에 설치하고 원격으로 액세스할 수 있습니다.

![Azure Cloud Services 다이어그램](./media/cloud-services-choose-me/diagram.png)

더욱 긴밀하게 제어할수록 사용 편의성은 낮아집니다. 추가 제어 옵션이 필요한 경우가 아니면 일반적으로는 Azure Cloud Services에 비해 App Service의 Web Apps에서 웹 애플리케이션을 작동하고 실행하는 것이 더 빠르고 간편합니다.

Azure Cloud Services 역할에는 두 가지 형식이 있습니다. 둘 사이의 유일한 차이점은 VM에서 역할을 호스팅하는 방식입니다.

* **웹 역할**: IIS를 통해 앱을 자동으로 배포하고 호스팅합니다.

* **작업자 역할**: IIS를 사용하지 않고 앱을 독립 실행형으로 실행합니다.

예를 들어, 간단한 애플리케이션은 단일 웹 역할만 사용하여 웹 사이트를 제공할 수 있습니다. 좀더 복잡한 애플리케이션은 웹 역할을 사용하여 사용자로부터의 수신 요청을 처리한 후 해당 요청을 작업자 역할에 전달하여 처리할 수 있습니다. (이 통신은 [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) 또는 [Azure Queue Storage](../storage/common/storage-introduction.md)를 사용할 수 있습니다.)

이전 그림에 나와 있는 것처럼 단일 애플리케이션의 모든 VM은 동일한 클라우드 서비스에서 실행됩니다. 사용자는 애플리케이션 VM의 자동 부하 분산 요청으로 하나의 공용 IP 주소를 통해 애플리케이션에 액세스합니다. 플랫폼은 단일 하드웨어 오류 지점을 방지하는 방식으로 Azure Cloud Services 애플리케이션에 VM을 [확장 및 배포](cloud-services-how-to-scale-portal.md)합니다.

애플리케이션이 VM에서 실행된다 해도 Azure Cloud Services가 IaaS(서비스 제공 인프라)가 아닌 PaaS를 제공한다는 사실을 이해하는 것이 중요합니다. 이에 대한 한 가지 방법은 다음과 같습니다. Azure Virtual Machines와 같은 IaaS를 사용하여 먼저 애플리케이션을 실행하는 환경을 만들고 구성합니다. 그런 다음, 이 환경에 애플리케이션을 배포합니다. 각 VM에 운영 체제의 새로 페치된 버전을 배포하는 것처럼 무언가를 수행함으로써 이 환경 대부분을 관리할 책임이 있습니다. 각 VM에 운영 체제의 새 패치 버전을 배포하는 것과 같은 작업을 수행하며 많은 작업에 대해 관리해야 합니다. 애플리케이션을 배포하기만 하면 됩니다. 응용 프로그램이 실행되는 플랫폼을 관리합니다(운영 체제의 새 버전 배포 포함).

## <a name="scaling-and-management"></a>확장 및 관리
Azure Cloud Services를 사용하면 가상 머신을 만들지 않습니다. 대신 “세 개 웹 역할 인스턴스” 및 “두 개 작업자 역할 인스턴스”와 같이 원하는 개수를 Azure에게 알려 주는 구성 파일을 제공합니다. 그러면, 플랫폼에서 이를 만듭니다. 사용자는 이들 지원 VM의 [실제 크기](cloud-services-sizes-specs.md) 를 선택하지만 명시적으로 직접 만들는지는 않습니다. 애플리케이션이 더 큰 부하를 처리해야 하면 더 많은 VM을 요구할 수 있으며 Azure가 그러한 인스턴스를 만듭니다. 부하가 감소하면 이러한 인스턴스를 종료하여 지불을 중지할 수 있습니다.

Azure Cloud Services 애플리케이션은 사용자가 사용할 수 있도록 일반적으로 두 단계 프로세스를 통해 만들어집니다. 우선 개발자가 플랫폼의 준비 영역에 [애플리케이션을 업로드](cloud-services-how-to-create-deploy-portal.md)합니다. 개발자가 애플리케이션을 사용할 준비가 되면 Azure Portal을 사용하여 준비를 프로덕션으로 바꿉니다. [준비와 프로덕션 간의 이러한](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) 전환은 가동 중지 시간 없이 수행될 수 있어서 사용자를 방해하지 않고도 실행 중인 애플리케이션을 새 버전으로 업그레이드할 수 있습니다.

## <a name="monitoring"></a>모니터링
Azure Cloud Services는 모니터링도 제공합니다. Virtual Machines처럼 오류가 발생한 물리적 서버를 검색하여 해당 서버에서 실행 중이었던 VM을 새 컴퓨터에서 다시 시작합니다. 하지만 Azure Cloud Services는 하드웨어 오류뿐만 아니라 오류가 발생한 VM과 애플리케이션도 검색합니다. Virtual Machines와 달리 클라우드 서비스에는 각 웹 역할 및 작업자 역할 내에 에이전트가 있어서 오류가 발생할 때 새 VM 및 애플리케이션 인스턴스를 시작할 수 있습니다.

Azure Cloud Services의 PaaS 특성에는 다른 것도 있습니다. 가장 중요한 것 중 하나는 웹 역할 또는 작업자 역할 인스턴스에 오류가 발생할 때 이 기술로 빌드된 애플리케이션이 제대로 실행되도록 작성되어야 한다는 점입니다. 이렇게 하려면 Azure Cloud Services 애플리케이션이 자체 VM의 파일 시스템 상태를 관리해서는 안 됩니다. Virtual Machines를 사용하여 만든 VM의 경우와 달리 Azure Cloud Services VM에 대해 실행된 쓰기는 영구적이지 않습니다. Virtual Machines 데이터 디스크와 같은 것도 없습니다. 대신 Azure Cloud Services 애플리케이션은 Azure SQL Database, Blob, 테이블 또는 일부 기타 외부 스토리지에 모든 상태를 명확하게 작성해야 합니다. 이 방법으로 애플리케이션을 빌드하면 더 쉽게 확장하고 오류를 방어할 수 있습니다. 이 두 가지는 모두 Azure Cloud Services의 중요한 목표입니다.

## <a name="next-steps"></a>다음 단계
* [.NET으로 클라우드 서비스 앱 만들기](cloud-services-dotnet-get-started.md) 
* [Node.js로 클라우드 서비스 앱 만들기](cloud-services-nodejs-develop-deploy-app.md) 
* [PHP로 클라우드 서비스 앱 만들기](../cloud-services-php-create-web-role.md) 
* [Python에서 클라우드 서비스 앱 만들기](cloud-services-python-ptvs.md)






