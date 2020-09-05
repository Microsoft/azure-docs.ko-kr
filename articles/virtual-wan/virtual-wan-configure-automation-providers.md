---
title: Azure 가상 WAN 파트너 자동화 지침 | Microsoft Docs
description: Azure 가상 WAN에 대 한 온-프레미스 VPN 또는 SD WAN CPE 또는 분기 장치를 연결 하 고 구성 하는 automation 환경을 설정 합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 0029f3fbcf96036a247356042e4c39d59f86a224
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208355"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>가상 WAN 파트너에 대 한 자동화 지침

이 문서는 Azure Virtual WAN의 분기 디바이스(고객 온-프레미스 VPN 디바이스 또는 SDWAN CPE)를 연결 및 구성하기 위해 자동화 환경을 설정하는 방법을 이해하는 데 도움이 됩니다. 이 문서는 IPsec/IKEv2 또는 IPsec/IKEv1을 통해 VPN 연결을 수용할 수 있는 분기 디바이스를 제공하는 공급자를 대상으로 합니다.

분기 디바이스(고객 온-프레미스 VPN 디바이스 또는 SDWAN CPE)는 일반적으로 프로비전될 컨트롤러/디바이스 대시보드를 사용합니다. SD-WAN 솔루션 관리자는 종종 네트워크로 연결하기 전에 관리 콘솔을 사용하여 디바이스를 사전 프로비전할 수 있습니다. 이 VPN 지원 디바이스는 컨트롤러에서 해당 컨트롤 평면 논리를 가져옵니다. VPN 디바이스 또는 SD-WAN 컨트롤러는 Azure API를 사용하여 Azure Virtual WAN에 대한 연결을 자동화할 수 있습니다. 이 연결 유형은 할당된 외부 연결 공용 IP 주소를 갖고 있는 온-프레미스 디바이스를 필요로 합니다.

## <a name="before-you-begin-automating"></a><a name ="before"></a>자동화를 시작하기 전에

* 디바이스에서 IPsec IKEv1/IKEv2를 지원하는지 확인합니다. [기본 정책](#default)을 참조하세요.
* Azure 가상 WAN에 대 한 연결을 자동화 하는 데 사용 하는 [REST api](#additional) 를 확인 합니다.
* Azure Virtual WAN의 포털 환경을 테스트합니다.
* 그런 다음, 자동화하려는 연결 단계의 부분을 결정합니다. 최소한 자동화하는 것이 좋습니다.

  * Access Control
  * Azure Virtual WAN에 분기 디바이스 정보 업로드
  * Azure 구성 다운로드 및 분기 디바이스에서 Azure Virtual WAN으로 연결 설정

### <a name="additional-information"></a><a name ="additional"></a>추가 정보

* 가상 허브 만들기를 자동화 하는 [REST API](https://docs.microsoft.com/rest/api/virtualwan/virtualhubs)
* 가상 WAN에 대 한 Azure VPN gateway를 자동화 하는 [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpngateways)
* VPNSite를 Azure VPN Hub에 연결 하는 [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnconnections)
* [기본 IPsec 정책](#default)

## <a name="customer-experience"></a><a name ="ae"></a>사용자 환경

Azure Virtual WAN과 함께 예상되는 고객 환경을 이해합니다.

  1. 일반적으로 가상 WAN 사용자는 Virtual WAN 리소스를 만들어 프로세스를 시작합니다.
  2. 사용자는 온-프레미스 시스템(사용자의 분기 컨트롤러 또는 VPN 디바이스 프로비저닝 소프트웨어)에 대한 서비스 주체 기반 리소스 그룹 액세스를 설정하여 Azure Virtual WAN으로 분기 정보를 작성합니다.
  3. 사용자는 이 때 UI에 로그인하고 서비스 주체 자격 증명을 설정하도록 결정할 수 있습니다. 완료되면 컨트롤러는 제공하는 자동화를 사용하여 분기 정보를 업로드할 수 있어야 합니다. Azure 측에서 동일한 수동 작업은 '사이트 만들기'입니다.
  4. Azure에서 사이트 (분기 장치) 정보를 사용할 수 있게 되 면 사용자는 사이트를 허브에 연결 합니다. 가상 허브는 Microsoft에서 관리하는 가상 네트워크입니다. 허브는 온-프레미스 네트워크(vpnsite)에서 연결을 활성화하는 다양한 서비스 엔드포인트를 포함합니다. 허브는 지역에서 네트워크의 핵심입니다. Azure 지역당 하나의 허브만 있을 수 있으며 그 안의 vpn 엔드포인트(vpngateway)가 이 프로세스 중 생성됩니다. VPN 게이트웨이는 대역폭 및 연결 요구 사항에 따라 적절하게 크기가 조정되는 확장 가능한 게이트웨이입니다. 분기 디바이스 컨트롤러 대시보드에서 가상 허브 및 vpngateway 생성을 자동화하도록 선택할 수 있습니다.
  5. 가상 허브가 사이트에 연결되면 사용자가 수동으로 다운로드할 수 있도록 구성 파일이 생성됩니다. 자동화가 들어오는 위치이며 사용자 환경을 원활하게 만듭니다. 사용자가 수동으로 다운로드하고 분기 디바이스를 구성하는 대신 자동화를 설정하고 UI에서 최소 클릭 환경을 제공할 수 있으므로 공유 키 불일치, IPSec 매개 변수 불일치, 구성 파일 가독성 등과 같은 일반적인 연결 문제를 완화합니다.
  6. 솔루션의 이 단계 끝에서 사용자는 분기 디바이스와 가상 허브 간의 원활한 사이트 간 연결을 갖게 됩니다. 다른 허브에서 추가 연결을 설정할 수도 있습니다. 각 연결은 활성-활성 터널입니다. 고객은 터널에 대한 각 링크에 다른 ISP를 사용하도록 선택할 수 있습니다.
  7. CPE 관리 인터페이스에서 문제 해결 및 모니터링 기능을 제공 하는 것이 좋습니다. 일반적인 시나리오에는 "고객이 cpe 문제로 인해 Azure 리소스에 액세스할 수 없음", "CPE 쪽에서 IPsec 매개 변수 표시" 등이 포함 됩니다.

## <a name="automation-details"></a><a name ="understand"></a>자동화 세부 정보

###  <a name="access-control"></a><a name="access"></a>Access Control

고객은 디바이스 UI에서 Virtual WAN에 적절한 액세스 제어를 설정할 수 있어야 합니다. 이 경우, Azure 서비스 주체를 사용하는 것이 좋습니다. 서비스 주체 기반 액세스는 분기 정보 업로드에 적합한 인증을 디바이스 컨트롤러에 제공합니다. 자세한 내용은 [서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)를 참조하세요. 이 기능은 Azure Virtual WAN 제품의 외부이지만 관련 세부 정보가 디바이스 관리 대시보드에 입력된 후 Azure에서 액세스를 설정하는 데 수행되는 일반적인 단계를 아래에 나열합니다.

* 온-프레미스 디바이스 컨트롤러에 대한 Azure Active Directory 애플리케이션을 만듭니다.
* 애플리케이션 ID 및 인증 키 가져오기
* 테넌트 ID 가져오기
* 애플리케이션을 “기여자”에 할당

###  <a name="upload-branch-device-information"></a><a name="branch"></a>분기 디바이스 정보 업로드

Azure에 분기 (온-프레미스 사이트) 정보를 업로드 하는 사용자 환경을 디자인 해야 합니다. VPNSite 용 [REST api](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) 를 사용 하 여 가상 WAN에서 사이트 정보를 만들 수 있습니다. 모든 분기 SDWAN/VPN 디바이스를 제공하거나 적절하게 디바이스 사용자 지정을 선택할 수 있습니다.

### <a name="device-configuration-download-and-connectivity"></a><a name="device"></a>디바이스 구성 다운로드 및 연결

이 단계는 Azure 구성 다운로드 및 분기 디바이스에서 Azure Virtual WAN으로 연결 설정을 포함합니다. 이 단계에서는 공급자를 사용하지 않는 고객은 수동으로 Azure 구성을 다운로드하고 온-프레미스 SDWAN/VPN 디바이스에 적용합니다. 공급자는 이 단계를 자동화해야 합니다. 추가 정보는 [REST api](https://docs.microsoft.com/rest/api/virtualwan/vpnsitesconfiguration/download) 다운로드를 참조 하세요. 장치 컨트롤러에서 ' GetVpnConfiguration ' REST API를 호출 하 여 Azure 구성을 다운로드할 수 있습니다.

**구성 정보**

  * 가상 허브에 연결된 Azure VNet은 ConnectedSubnet으로 표시됩니다.
  * VPN 연결은 경로 기반 구성을 사용 하 고 IKEv1 및 IKEv2 프로토콜을 모두 지원 합니다.

## <a name="device-configuration-file"></a><a name="devicefile"></a>장치 구성 파일

디바이스 구성 파일에는 온-프레미스 VPN 디바이스를 구성할 때 사용할 설정이 포함되어 있습니다. 이 파일을 볼 때 다음 정보를 확인합니다.

* **vpnSiteConfiguration -** 이 섹션은 Virtual WAN에 연결된 사이트로 설정된 디바이스 정보를 나타냅니다. 여기에는 분기 디바이스의 이름 및 공용 IP 주소가 포함됩니다.
* **vpnSiteConnections -** 이 섹션에서는 다음 정보를 제공합니다.

    * 가상 허브 VNet의 **주소 공간** 입니다.<br>예:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * 허브에 연결 된 Vnet의 **주소 공간** 입니다.<br>예제:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * 가상 허브 vpngateway의 **IP 주소**. vpngateway에는 활성-활성 구성의 2개 터널로 구성된 각 연결이 있기 때문에 이 파일에 두 IP 주소가 모두 나열됩니다. 이 예제에서는 각 사이트에 대한 “Instance0” 및 “Instance1”이 표시됩니다.<br>예제:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * BGP, 미리 공유한 키 등의 **Vpngateway 연결 구성 세부 정보**. PSK는 자동으로 생성되는 미리 공유한 키입니다. 사용자 지정 PSK의 개요 페이지에서 연결을 언제든지 편집할 수 있습니다.
  
**예제 디바이스 구성 파일**

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.3.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

## <a name="connectivity-details"></a><a name="default"></a>연결 세부 정보

온-프레미스 SDWAN/VPN 디바이스 또는 SD-WAN 구성은 Azure IPsec/IKE 정책에서 지정한 다음과 같은 알고리즘 및 매개 변수와 일치하거나 해당 항목을 포함해야 합니다.

* IKE 암호화 알고리즘
* IKE 무결성 알고리즘
* DH 그룹
* IPsec 암호화 알고리즘
* IPsec 무결성 알고리즘
* PFS 그룹

### <a name="default-policies-for-ipsec-connectivity"></a><a name="default"></a>IPsec 연결에 대한 기본 정책

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom-policies-for-ipsec-connectivity"></a><a name="custom"></a>IPsec 연결에 대 한 사용자 지정 정책

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>다음 단계

Virtual WAN에 대한 자세한 내용은 [Azure Virtual WAN 정보](virtual-wan-about.md) 및 [Azure Virtual WAN FAQ](virtual-wan-faq.md)를 참조하세요.

추가 정보는 <azurevirtualwan@microsoft.com>으로 이메일을 보내세요. 제목 줄에서 “[]”에 회사 이름을 적어주세요.
