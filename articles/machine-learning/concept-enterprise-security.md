---
title: 엔터프라이즈 보안 및 거버넌스
titleSuffix: Azure Machine Learning
description: 'Azure Machine Learning: 인증, 권한 부여, 네트워크 보안, 데이터 암호화 및 모니터링을 안전하게 사용합니다.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/20/2020
ms.openlocfilehash: a079504872eaf3840416a99e784c4d33a6828b0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94992032"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 엔터프라이즈 보안 및 거 버 넌 스

이 문서에서는 Azure Machine Learning에 대해 제공 되는 보안 및 거 버 넌 스 기능에 대해 알아봅니다. 이러한 기능은 회사 정책을 준수 하는 보안 구성을 만들려는 관리자, DevOps 및 MLOps에 유용 합니다. Azure Machine Learning 및 Azure 플랫폼을 사용 하 여 다음을 수행할 수 있습니다.

* 사용자 계정 또는 그룹에의 한 리소스 및 작업에 대 한 액세스 제한
* 들어오고 나가는 네트워크 통신 제한
* 전송 중 및 저장소 데이터 암호화
* 취약성 검색
* 구성 정책 적용 및 감사

## <a name="restrict-access-to-resources-and-operations"></a>리소스 및 작업에 대 한 액세스 제한

[Azure Active Directory (AZURE AD)](../active-directory/fundamentals/active-directory-whatis.md) 는 Azure Machine Learning에 대 한 id 서비스 공급자입니다. Azure 리소스를 _인증_ 하는 데 사용 되는 보안 개체 (사용자, 그룹, 서비스 주체 및 관리 id)를 만들고 관리할 수 있습니다. Azure AD가이를 사용 하도록 구성 된 경우 multi-factor authentication이 지원 됩니다.

Azure AD에서 multi-factor authentication을 사용 하는 Azure Machine Learning에 대 한 인증 프로세스는 다음과 같습니다.

1. 클라이언트가 Azure AD에 로그인하여 Azure Resource Manager 토큰을 얻습니다.
1. 클라이언트가 Azure Resource Manager와 모든 Azure Machine Learning에 토큰을 제공합니다.
1. Azure Machine Learning은 사용자 계산 대상에 Machine Learning 서비스 토큰을 제공 합니다 (예: Azure Machine Learning 계산 클러스터). 이 토큰은 실행이 완료된 후 사용자 컴퓨팅 대상이 Machine Learning Service로 콜백되는 데 사용됩니다. 범위는 작업 영역으로 제한 됩니다.

[![Azure Machine Learning 인증](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

각 작업 영역에는 작업 영역과 동일한 이름을 가진 시스템 할당 [관리 id](../active-directory/managed-identities-azure-resources/overview.md) 가 연결 되어 있습니다. 이 관리 id는 작업 영역에서 사용 하는 리소스에 안전 하 게 액세스 하는 데 사용 됩니다. 연결 된 리소스에 대 한 다음과 같은 Azure RBAC 권한이 있습니다.

| 리소스 | 사용 권한 |
| ----- | ----- |
| 작업 영역 | 참가자 |
| 스토리지 계정 | Storage Blob 데이터 기여자 |
| 주요 자격 증명 모음 | 모든 키, 비밀, 인증서에 대한 액세스 |
| Azure Container Registry | 참가자 |
| 작업 영역을 포함하는 리소스 그룹 | 참가자 |
| 키 자격 증명 모음이 포함된 리소스 그룹(작업 영역이 포함된 것과 다른 경우) | 참가자 |

이전 표에 언급된 리소스의 관리 ID에 대한 액세스를 관리자가 취소하지 않는 것이 좋습니다. [키 다시 동기화 작업](how-to-change-storage-access-key.md)을 사용 하 여 액세스를 복원할 수 있습니다.

또한 Azure Machine Learning는 `aml-` `Microsoft-AzureML-Support-App-` 모든 작업 영역에 대 한 구독에서 참가자 수준 액세스 권한이 있는 추가 응용 프로그램 (또는로 시작 하는 이름)을 만듭니다. 예를 들어, 미국 동부에 작업 영역이 하나 있고 동일한 구독의 북유럽에 하나가 있으면 해당 애플리케이션은 두 개가 보입니다. 이러한 애플리케이션을 통해 Azure Machine Learning에서 컴퓨팅 리소스를 관리할 수 있습니다.

Azure Virtual Machines 및 Azure Machine Learning 계산 클러스터에서 사용할 관리 되는 id를 구성할 수도 있습니다. VM을 사용 하면 관리 되는 id를 사용 하 여 개별 사용자의 Azure AD 계정 대신 SDK에서 작업 영역에 액세스할 수 있습니다. 계산 클러스터를 사용 하면 관리 되는 id를 사용 하 여 학습 작업을 실행 하는 사용자가 액세스할 수 없는 보안 된 데이터 저장소와 같은 리소스에 액세스 합니다. 자세한 내용은 [Azure Machine Learning 작업 영역에 대 한 인증](how-to-setup-authentication.md)을 참조 하세요.

> [!TIP]
> Azure Machine Learning 내에서 Azure AD 및 Azure RBAC 사용에 대 한 몇 가지 예외가 있습니다.
> * 필요에 따라 Azure Machine Learning 계산 인스턴스 및 계산 클러스터와 같은 계산 리소스에 대 한 __SSH__ 액세스를 사용 하도록 설정할 수 있습니다. SSH 액세스는 Azure AD가 아닌 공개/개인 키 쌍을 기반으로 합니다. SSH 액세스는 Azure RBAC의 적용을 받지 않습니다.
> * __키__ 또는 __토큰__ 기반 인증을 사용 하 여 웹 서비스 (유추 끝점)로 배포 된 모델에 인증할 수 있습니다. 키는 정적 문자열이 고, 토큰은 Azure AD 보안 개체를 사용 하 여 검색 됩니다. 자세한 내용은 [웹 서비스로 배포 된 모델에 대 한 인증 구성](how-to-authenticate-web-service.md)을 참조 하세요.

자세한 내용은 다음 아티클을 참조하세요.
* [Azure Machine Learning 작업 영역에 대 한 인증](how-to-setup-authentication.md)
* [Azure Machine Learning에 대 한 액세스 관리](how-to-assign-roles.md)
* [저장소 서비스에 연결](how-to-access-data.md)
* [학습 시 비밀에 Azure Key Vault 사용](how-to-use-secrets-in-runs.md)
* [Azure Machine Learning에서 Azure AD 관리 id 사용](how-to-use-managed-identities.md)
* [웹 서비스에서 Azure AD 관리 id 사용](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>네트워크 보안 및 격리

Azure Machine Learning 리소스에 대 한 네트워크 액세스를 제한 하기 위해 [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md)를 사용할 수 있습니다. Vnet를 사용 하면 공용 인터넷에서 부분적으로 또는 완전히 격리 된 네트워크 환경을 만들 수 있습니다. 이렇게 하면 솔루션에 대 한 공격 노출 영역 뿐만 아니라 데이터 exfiltration의 기회가 줄어듭니다.

VPN (가상 사설망) 게이트웨이를 사용 하 여 개별 클라이언트 또는 자신의 네트워크를 VNet에 연결할 수 있습니다.

Azure Machine Learning 작업 영역에서는 [Azure 개인 링크](../private-link/private-link-overview.md) 를 사용 하 여 VNet 뒤에 개인 끝점을 만들 수 있습니다. 이는 VNet 내에서 작업 영역에 액세스 하는 데 사용할 수 있는 개인 IP 주소 집합을 제공 합니다. Azure Machine Learning에서 사용 하는 일부 서비스는 Azure 개인 링크를 사용할 수도 있지만, 일부는 네트워크 보안 그룹 또는 사용자 정의 라우팅에 의존 합니다.

자세한 내용은 다음 문서를 참조하세요.

* [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md)
* [보안 작업 영역](how-to-secure-workspace-vnet.md)
* [보안 교육 환경](how-to-secure-training-vnet.md)
* [보안 유추 환경](how-to-secure-inferencing-vnet.md)
* [보안 가상 네트워크에서 스튜디오 사용](how-to-enable-studio-virtual-network.md)
* [사용자 지정 DNS 사용](how-to-custom-dns.md)
* [방화벽 구성](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>데이터 암호화.

Azure Machine Learning는 Azure 플랫폼에서 다양 한 계산 리소스 및 데이터 저장소를 사용 합니다. 이러한 각각은 미사용 및 전송 중에 데이터 암호화를 지 원하는 방법에 대해 자세히 알아보려면 [Azure Machine Learning로 데이터 암호화](concept-data-encryption.md)를 참조 하세요.

모델을 웹 서비스로 배포 하는 경우 TLS (전송 계층 보안)를 사용 하도록 설정 하 여 전송 중인 데이터를 암호화할 수 있습니다. 자세한 내용은 [보안 웹 서비스 구성](how-to-secure-web-service.md)을 참조 하세요.

## <a name="vulnerability-scanning"></a>취약성 검색

[Azure Security Center](../security-center/security-center-introduction.md)는 하이브리드 클라우드 워크로드에서 통합 보안 관리 및 지능형 위협 방지 기능을 제공합니다. Azure machine learning의 경우 [Azure Container Registry](../container-registry/container-registry-intro.md) 리소스 및 Azure Kubernetes Service 리소스에 대 한 검사를 사용 하도록 설정 해야 합니다. 자세한 내용은 [Azure Container Registry 이미지 검색 Security Center](../security-center/defender-for-container-registries-introduction.md) 및 [Security Center와 Azure Kubernetes Services 통합](../security-center/defender-for-kubernetes-introduction.md)을 참조 하세요.

## <a name="audit-and-manage-compliance"></a>규정 준수 감사 및 관리

[Azure Policy](../governance/policy/index.yml) 은 Azure 리소스가 정책을 준수 하는지 확인할 수 있도록 하는 거 버 넌 스 도구입니다. Azure Machine Learning 작업 영역에서 개인 끝점을 사용 하는지 여부와 같은 특정 구성을 허용 하거나 적용 하도록 정책을 설정할 수 있습니다. Azure Policy에 대 한 자세한 내용은 [Azure Policy 설명서](../governance/policy/overview.md)를 참조 하세요. Azure Machine Learning 관련 된 정책에 대 한 자세한 내용은 [Azure Policy 준수 감사 및 관리](how-to-integrate-azure-policy.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [TLS를 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 Machine Learning 모델 사용](how-to-consume-web-service.md)
* [Azure Firewall과 함께 Azure Machine Learning 사용](how-to-access-azureml-behind-firewall.md)
* [Azure Virtual Network에서 Azure Machine Learning 사용](how-to-network-security-overview.md)
* [미사용 및 전송 중인 데이터 암호화](concept-data-encryption.md)
* [Azure에서 실시간 추천 API 빌드](/azure/architecture/reference-architectures/ai/real-time-recommendation)
