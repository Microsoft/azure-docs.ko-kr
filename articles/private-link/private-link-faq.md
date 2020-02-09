---
title: Azure 개인 링크 FAQ (질문과 대답)
description: Azure 개인 링크에 대해 알아봅니다.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7870b68ca931123d50e88e846aa066ce53972dbc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75349930"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure 개인 링크 FAQ (질문과 대답)

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Azure 개인 엔드포인트 및 Azure 개인 링크 서비스 란 무엇 인가요?

- **[Azure 개인 엔드포인트](private-endpoint-overview.md)** : Azure 개인 엔드포인트은 Azure 개인 링크를 통해 제공 되는 서비스에 개인적이 고 안전 하 게 연결 하는 네트워크 인터페이스입니다. 개인 엔드포인트을 사용 하 여 개인 링크 또는 개인 링크 서비스에 대 한 개인 링크를 지 원하는 Azure PaaS 서비스에 연결할 수 있습니다.
- **[Azure 개인 링크 서비스](private-link-service-overview.md)** : Azure 개인 링크 서비스는 서비스 공급자가 만든 서비스입니다. 현재 개인 링크 서비스는 표준 Load Balancer의 프런트 엔드 IP 구성에 연결할 수 있습니다. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>개인 링크를 사용 하는 경우 트래픽이 어떻게 전송 되나요?
트래픽은 Microsoft 백본을 통해 개인적으로 전송 됩니다. 인터넷을 통과 하지 않습니다.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>서비스 엔드포인트과 개인 엔드포인트의 차이점은 무엇 인가요?
- 개인 엔드포인트을 사용 하는 경우 세분화 된 조각화를 제공 하는 특정 서비스 뒤의 특정 리소스에 대 한 네트워크 액세스가 부여 됩니다. 또한 트래픽은 공용 엔드포인트을 사용 하지 않고 온-프레미스에서 서비스 리소스에 도달할 수 있습니다.
- 서비스 엔드포인트은 공개적으로 라우팅 가능한 IP 주소를 유지 합니다.  개인 엔드포인트은 개인 엔드포인트이 구성 된 가상 네트워크의 주소 공간에 있는 개인 IP입니다.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>개인 링크 서비스와 개인 엔드포인트 간의 관계는 무엇 인가요?
개인 엔드포인트은 Azure PaaS 서비스 및 사용자 고유의 개인 링크 서비스를 비롯 한 여러 개인 링크 리소스 유형에 대 한 액세스를 제공 합니다. 이는 일 대 다 관계입니다. 하나의 개인 링크 서비스는 여러 개인 엔드포인트에서 연결을 수신할 수 있습니다. 반면, 하나의 개인 엔드포인트은 하나의 개인 링크 서비스에만 연결할 수 있습니다.    

## <a name="private-endpoint"></a>프라이빗 엔드포인트 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>동일한 VNet에 여러 개인 엔드포인트을 만들 수 있나요? 서로 다른 서비스에 연결할 수 있나요? 
예. 동일한 VNet 또는 서브넷에 여러 개인 엔드포인트을 포함할 수 있습니다. 서로 다른 서비스에 연결할 수 있습니다.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>전용 엔드포인트에 전용 서브넷이 필요 한가요? 
아닙니다. 전용 엔드포인트에 전용 서브넷이 필요 하지 않습니다. 서비스가 배포 된 VNet의 모든 서브넷에서 개인 엔드포인트 IP를 선택할 수 있습니다.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>개인 엔드포인트이 Azure Active Directory 테넌트 간 개인 링크 서비스에 연결할 수 있나요? 
예. 개인 엔드포인트은 AD 테넌트를 통해 개인 링크 서비스 또는 Azure PaaS에 연결할 수 있습니다.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>개인 엔드포인트이 Azure 지역에서 Azure PaaS 리소스에 연결할 수 있나요?
예. 개인 엔드포인트은 Azure 지역에서 Azure PaaS 리소스에 연결할 수 있습니다.

## <a name="private-link-service"></a>Private Link 서비스
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>개인 링크 서비스를 만들기 위한 필수 구성 요소는 무엇 인가요? 
Service 백 엔드는 가상 네트워크에 있고 표준 Load Balancer 뒤에 있어야 합니다.
 
### <a name="how-can-i-scale-my-private-link-service"></a>개인 링크 서비스의 크기를 조정 하려면 어떻게 해야 하나요? 
여러 가지 방법으로 개인 링크 서비스를 확장할 수 있습니다. 
- 표준 Load Balancer 뒤에 백 엔드 Vm을 풀에 추가 합니다. 
- 개인 링크 서비스에 IP를 추가 합니다. 개인 링크 서비스 당 최대 8 개의 Ip를 허용 합니다.  
- 표준 Load Balancer에 새 개인 링크 서비스를 추가 합니다. 부하 분산 장치 당 최대 8 개의 개인 링크 서비스를 허용 합니다.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>내 서비스를 여러 개인 엔드포인트에 연결할 수 있나요?
예. 하나의 개인 링크 서비스는 여러 개인 엔드포인트에서 연결을 수신할 수 있습니다. 그러나 하나의 개인 엔드포인트은 하나의 개인 링크 서비스에만 연결할 수 있습니다.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>개인 링크 서비스의 노출을 제어 하려면 어떻게 해야 하나요?
개인 링크 서비스에서 표시 유형 구성을 사용 하 여 노출을 제어할 수 있습니다. 표시 유형은 세 가지 설정을 지원 합니다.

- **None** -RBAC 액세스 권한이 있는 구독만 서비스를 찾을 수 있습니다. 
- 허용 목록 및 RBAC 액세스 권한이 있는 **제한적** 전용 구독은 서비스를 찾을 수 있습니다. 
- **모두** -모든 사용자가 서비스를 찾을 수 있습니다. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>기본 Load Balancer를 사용 하 여 개인 링크 서비스를 만들 수 있나요? 
아닙니다. 기본 Load Balancer에 대 한 개인 링크 서비스는 지원 되지 않습니다.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>개인 링크 서비스에 전용 서브넷이 필요 한가요? 
아닙니다. 개인 링크 서비스에는 전용 서브넷이 필요 하지 않습니다. VNet에서 서비스가 배포 된 모든 서브넷을 선택할 수 있습니다.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Azure 개인 링크를 사용 하는 서비스 공급자입니다. 모든 고객에 게 고유한 IP 공간이 있는지와 IP 공간이 겹치지 않도록 해야 하나요? 
아닙니다. Azure 개인 링크는이 기능을 제공 합니다. 따라서 고객의 주소 공간에 겹치지 않는 주소 공간이 있을 필요는 없습니다. 

##  <a name="next-steps"></a>다음 단계

- [Azure 개인 링크](private-link-overview.md) 에 대 한 자세한 정보
