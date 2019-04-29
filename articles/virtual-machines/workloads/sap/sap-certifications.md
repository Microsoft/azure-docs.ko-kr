---
title: SAP용 Microsoft Azure 인증 | Microsoft Docs
description: Azure 플랫폼의 SAP에 대한 현재 구성 및 인증의 업데이트된 목록입니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: de282041066e18a52d596f051def598eef3d458d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648927"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Microsoft Azure에서 실행되는 SAP 인증 및 구성

SAP와 Microsoft는 고객에게 상호 혜택을 주는 강력한 파트너 관계 속에서 장기간 함께 협력해 왔습니다. Microsoft는 Microsoft Azure가 SAP 작업을 실행할 최상의 플랫폼이 되도록 하기 위해 지속적으로 플랫폼을 업데이트하고 SAP에 새로운 인증 세부 정보를 제출하고 있습니다. 다음 표에서는 지원되는 Azure 구성 및 증가하는 SAP 인증 목록을 간략히 설명합니다. 

## <a name="sap-hana-certifications"></a>SAP HANA 인증
참조:

- [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure): 네이티브 Azure VM 및 HANA 대규모 인스턴스에 대한 SAP HANA 지원

| SAP 제품 | 지원되는 OS | Azure 제품 |
| --- | --- | --- |
| SAP HANA Developer Edition(SQLODBC, ODBO(Windows 전용), ODBC, JDBC 드라이버로 구성된 HANA Client 소프트웨어, HANA Studio, HANA Database 포함) | Red Hat Enterprise Linux, SUSE Linux Enterprise | D 시리즈 VM 제품군 |
| HANA의 Business One | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5의 제어되는 가용성 M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, <br /> Azure의 SAP HANA (대규모 인스턴스) [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite on HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, Azure의 SAP HANA(대규모 인스턴스) <br /> [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise for BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, <br /> Azure의 SAP HANA (대규모 인스턴스) [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, <br /> Azure(큰 인스턴스)에서 SAP HANA 사용 <br /> [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

SAP는 '클러스터링'이라는 용어를 고가용성 '클러스터링'이라는 의미가 아닌 [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)의 '스케일 아웃'과 동일한 의미로 사용합니다.

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver 인증
Microsoft Azure는 다음과 같은 SAP 제품에서 인증되었고 Microsoft와 SAP의 전폭적인 지원을 받고 있습니다.
참조:

- [1928533-Azure에서 SAP 응용 프로그램: 지원 되는 제품 및 Azure VM 유형](https://launchpad.support.sap.com/#/notes/1928533) 모든 SAP NetWeaver 기반 응용 프로그램에 대 한 SAP TREX, SAP LiveCache 및 SAP Content Server를 포함 합니다. 모든 데이터베이스(SAP HANA 제외)


| SAP 제품 | 게스트 OS | RDBMS | Virtual Machine 유형 |
| --- | --- | --- | --- |
| SAP Business Suite 소프트웨어 | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle(Windows 및 Oracle Linux만 해당), DB2, SAP ASE |A5 ~ A11, D11 ~ D14, DS11 ~ DS14, DS11_v2 ~ DS15_v2, GS1 ~ GS5, D2s_v3 ~ D64s_v3, E2s_v3 ~ E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle(Windows 및 Oracle Linux만 해당), DB2, SAP ASE |A5 ~ A11, D11 ~ D14, DS11 ~ DS14, DS11_v2 ~ DS15_v2, GS1 ~ GS5, D2s_v3 ~ D64s_v3, E2s_v3 ~ E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts |
| SAP BusinessObjects BI | Windows |N/A |A5 ~ A11, D11 ~ D14, DS11 ~ DS14, DS11_v2 ~ DS15_v2, GS1 ~ GS5, D2s_v3 ~ D64s_v3, E2s_v3 ~ E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle(Windows 및 Oracle Linux만 해당), DB2, SAP ASE |A5 ~ A11, D11 ~ D14, DS11 ~ DS14, DS11_v2 ~ DS15_v2, GS1 ~ GS5, D2s_v3 ~ D64s_v3, E2s_v3 ~ E64s_v3, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts |

## <a name="other-sap-workload-supported-on-azure"></a>Azure에서 지원되는 다른 SAP 워크로드

| SAP 제품 | 게스트 OS | RDBMS | Virtual Machine 유형 |
| --- | --- | --- | --- |
| SQL Server의 SAP Business One | Windows  | SQL Server | 모든 NetWeaver 인증 VM 유형<br /> [SAP 참고 #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows 및 Linux | | 모든 NetWeaver 인증 VM 유형<br /> SAP Note #2451795 |
| SAP 비즈니스 개체 BI 플랫폼 | Windows 및 Linux | | SAP Note #2145537 |
| SAP Data Services 4.2 | | | SAP Note #2288344 |
| SAP Hybris Commerce Platform 5.x 및 6.x | Windows | SQL Server, Oracle | 모든 NetWeaver 인증 VM 유형<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
