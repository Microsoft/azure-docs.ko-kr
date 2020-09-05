---
title: 자습서 - 가상 네트워크에 Azure Spring Cloud 배포
description: Azure Spring Cloud를 가상 네트워크에 배포합니다(v-net 삽입).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: a7905ae0fdbd797d9b544cb71f44b82af1295246
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688456"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>자습서: Azure 가상 네트워크에 Azure Spring Cloud 배포(VNet 삽입)

이 자습서에서는 Azure Spring Cloud 서비스 인스턴스를 가상 네트워크에 배포하는 방법에 대해 설명합니다. 이를 VNet 삽입이라고도 합니다.  

배포를 통해 다음을 수행할 수 있습니다.

* 회사 네트워크의 인터넷에서 Azure Spring Cloud 앱 및 서비스 런타임 격리
* 온-프레미스 데이터 센터의 시스템 및/또는 다른 가상 네트워크의 Azure 서비스와의 Azure Spring Cloud 상호 작용
* Azure Spring Cloud에 대한 인바운드 및 아웃바운드 네트워크 통신을 제어하는 고객의 역량 강화

## <a name="prerequisites"></a>사전 요구 사항
[Azure Portal에서 리소스 공급자 등록](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) 지침을 따르거나 다음 az CLI 명령을 실행하여 `Microsoft.AppPlatform` Azure Spring Cloud 리소스 공급자를 등록해야 합니다.

```azurecli
az provider register --namespace Microsoft.AppPlatform
```
## <a name="virtual-network-requirements"></a>가상 네트워크 요구 사항
Azure Spring Cloud 서비스 인스턴스를 배포하는 가상 네트워크는 다음 요구 사항을 충족해야 합니다.

* **위치**: 가상 네트워크는 Azure Spring Cloud 서비스 인스턴스와 동일한 위치에 있어야 합니다.
* **구독**: 가상 네트워크는 Azure Spring Cloud 서비스 인스턴스와 동일한 구독 상태에 있어야 합니다.
* **서브넷**: 가상 네트워크는 다음과 같은 Azure Spring Cloud 서비스 인스턴스 전용의 두 서브넷을 포함해야 합니다. 
    * 서비스 런타임용 서브넷
    * Spring Boot 마이크로 서비스 애플리케이션용 서브넷 
    * 이러한 서브넷과 Azure Spring Cloud 서비스 인스턴스 간에는 일대일 관계가 있습니다. 단일 서브넷에서 여러 서비스 인스턴스를 공유할 수 없습니다. 배포하는 각 서비스 인스턴스에 대해 새 서브넷을 사용해야 합니다.
* **주소 공간**: 서비스 런타임 서브넷에 대한 하나의 CIDR 블록은 최대 /28이고, Spring Boot 마이크로 서비스 애플리케이션 서브넷에 대한 또 하나의 CIDR 블록은 최대 /24입니다.
* **경로 테이블**: 서브넷에는 연결된 기존 경로 테이블이 없어야 합니다.

다음 절차에서는 Azure Spring Cloud 인스턴스를 포함하도록 가상 네트워크를 설정하는 방법에 대해 설명합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기
Azure Spring Cloud 서비스 인스턴스를 호스팅하는 가상 네트워크가 이미 있는 경우 1, 2 및 3단계를 건너뜁니다. 4단계부터 시작하여 가상 네트워크에 대한 서브넷을 준비할 수 있습니다.

1. Azure Portal 메뉴에서 **리소스 만들기**를 선택합니다. Azure Marketplace에서 **네트워킹** > **가상 네트워크**를 선택합니다.

1. **가상 네트워크 만들기** 대화 상자에서 다음 정보를 입력하거나 선택합니다.

    |설정          |값                                             |
    |-----------------|--------------------------------------------------|
    |Subscription     |구독을 선택합니다.                         |
    |Resource group   |리소스 그룹을 선택하거나 새로 만듭니다.  |
    |Name             |*azure-spring-cloud-vnet*을 입력합니다.                   |
    |위치         |**미국 동부**를 선택합니다.                                |

1. **다음: IP 주소>** 를 클릭합니다. 
 
1. IPv4 주소 공간에 대해 10.1.0.0/16을 입력합니다.

1. **서브넷 추가**를 선택한 다음, **서브넷 이름**에 대해 *service-runtime-subnet*을 입력하고, **서브넷 주소 범위**에 대해 10.1.0.0/24를 입력합니다. 그런 다음, **추가**를 클릭합니다.

1. **서브넷 추가**를 다시 선택한 다음, **서브넷 이름**에 대해 *apps-subnet*을 입력하고, **서브넷 주소 범위**에 대해 10.1.1.0/24를 입력합니다.  **추가**를 클릭합니다.

1. **검토 + 만들기**를 클릭합니다. 나머지는 기본값으로 두고, **만들기**를 클릭합니다.

## <a name="grant-service-permission-to-the-virtual-network"></a>가상 네트워크에 서비스 권한 부여

이전에 만든 *azure-spring-cloud-vnet* 가상 네트워크를 선택합니다.

1. **액세스 제어(IAM)** 를 선택한 다음, **추가 > 역할 할당 추가**를 차례로 선택합니다.

    ![v-net에 대한 액세스 제어](./media/spring-cloud-v-net-injection/access-control.png)

2. **역할 할당 추가** 대화 상자에서 다음 정보를 입력하거나 선택합니다.

    |설정  |값                                             |
    |---------|--------------------------------------------------|
    |역할     |**소유자**를 선택합니다.                                  |
    |선택   |*Azure Spring Cloud 리소스 공급자*를 입력합니다.      |

    그런 다음, *Azure Spring Cloud 리소스 공급자*를 선택하고, **저장**을 클릭합니다.

    ![Azure Spring Cloud 리소스 공급자를 v-net에 부여](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

또한 다음 az CLI 명령을 실행하여 이를 수행할 수도 있습니다.

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>가상 네트워크에 Azure Spring Cloud 서비스 인스턴스 배포

1. https://ms.portal.azure.com 에서 Azure Portal을 엽니다.

1. 위쪽 검색 상자에서 **Azure Spring Cloud**를 검색하고, 결과에서 **Azure Spring Cloud**를 선택합니다.

1. **Azure Spring Cloud** 페이지에서 **+ 추가**를 선택합니다.

1. Azure Spring Cloud **만들기** 페이지에 있는 양식을 채웁니다. 

1. 가상 네트워크와 동일한 리소스 그룹 및 지역을 선택합니다.

1. **서비스 세부 정보** 아래의 **이름**에 대해 *azure-spring-cloud-vnet*을 선택합니다.

1. **네트워킹** 탭을 선택하고, 다음을 선택합니다.

    |설정                                |값                                             |
    |---------------------------------------|--------------------------------------------------|
    |사용자 고유의 가상 네트워크에 배포     |**예**를 선택합니다.                                    |
    |가상 네트워크                        |*azure-spring-cloud-vnet*을 선택합니다.                  |
    |서비스 런타임 서브넷                 |*service-runtime-subnet*을 선택합니다.                   |
    |Spring Boot 마이크로 서비스 앱 서브넷   |*apps-subnet*을 선택합니다.                              |

    ![네트워킹 만들기 탭](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. **검토 + 만들기**를 클릭합니다.

1. 사양을 확인하고 **만들기**를 클릭합니다.

배포 후에는 Azure Spring Cloud 서비스 인스턴스에 대한 네트워크 리소스를 호스팅하는 두 개의 추가 리소스 그룹이 구독에 만들어집니다.  **홈**으로 이동한 다음, 위쪽 메뉴 항목에서 **리소스 그룹**을 선택하여 다음과 같은 새 리소스 그룹을 찾습니다.

*azure-spring-cloud-service-runtime_{service instance name}_{service instance region}* 이라는 리소스 그룹에는 서비스 인스턴스의 서비스 런타임에 대한 네트워크 리소스가 포함되어 있습니다.

  ![서비스 런타임](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

*azure-spring-cloud-service-runtime_{service instance name}_{service instance region}* 이라는 리소스 그룹에는 서비스 인스턴스의 Spring Boot 마이크로 서비스 애플리케이션에 대한 네트워크 리소스가 포함되어 있습니다.

  ![앱 리소스 그룹](./media/spring-cloud-v-net-injection/apps-resource-group.png)

이러한 네트워크 리소스는 위에서 만든 가상 네트워크에 연결됩니다.

  ![연결된 디바이스가 있는 V-net](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > 리소스 그룹은 Azure Spring Cloud 서비스에서 완전히 관리됩니다. 내부에 있는 리소스를 수동으로 삭제하거나 수정하지 마세요.

## <a name="next-steps"></a>다음 단계

[VNet에서 애플리케이션을 Azure Spring Cloud에 배포](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>참고 항목

- [VNET에서 Azure Spring Cloud 문제 해결](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [VNET에서 Azure Spring Cloud를 실행하는 고객의 책임](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)