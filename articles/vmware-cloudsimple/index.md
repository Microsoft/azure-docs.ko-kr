---
title: Azure VMware Solution by CloudSimple
description: Azure VMware Solutions by CloudSimple에 대한 개요, 빠른 시작, 개념, 자습서 및 방법 가이드 등을 알아보세요.
author: sharaths-cs
ms.author: b-mashar
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.custom: seo-azure-migrate
keywords: vm 지원, azure vmware solution by cloudsimple, cloudsimple azure, vm 도구, vmware 설명서
ms.openlocfilehash: fffe0255bb905c0314e2ed59f5beab726e681e00
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230639"
---
# <a name="azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple

Azure VMware Solution by CloudSimple 관련 지원을 제공하는 원스톱 포털에 오신 것을 환영합니다.
설명서 사이트에서 다음 항목에 대해 알아볼 수 있습니다.

## <a name="overview"></a>개요

Azure VMware Solution by CloudSimple에 대한 자세한 정보

* [Azure VMware Solution by CloudSimple이란?](cloudsimple-vmware-solutions-overview.md)에서 기능, 이점, 사용 시나리오에 대해 알아보기
* [관리 주요 개념](key-concepts.md) 살펴보기

## <a name="quickstart"></a>빠른 시작

솔루션을 시작하는 방법 알아보기

* [서비스를 초기화하고 용량을 구매](quickstart-create-cloudsimple-service.md)하는 방법 이해
* [프라이빗 클라우드 환경 구성](quickstart-create-private-cloud.md)에서 새 VMware 환경을 만드는 방법 알아보기
* [Azure에서 VMware VM 사용](quickstart-create-vmware-virtual-machine.md) 문서를 살펴보고 VMware 및 Azure에 대한 관리를 통합하는 방법 알아보기

## <a name="concepts"></a>개념

다음 개념에 대해 알아보기

* [Cloudsimple 서비스](cloudsimple-service.md)("Azure VMware Solution by CloudSimple - 서비스"라고도 함). 이 리소스는 지역마다 하나씩 만들어야 합니다.
* [CloudSimple 노드](cloudsimple-node.md) 리소스를 여러 개 만들어 사용자 환경의 용량을 구매합니다. 이러한 리소스를 "Azure VMware Solution by CloudSimple - 노드"라고도 합니다.
* [프라이빗 클라우드](cloudsimple-private-cloud.md)를 사용하여 VMware 환경을 초기화하고 구성합니다.
* [CloudSimple Virtual Machines](cloudsimple-virtual-machines.md)("Azure VMware Solution by CloudSimple - 가상 머신"이라고도 함).
* [VLAN/서브넷](cloudsimple-vlans-subnets.md)을 사용하여 언더레이 네트워크를 설계합니다.
* [방화벽 테이블](cloudsimple-firewall-tables.md) 리소스를 사용하여 언더레이 네트워크를 분할하고 보호합니다.
* [VPN Gateway](cloudsimple-vpn-gateways.md)를 사용하여 WAN을 통해 VMware 환경에 안전하게 액세스합니다.
* [공용 IP](cloudsimple-public-ip-address.md)를 사용하여 워크로드에 대한 공용 액세스를 사용하도록 설정합니다.
* [Azure 네트워크 연결](cloudsimple-azure-network-connection.md)을 사용하여 Azure Virtual Network 및 온-프레미스 네트워크에 대한 연결을 설정합니다.
* [계정 관리](cloudsimple-account.md)를 사용하여 경고 이메일 대상을 구성합니다.
* [작업 관리](cloudsimple-activity.md) 화면에서 사용자 및 시스템 작업 로그를 봅니다.
* 다양한 [VMware 구성 요소](vmware-components.md)를 이해합니다.

## <a name="tutorials"></a>자습서

다음과 같은 일반적인 작업을 수행하는 방법을 알아봅니다.

* VMware 환경을 배포하려는 지역마다 하나씩 [CloudSimple 서비스를 생성](create-cloudsimple-service.md)합니다.
* [CloudSimple 포털](access-cloudsimple-portal.md)에서 핵심 서비스 기능을 관리합니다.
* [CloudSimple 노드를 구매](create-nodes.md)하여 인프라 대금 청구를 최적화하고 용량을 사용하도록 설정합니다.
* 프라이빗 클라우드를 사용하여 VMware 환경 구성을 관리합니다. 프라이빗 클라우드를 [생성](create-private-cloud.md), [관리](manage-private-cloud.md), [확장](expand-private-cloud.md) 또는 [축소](shrink-private-cloud.md)할 수 있습니다.
* [Azure 구독을 매핑](azure-subscription-mapping.md)하여 통합 관리를 사용하도록 설정합니다.
* [작업 페이지](monitor-activity.md)를 사용하여 사용자 및 시스템 작업을 모니터링합니다.
* [서브넷을 만들고 관리](create-vlan-subnet.md)하여 환경의 네트워킹을 구성합니다.
* [방화벽 테이블 및 규칙](firewall.md)을 사용하여 환경을 분할하고 보호합니다.
* [공용 IP를 할당](public-ips.md)하여 워크로드에 인바운드 인터넷 액세스를 사용하도록 설정합니다.
* [VPN을 설정](vpn-gateway.md)하여 내부 네트워크 또는 클라이언트 워크스테이션에서 들어오는 연결을 사용하도록 설정합니다.
* [온-프레미스 환경](on-premises-connection.md)에서 [Azure 가상 네트워크](virtual-network-connection.md)로의 통신을 사용하도록 설정합니다.
* 경고 대상을 구성하고 [계정 요약](account.md)에서 구매한 총 용량을 확인합니다.
* CloudSimple 포털에 액세스한 [사용자](users.md)를 확인합니다.
* Azure Portal에서 VMware 가상 머신 관리:
    * Azure Portal에서 [가상 머신을 생성](azure-create-vm.md)합니다.
    * 생성된 [가상 머신을 관리](azure-manage-vm.md)합니다.

## <a name="how-to-guides"></a>방법 가이드

이러한 가이드에서는 다음과 같은 목표에 대한 솔루션을 설명합니다.

* [환경 보호](private-cloud-secure.md)
* 타사 도구를 설치하고, [권한 상승](escalate-privileges.md)을 사용하여 vSphere에서 추가 사용자 및 외부 인증 소스를 사용하도록 설정합니다.
* [온-프레미스 DNS를 구성](on-premises-dns-setup.md)하여 다양한 VMware 서비스에 대한 액세스를 구성합니다.
* [워크로드 DNS 및 DHCP를 구성](dns-dhcp-setup.md)하여 워크로드에 이름 및 주소 할당을 사용하도록 설정합니다.
* 서비스 [업데이트 및 업그레이드](vmware-components.md#updates-and-upgrades)를 통해 서비스에서 플랫폼의 보안 및 기능을 보장하는 방법을 이해합니다.
* [Veeam과 같은 타사 백업 소프트웨어](backup-workloads-veeam.md)를 통해 샘플 백업 아키텍처를 만들어 백업의 TCO를 절감합니다.
* [타사 KMS 암호화 소프트웨어](vsan-encryption.md)를 통해 저장 데이터 암호화를 사용하도록 설정하여 보안 환경을 만듭니다.
* [Azure AD ID 소스](azure-ad.md)를 구성하여 Azure Active Directory(Azure AD) 관리를 VMware로 확장합니다.
