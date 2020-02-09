---
title: Azure VMware 솔루션 (AVS)-AVS 서비스에 대 한 보안
description: AVS 서비스의 보안을 위한 공유 책임 모델에 대해 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d2d45f827d4165175a4a5429f0b9393a55e2ff1e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024996"
---
# <a name="avs-security-overview"></a>AVS 보안 개요

이 문서에서는 AVS 서비스, 인프라 및 데이터 센터에서 Azure VMware 솔루션에 보안을 구현 하는 방법에 대 한 개요를 제공 합니다. 데이터 보호 및 보안, 네트워크 보안 및 취약성 및 패치 관리 방법에 대해 알아봅니다.

## <a name="shared-responsibility"></a>공동 책임

AVS의 Azure VMware 솔루션은 보안을 위해 공유 책임 모델을 사용 합니다. 클라우드에서 신뢰할 수 있는 보안은 고객 및 Microsoft가 서비스 공급자로 서 공유 하는 책임을 통해 달성 됩니다. 이 책임 매트릭스는 더 높은 보안을 제공 하 고 단일 실패 지점이 제거 됩니다.

## <a name="azure-infrastructure"></a>Azure 인프라

Azure 인프라 보안 고려 사항에는 데이터 센터 및 장비 위치가 포함 됩니다.

### <a name="datacenter-security"></a>데이터 센터 보안

Microsoft에는 Azure를 지 원하는 물리적 시설을 설계, 빌드 및 운영 하기 위한 전체 부서가 포함 되어 있습니다. 이 팀은 최첨단의 물리적 보안을 유지 관리하는 데 투입되었습니다. 물리적 보안에 대 한 자세한 내용은 [Azure 시설, 프레미스 및 물리적 보안](../security/azure-physical-security.md)을 참조 하세요.

### <a name="equipment-location"></a>장비 위치

AVS 사설 클라우드를 실행 하는 운영 체제 미 설치 하드웨어 장비는 Azure 데이터 센터 위치에서 호스팅됩니다. 해당 장비가 있는 케이지도는 액세스 권한을 얻기 위해 생체 인식 기반 2 단계 인증이 필요 합니다.

## <a name="dedicated-hardware"></a>전용 하드웨어

모든 AVS 고객은 AVS 서비스의 일부로, 다른 테넌트 하드웨어와 물리적으로 분리 된 로컬 연결 디스크를 사용 하 여 전용 운영 체제 미 설치 호스트를 가져옵니다. VSAN을 사용 하는 ESXi 하이퍼바이저는 모든 노드에서 실행 됩니다. 노드는 고객 전용 VMware vCenter 및 NSX를 통해 관리 됩니다. 테넌트 간에 하드웨어를 공유 하지 않는 경우 추가 격리 및 보안 보호 계층을 제공 합니다.

## <a name="data-security"></a>데이터 보안

고객은 자신의 데이터에 대 한 제어 및 소유권을 유지 합니다. 고객 데이터의 데이터 stewardship 고객의 책임입니다.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>미사용 데이터 및 내부 네트워크 내에서 데이터에 대 한 데이터 보호

AVS 사설 클라우드 환경에서 미사용 데이터의 경우 vSAN 암호화를 사용할 수 있습니다. vSAN 암호화는 자체 가상 네트워크 또는 온-프레미스에서 VMware 인증 된 KMS (키 관리 서버)와 함께 작동 합니다. 데이터 암호화 키를 직접 제어할 수 있습니다. AVS 사설 클라우드 내에서 이동 하는 데이터의 경우 vSphere는 모든 vmkernel 트래픽 (vMotion 트래픽 포함)에 대해 네트워크를 통해 데이터를 암호화 하는 것이 지원 됩니다.

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>공용 네트워크를 통해 이동 하는 데 필요한 데이터에 대 한 데이터 보호

공용 네트워크를 통해 이동 하는 데이터를 보호 하기 위해 AVS 사설 클라우드에 대 한 IPsec 및 SSL VPN 터널을 만들 수 있습니다. 128 바이트 및 256 바이트 AES를 비롯 한 일반적인 암호화 방법이 지원 됩니다. 전송 중인 데이터 (인증, 관리 액세스 및 고객 데이터 포함)는 표준 암호화 메커니즘 (SSH, TLS 1.2 및 보안 RDP)으로 암호화 됩니다. 중요 한 정보를 전송 하는 통신은 표준 암호화 메커니즘을 사용 합니다.

### <a name="secure-disposal"></a>보안 삭제

AVS 서비스가 만료 되거나 종료 되 면 데이터를 제거 하거나 삭제 해야 합니다. AVS는 고객 계약에 제공 된 모든 고객 데이터를 삭제 하거나 반환 하는 데 사용할 수 있습니다. 단, 해당 법률에 따라 개인 데이터의 일부 또는 전체를 유지 하기 위해 적용 되는 익스텐트가 필요 하지 않습니다. 모든 개인 데이터를 보존 하는 데 필요한 경우, AVS는 데이터를 보관 하 고 적절 한 측정값을 구현 하 여 고객 데이터의 추가 처리를 방지 합니다.

### <a name="data-location"></a>데이터 위치

AVS 사설 클라우드를 설정 하는 경우 배포 될 Azure 지역을 선택 합니다. 데이터 마이그레이션 또는 오프 사이트 데이터 백업을 수행 하지 않는 한 VMware 가상 컴퓨터 데이터는 해당 물리적 데이터 센터에서 이동 되지 않습니다. 필요에 따라 워크 로드를 호스트 하 고 여러 Azure 지역 내에 데이터를 저장할 수도 있습니다.

AVS 사설 클라우드 하이퍼 수렴 형 노드에 상주 하는 고객 데이터는 테넌트 관리자의 명시적인 작업 없이 위치를 트래버스 하지 않습니다. 항상 사용 가능한 방식으로 워크 로드를 구현 하는 것은 사용자의 책임입니다.

### <a name="data-backups"></a>데이터 백업

AVS는 고객 데이터를 백업 하거나 보관 하지 않습니다. AVS는 vCenter 및 NSX 데이터를 정기적으로 백업 하 여 관리 서버의 고가용성을 제공 합니다. 백업 하기 전에 모든 데이터는 VMware Api를 사용 하 여 vCenter 원본에서 암호화 됩니다. 암호화 된 데이터는 Azure blob에 전송 되 고 저장 됩니다. 백업용 암호화 키는 Azure의 AVS 가상 네트워크에서 실행 되는 매우 안전한 AVS 관리 자격 증명 모음에 저장 됩니다.

## <a name="network-security"></a>네트워크 보안

AVS 솔루션은 네트워크 보안 계층을 기반으로 합니다.

### <a name="azure-edge-security"></a>Azure edge 보안

AVS 서비스는 Azure에서 제공 하는 기본 네트워크 보안을 기반으로 빌드됩니다. Azure는 비정상 수신 또는 송신 트래픽 패턴 및 DDoS (배포 된 서비스 거부) 공격과 관련 된 네트워크 기반 공격에 대 한 심층 대응 및 검색을 위한 심층 방어 기술을 적용 합니다. 이 보안 제어는 avs에서 개발한 AVS 사설 클라우드 환경 및 제어 평면 소프트웨어에 적용 됩니다.

### <a name="segmentation"></a>분할

AVS 서비스는 AVS 사설 클라우드 환경에서 고유한 개인 네트워크에 대 한 액세스를 제한 하는 계층 2 네트워크를 논리적으로 분리 합니다. 방화벽을 사용 하 여 AVS 사설 클라우드 네트워크를 추가로 보호할 수 있습니다. Avs 포털을 사용 하면 모든 네트워크 트래픽에 대 한 기본 및 NS 네트워크 트래픽 제어 규칙을 정의할 수 있습니다. 여기에는 avs 사설 클라우드 트래픽, AVS 간 사설 클라우드 트래픽, 인터넷에 대 한 일반 트래픽, IPsec VPN을 통해 온-프레미스에 대 한 네트워크 트래픽 등이 포함 됩니다. Express 경로 연결입니다.

## <a name="vulnerability-and-patch-management"></a>취약성 및 패치 관리

AVS는 관리 VMware 소프트웨어 (ESXi, vCenter 및 NSX)의 주기적인 보안 패치를 담당 합니다.

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

고객은 기본으로 multi-factor authentication 또는 SSO를 사용 하 여 azure 계정 (Azure AD)에 인증할 수 있습니다. Azure Portal에서 자격 증명을 사용자 지정할 필요 없이 AVS 포털을 시작할 수 있습니다.

AVS는 AVS 사설 클라우드 vCenter에 대 한 id 원본의 선택적 구성을 지원 합니다. [온-프레미스 id 원본](set-vcenter-identity.md), AVS 사설 클라우드의 새 id 원본 또는 [Azure AD](azure-ad.md)를 사용할 수 있습니다.

기본적으로 고객은 AVS 사설 클라우드 내에서 vCenter의 일상적인 작업에 필요한 권한을 부여 합니다. 이 권한 수준은 vCenter에 대 한 관리 액세스를 포함 하지 않습니다. 관리 액세스가 일시적으로 필요한 경우 관리 작업을 완료 하는 동안 제한 된 기간 동안 [권한을 에스컬레이션](escalate-private-cloud-privileges.md) 할 수 있습니다.
