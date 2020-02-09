---
title: Azure Traffic Manager에서 엔드포인트 관리 | Microsoft Docs
description: 이 문서는 Azure Traffic Manager에서 엔드포인트를 추가, 제거 및 사용하거나 사용하지 않도록 설정하는 데 도움이 됩니다.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: rohink
ms.openlocfilehash: 2aee4282fe6a66151ea39018f0d6c0db51f2c027
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939128"
---
# <a name="add-disable-enable-or-delete-endpoints"></a>엔드포인트 추가, 사용 안 함, 사용 또는 삭제

Azure App Service의 Web Apps는 웹 사이트 모드에 관계없이 데이터 센터 내의 웹 사이트에 대해 이미 장애 조치(Failover) 및 라운드 로빈 트래픽 라우팅 기능을 제공합니다. Azure Traffic Manager를 통해 다른 데이터 센터의 웹 사이트와 클라우드 서비스에 대해 장애 조치(Failover) 및 라운드 로빈 트래픽 라우팅을 지정할 수 있습니다. 해당 기능을 제공하는 데 필요한 첫 번째 단계는 Traffic Manager에 클라우드 서비스 또는 웹 사이트 엔드포인트를 추가하는 것입니다.

Traffic Manager 프로필의 일부인 개별 엔드포인트를 사용하지 않도록 설정할 수도 있습니다. 엔드포인트를 사용하지 않도록 설정하는 경우 프로필의 일부로 유지되지만 엔드포인트가 없는 것처럼 프로필이 작동합니다. 이 작업은 유지 관리 모드에 있거나 다시 배포할 엔드포인트를 일시적으로 제거하는 데 유용합니다. 엔드포인트가 다시 작동하여 실행되면 사용하도록 설정할 수 있습니다.

> [!NOTE]
> 엔드포인트를 사용하지 않도록 설정하는 경우 Azure의 엔드포인트 배포 상태에는 영향을 주지 않습니다. 정상 엔드포인트는 실행 상태로 유지되며 Traffic Manager에서 사용하지 않도록 설정된 경우에도 트래픽을 수신할 수 있습니다. 또한 한 프로필에서 엔드포인트를 사용하지 않도록 설정해도 다른 프로필의 해당 엔드포인트 상태에는 영향을 주지 않습니다.

## <a name="to-add-a-cloud-service-or-an-app-service-endpoint-to-a-traffic-manager-profile"></a>Traffic Manager 프로필에 클라우드 서비스 또는 App Service 엔드포인트를 추가하려면

1. 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털의 검색 창에서 수정하려는 **Traffic Manager 프로필** 이름을 검색한 다음 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
3. **Traffic Manager 프로필** 블레이드에서 **설정** 섹션의 **엔드포인트**를 클릭합니다.
4. 표시되는 **엔드포인트** 블레이드에서 **추가**를 클릭합니다.
5. **엔드포인트 추가** 블레이드에서 다음과 같이 완료합니다.
    1. **형식**의 경우 **Azure 엔드포인트**를 클릭합니다.
    2. 이 엔드포인트를 인식하는 기준으로 사용할 **이름**을 제공합니다.
    3. **대상 리소스 형식**의 경우 드롭다운 목록에서 적절한 리소스 형식을 선택합니다.
    4. **대상 리소스**에서 **선택...**  선택기를 클릭하여 **리소스 블레이드**에서 동일한 구독 아래의 리소스에 나열합니다. 표시되는 **리소스** 블레이드에서 첫 번째 엔드포인트로 추가할 서비스를 선택합니다.
    5. **우선 순위**의 경우 **1**로 선택합니다. 이제 모든 트래픽이 정상일 경우 이 엔드포인트로 전송됩니다.
    6. **사용 안 함으로 추가**를 선택 취소 상태로 유지합니다.
    7. **확인**을 클릭합니다.
6.  다음 Azure 엔드포인트를 추가하려면 4, 5 단계를 반복합니다. **우선 순위** 값을 **2**로 설정한 상태에서 추가해야 합니다.
7.  두 엔드포인트 추가가 완료되면 **온라인**인 모니터링 상태와 함께 **Traffic Manager 프로필** 블레이드에 표시됩니다.

> [!NOTE]
> *장애 조치* 트래픽 라우팅 방법을 사용하여 프로필에서 엔드포인트를 추가하거나 제거한 후에는 장애 조치 우선 순위 목록을 원하는 방식으로 정렬할 수 없습니다. 대신 구성 페이지에서 장애 조치 우선 순위 목록의 순서를 조정할 수 있습니다. 자세한 내용은 [장애 조치(Failover) 트래픽 라우팅 구성](traffic-manager-configure-failover-routing-method.md)을 참조하세요.

## <a name="to-disable-an-endpoint"></a>엔드포인트를 사용하지 않도록 설정하려면

1. 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털의 검색 창에서 수정하려는 **Traffic Manager 프로필** 이름을 검색한 다음 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
3. **Traffic Manager 프로필** 블레이드에서 **설정** 섹션의 **엔드포인트**를 클릭합니다. 
4. 사용 하지 않도록 설정할 엔드포인트을 클릭 합니다.
5. **엔드포인트** 블레이드에서 엔드포인트 상태를 **비활성화**로 변경하고 **저장**을 클릭합니다.
6. 클라이언트에서 트래픽을 TTL(활성 시간) 기간의 엔드포인트로 계속 보냅니다. Traffic Manager 프로필의 구성 페이지에서 TTL을 변경할 수 있습니다.

## <a name="to-enable-an-endpoint"></a>엔드포인트를 사용하도록 설정하려면

1. 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털의 검색 창에서 수정하려는 **Traffic Manager 프로필** 이름을 검색한 다음 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
3. **Traffic Manager 프로필** 블레이드에서 **설정** 섹션의 **엔드포인트**를 클릭합니다. 
4. 사용 하도록 설정할 엔드포인트을 클릭 합니다.
5. **엔드포인트** 블레이드에서 엔드포인트 상태를 **활성화**로 변경하고 **저장**을 클릭합니다.
6. 클라이언트에서 트래픽을 TTL(활성 시간) 기간의 엔드포인트로 계속 보냅니다. Traffic Manager 프로필의 구성 페이지에서 TTL을 변경할 수 있습니다.

## <a name="to-delete-an-endpoint"></a>엔드포인트를 삭제하려면

1. 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털의 검색 창에서 수정하려는 **Traffic Manager 프로필** 이름을 검색한 다음 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
3. **Traffic Manager 프로필** 블레이드에서 **설정** 섹션의 **엔드포인트**를 클릭합니다. 
4. 삭제할 엔드포인트을 클릭 합니다.
5. **엔드포인트** 블레이드에서 **삭제** 를 클릭 합니다.


## <a name="next-steps"></a>다음 단계

* [Traffic Manager 프로필 관리](traffic-manager-manage-profiles.md)
* [라우팅 방법 구성](traffic-manager-configure-routing-method.md)
* [Traffic Manager 성능 저하 상태 문제 해결](traffic-manager-troubleshooting-degraded.md)
* [Traffic Manager 성능 고려 사항](traffic-manager-performance-considerations.md)
* [Traffic Manager 작업(REST API 참조)](https://go.microsoft.com/fwlink/p/?LinkID=313584)

