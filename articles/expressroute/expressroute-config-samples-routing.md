---
title: 'Azure ExpressRoute: 라우터 구성 샘플'
description: Azure Express 경로를 사용 하는 예제로 Cisco IOS-XE 및 곱 향나무 MX 시리즈 라우터 용 인터페이스 및 라우팅 구성 샘플을 사용 합니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: fdf50dc6b4c0976a713a1d436ac9af7c1bd9701b
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88190791"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>라우팅 설정 및 관리를 위한 라우터 구성 샘플
이 페이지에서는 Azure ExpressRoute를 사용하여 작업할 때 Cisco IOS-XE 및 Juniper MX 시리즈 라우터에 대한 인터페이스 및 라우팅 구성 샘플을 제공합니다.

> [!IMPORTANT]
> 이 페이지의 샘플은 순전히 지침용입니다. 공급업체의 영업/기술 팀 및 네트워킹 팀과 협력하여 요구 사항에 맞는 적절한 구성을 찾아야 합니다. Microsoft는 이 페이지에 나열된 구성과 관련된 문제를 지원하지 않습니다. 지원 문제는 디바이스 공급업체에 문의하세요.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>라우터 인터페이스의 MTU 및 TCP MSS 설정
ExpressRoute 인터페이스에 대한 MTU(최대 전송 단위)는 라우터의 이더넷 인터페이스에 대한 일반적인 기본 MTU인 1500입니다. 라우터에 기본적으로 다른 MTU가 있지 않는 한, 라우터 인터페이스에서 값을 지정할 필요가 없습니다.

Azure VPN Gateway와는 달리, ExpressRoute 회로용 TCP MSS(최대 세그먼트 크기)를 지정할 필요가 없습니다.

이 문서의 라우터 구성 샘플은 모든 피어링에 적용됩니다. 라우팅에 대한 자세한 내용은 [ExpressRoute 피어링](expressroute-circuit-peerings.md) 및 [ExpressRoute 라우팅 요구 사항](expressroute-routing.md)을 검토하세요.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE 기반 라우터
이 섹션의 샘플은 IOS-XE OS 제품군을 실행하는 모든 라우터에 적용됩니다.

### <a name="configure-interfaces-and-subinterfaces"></a>인터페이스 및 하위 인터페이스 구성
Microsoft에 연결하는 모든 라우터에서 피어링당 하나의 하위 인터페이스가 필요합니다. 하위 인터페이스는 VLAN ID 또는 누적된 한 쌍의 VLAN ID 및 IP 주소로 식별될 수 있습니다.

**Dot1Q 인터페이스 정의**

이 샘플에서는 단일 VLAN ID가 있는 하위 인터페이스에 대한 하위 인터페이스 정의를 제공합니다. VLAN ID는 피어링별로 고유합니다. IPv4 주소의 마지막 옥텟은 항상 홀수입니다.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <VLAN_ID>
 ip address <IPv4_Address><Subnet_Mask>
```

**QinQ 인터페이스 정의**

이 샘플에서는 두 개의 VLAN ID가 있는 하위 인터페이스에 대한 하위 인터페이스 정의를 제공합니다. 외부 VLAN ID(s-tag)는 사용되는 경우 모든 피어링에서 동일하게 유지됩니다. 안쪽의 VLAN ID(c-tag)는 피어링별로 고유합니다. IPv4 주소의 마지막 옥텟은 항상 홀수입니다.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>
```

### <a name="set-up-ebgp-sessions"></a>eBGP 세션 설정
모든 피어링에 대해 Microsoft와 BGP 세션을 설정해야 합니다. 다음 샘플을 사용하여 BGP 세션을 설정합니다. 하위 인터페이스에 사용한 IPv4 주소가 a.b.c.d인 경우 BGP 인접 라우터(Microsoft)의 IP 주소는 a.b.c.d+1입니다. BGP 인접 라우터에 대한 IPv4 주소의 마지막 옥텟은 항상 짝수입니다.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
 neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>BGP 세션을 통해 알릴 접두사 설정
다음 샘플을 사용하여 선택 접두사를 Microsoft에 알리도록 라우터를 구성합니다.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="route-maps"></a>경로 맵
경로 맵과 접두사 목록을 사용하여 네트워크에 전파되는 접두사를 필터링합니다. 다음 샘플을 참조하여 적절한 접두사 목록이 설정되었는지 확인합니다.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
  neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
 exit-address-family
!
route-map <MS_Prefixes_Inbound> permit 10
 match ip address prefix-list <MS_Prefixes>
!
```

### <a name="configure-bfd"></a>BFD 구성

두 위치(하나는 인터페이스 수준에서, 다른 하나는 BGP 수준)에서 BFD를 구성합니다. 여기서의 예제는 QinQ 인터페이스에 대한 것입니다. 

```console
interface GigabitEthernet<Interface_Number>.<Number>
 bfd interval 300 min_rx 300 multiplier 3
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>

router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  neighbor <IP#2_used_by_Azure> activate
  neighbor <IP#2_used_by_Azure> fall-over bfd
 exit-address-family
!
```


## <a name="juniper-mx-series-routers"></a>Juniper MX 시리즈 라우터
이 섹션의 샘플은 모든 Juniper MX 시리즈 라우터에 적용됩니다.

### <a name="configure-interfaces-and-subinterfaces"></a>인터페이스 및 하위 인터페이스 구성

**Dot1Q 인터페이스 정의**

이 샘플에서는 단일 VLAN ID가 있는 하위 인터페이스에 대한 하위 인터페이스 정의를 제공합니다. VLAN ID는 피어링별로 고유합니다. IPv4 주소의 마지막 옥텟은 항상 홀수입니다.

```console
    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }
```


**QinQ 인터페이스 정의**

이 샘플에서는 두 개의 VLAN ID가 있는 하위 인터페이스에 대한 하위 인터페이스 정의를 제공합니다. 외부 VLAN ID(s-tag)는 사용되는 경우 모든 피어링에서 동일하게 유지됩니다. 안쪽의 VLAN ID(c-tag)는 피어링별로 고유합니다. IPv4 주소의 마지막 옥텟은 항상 홀수입니다.

```console
    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           
```

### <a name="set-up-ebgp-sessions"></a>eBGP 세션 설정
모든 피어링에 대해 Microsoft와 BGP 세션을 설정해야 합니다. 다음 샘플을 사용하여 BGP 세션을 설정합니다. 하위 인터페이스에 사용한 IPv4 주소가 a.b.c.d인 경우 BGP 인접 라우터(Microsoft)의 IP 주소는 a.b.c.d+1입니다. BGP 인접 라우터에 대한 IPv4 주소의 마지막 옥텟은 항상 짝수입니다.

```console
    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>BGP 세션을 통해 알릴 접두사 설정
다음 샘플을 사용하여 선택 접두사를 Microsoft에 알리도록 라우터를 구성합니다.

```console
    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
                route-filter; 
                <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="route-policies"></a>경로 정책
경로 맵과 접두사 목록을 사용하여 네트워크에 전파되는 접두사를 필터링할 수 있습니다. 다음 샘플을 참조하여 적절한 접두사 목록이 설정되었는지 확인합니다.

```console
    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
                    prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>;
                import <MS_Prefixes_Inbound>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="configure-bfd"></a>BFD 구성
프로토콜 BGP 섹션에서만 BFD를 구성합니다.

```console
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }
```

### <a name="configure-macsec"></a>MACSec 구성
MACSec 구성의 경우 CAK(Connectivity Association Key) 및 CKN(Connectivity Association Key 이름)은 PowerShell 명령을 통해 구성된 값과 일치해야 합니다.

```console
    security {
        macsec {
            connectivity-association <Connectivity_Association_Name> {
                cipher-suite gcm-aes-xpn-128;
                security-mode static-cak;
                pre-shared-key {
                    ckn <Connectivity_Association_Key_Name>;
                    cak <Connectivity_Association_Key>; ## SECRET-DATA
                }
            }
            interfaces {
                <Interface_Number> {
                    connectivity-association <Connectivity_Association_Name>;
                }
            }
        }
    }
```

## <a name="next-steps"></a>다음 단계
자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md) 를 참조하세요.



