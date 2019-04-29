---
title: Azure 인프라 연습 예제 | Microsoft Docs
description: Azure에서 인프라 예제를 배포하기 위한 핵심 디자인 및 구현 지침에 대해 알아봅니다.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ddbaed6704fd32f7fd4fe5a790424cbf829d2f1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540405"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Windows VM에 대한 Azure 인프라 연습 예제
이 문서에서는 예제 애플리케이션 인프라를 구축하는 과정을 안내합니다. 명명 규칙, 가용성 집합, 가상 네트워크 및 부하 분산 장치에 대한 모든 지침 및 결정 사항을 함께 제공하는 간단한 온라인 스토어용 인프라의 설계와 VM(가상 머신)의 실제 배포를 자세히 다룹니다.

## <a name="example-workload"></a>워크로드 예제
Adventure Works Cycles는 Azure에서 다음으로 구성된 온라인 스토어 애플리케이션을 구축하려고 합니다.

* 웹 계층에 있으며 클라이언트 프런트 엔드를 실행하는 두 IIS 서버
* 애플리케이션 계층에 있으며 데이터 및 주문을 처리하는 두 IIS 서버
* 데이터베이스 계층에 제품 데이터 및 주문을 저장하기 위한 AlwaysOn 가용성 그룹(두 SQL Server 및 주 노드 감시) 이 있는 두 개의 Microsoft SQL Server 인스턴스
* 인증 계층에 있는 고객 계정 및 공급자에 대한 두 Active Directory 도메인 컨트롤러
* 모든 서버는 다음 두 서브넷에 있습니다.
  * 웹 서버에 대한 프런트 엔드 서브넷 
  * 애플리케이션 서버, SQL 클러스터 및 도메인 컨트롤러에 대한 백 엔드 서브넷

![애플리케이션 인프라의 여러 다른 계층 다이어그램](./media/infrastructure-example/example-tiers.png)

고객이 온라인 스토어를 검색할 때 들어오는 보안 웹 트래픽의 부하는 웹 서버 사이에서 분산되어야 합니다. 웹 서버에서 HTTP 요청 양식의 주문 처리 트래픽의 부하는 애플리케이션 서버 사이에서 부하 분산되어야 합니다. 또한 인프라는 고가용성을 위해 설계되어야 합니다.

결과로 나온 디자인 다음을 통합해야 합니다.

* Azure 구독 및 계정
* 단일 리소스 그룹
* Azure Managed Disks
* 두 서브넷을 사용하는 가상 네트워크
* 역할이 비슷한 VM에 대한 가용성 집합
* 가상 머신

위의 모든 사항은 명명 규칙을 따릅니다.

* Adventure Works Cycles는 **[IT 작업]-[위치]-[Azure 리소스]** 를 접두사로 사용합니다.
  * 이 예제에서 "**azos**"(Azure 온라인 저장소)는 IT 워크로드 이름이고 "**use**"(미국 동부 2)는 위치입니다.
* 가상 네트워크는 AZOS-USE-VN **[숫자]** 를 사용합니다.
* 가용성 집합은 azos-use-as-**[역할]** 을 사용합니다.
* 가상 머신 이름은 azos-use-vm-**[VM 이름]** 을 사용합니다.

## <a name="azure-subscriptions-and-accounts"></a>Azure 구독 및 계정
Adventure Works Cycles는 이 IT 작업에 대한 청구를 제공하기 위해 Adventure Works Enterprise Subscription이라는 엔터프라이즈 구독을 사용합니다.

## <a name="storage"></a>Storage
Adventure Works Cycles에서는 Azure Managed Disks를 사용해야 한다고 결정했습니다. VM을 만들 때 사용 가능한 두 저장소 계층이 모두 사용됩니다.

* **Standard Storage** - 웹 서버, 애플리케이션 서버 및 도메인 컨트롤러와 해당 데이터 디스크
* **Premium Storage** - SQL Server VM과 해당 데이터 디스크

## <a name="virtual-network-and-subnets"></a>가상 네트워크 및 서브넷
가상 네트워크는 Adventure Work Cycles 온-프레미스 네트워크에 지속적인 연결이 필요하지 않기 때문에 클라우드 전용 가상 네트워크로 결정했습니다.

Azure 포털을 사용하여 다음 설정을 포함한 클라우드 전용 가상 네트워크를 만들 수 있습니다.

* 이름: AZOS-USE-VN01
* 위치: 미국 동부 2
* 가상 네트워크 주소 공간: 10.0.0.0/8
* 첫 번째 서브넷:
  * 이름: FrontEnd
  * 주소 공간 10.0.1.0/24
* 두 번째 서브넷:
  * 이름: BackEnd
  * 주소 공간 10.0.2.0/24

## <a name="availability-sets"></a>가용성 집합
온라인 스토어에서 모든 네 개 계층의 고가용성을 유지하기 위해 Adventure Works Cycles는 다음과 같은 네 개의 가용성 집합으로 결정했습니다.

* **azos-use-as-web** 
* 애플리케이션 서버용 **azos-use-as-app**
* **azos-use-as-sql** 
* **azos-use-as-dc** 

## <a name="virtual-machines"></a>가상 머신
Adventure Works Cycles는 Azure VM에 대해 다음 이름을 결정했습니다.

* **azos-use-vm-web01** 
* **azos-use-vm-web02** 
* 첫 번째 애플리케이션 서버용 **azos-use-vm-app01**
* 두 번째 애플리케이션 서버용 **azos-use-vm-app02**
* **azfae-use-vm-sql01** 
* **azfae-use-vm-sql02** 
* **azos-use-vm-dc01** 
* **azos-use-vm-dc02** 

다음은 결과 구성입니다.

![Azure에 배포되는 최종 애플리케이션 인프라](./media/infrastructure-example/example-config.png)

이 구성은 다음을 통합합니다.

* 두 서브넷을 사용하는 클라우드 전용 가상 네트워크(프런트 엔드 및 백 엔드)
* Standard 디스크와 Premium 디스크가 둘 다 있는 Azure Managed Disks
* 네 개의 가용성 집합, 온라인 스토어의 각 계층마다 한 개
* 네 계층에 대한 가상 머신
* 인터넷에서 웹 서버 간 HTTPS 기반 웹 트래픽에 대한 외부 부하 분산 집합
* 웹 서버에서 애플리케이션 서버 간 암호화되지 않은 웹 트래픽에 대한 내부 부하 분산 집합
* 단일 리소스 그룹
