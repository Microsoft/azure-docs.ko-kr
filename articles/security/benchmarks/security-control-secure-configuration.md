---
title: Azure 보안 제어-보안 구성
description: Azure 보안 제어 보안 구성
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ac538d71f854643cb59390447be360bc541142ac
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077231"
---
# <a name="security-control-secure-configuration"></a>보안 제어: 보안 구성

공격자가 취약 한 서비스 및 설정을 악용 하지 못하도록 하기 위해 Azure 리소스의 보안 구성을 설정, 구현 및 적극적으로 관리 (추적, 보고, 수정) 합니다.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.1 | 5.1 | 고객 |

Azure Policy 별칭을 사용 하 여 Azure 리소스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 기본 제공 Azure Policy 정의를 사용할 수도 있습니다.

또한 Azure Resource Manager은 구성이 조직의 보안 요구 사항을 충족 하거나 초과 하는지 확인 하기 위해 검토 해야 하는 JavaScript Object Notation (JSON)에서 템플릿을 내보낼 수 있습니다.

Azure Security Center의 권장 사항을 Azure 리소스에 대 한 보안 구성 기준으로 사용할 수도 있습니다.

- [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [보안 권장 사항 - 참조 가이드](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.2 | 5.1 | 고객 |

Azure Security Center 권장 사항을 사용 하 여 모든 계산 리소스에 대 한 보안 구성을 유지 합니다.  또한 사용자 지정 운영 체제 이미지 또는 Azure Automation 상태 구성을 사용 하 여 조직에 필요한 운영 체제의 보안 구성을 설정할 수 있습니다.

- [Azure Security Center 권장 사항을 모니터링 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [보안 권장 사항 - 참조 가이드](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Azure Automation 상태 구성 개요](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [VHD를 업로드 하 고이를 사용 하 여 Azure에서 새 Windows Vm 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Azure CLI를 사용하여 사용자 지정 디스크에서 Linux VM 만들기](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.3 | 5.2 | 고객 |

Azure 리소스 전체에서 보안 설정을 적용 하려면 [거부] 및 [없는 경우 배포] Azure Policy 사용 합니다.  또한 Azure Resource Manager 템플릿을 사용 하 여 조직에 필요한 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다. 

- [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [규정 준수를 적용 하는 정책 만들기 및 관리](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Resource Manager 템플릿 개요](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.4 | 5.2 | 공유됨 |

Azure 계산 리소스에 대 한 취약성 평가를 수행 하는 Azure Security Center의 권장 사항을 따릅니다.  또한 Azure Resource Manager 템플릿, 사용자 지정 운영 체제 이미지 또는 Azure Automation 상태 구성을 사용 하 여 조직에 필요한 운영 체제의 보안 구성을 유지 관리할 수 있습니다.   Azure Automation 필요한 상태 구성과 결합 된 Microsoft 가상 머신 템플릿은 보안 요구 사항을 충족 하 고 유지 관리 하는 데 도움이 될 수 있습니다. 

또한 Microsoft에서 게시 하는 Azure Marketplace 가상 컴퓨터 이미지는 Microsoft에서 관리 하 고 유지 관리 합니다. 

- [Azure Security Center 취약성 평가 권장 사항을 구현 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Azure Resource Manager 템플릿에서 Azure Virtual Machine을 만드는 방법](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Azure Automation 상태 구성 개요](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Azure Portal에서 Windows 가상 컴퓨터 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [VM 템플릿을 다운로드 하는 방법에 대 한 정보](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Azure에 VHD를 업로드하고 새 VM을 만드는 샘플 스크립트](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.5 | 5.3 | 고객 |

Azure DevOps를 사용 하 여 사용자 지정 Azure 정책, Azure Resource Manager 템플릿 및 필요한 상태 구성 스크립트와 같은 코드를 안전 하 게 저장 하 고 관리할 수 있습니다. Azure DevOps에서 관리 하는 리소스에 액세스 하려면 Azure DevOps와 통합 된 경우 Azure Active Directory (Azure AD)에 정의 된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대 한 권한을 부여 하거나 거부할 수 있습니다. 또는 TFS와 통합 된 경우 Active Directory 합니다.

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps의 사용 권한 및 그룹 정보](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.6 | 5.3 | 고객 |

사용자 지정 이미지를 사용 하는 경우 Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다. 공유 이미지 갤러리를 사용하면 조직 내의 여러 사용자, 서비스 주체 또는 AD 그룹에게 이미지를 공유할 수 있습니다.  컨테이너 이미지의 경우 Azure Container Registry에 저장 하 고 Azure RBAC를 활용 하 여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다.  

- [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Container Registry에 대 한 Azure RBAC 이해](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Azure RBAC를 구성 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [공유 이미지 갤러리 개요](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.7 | 5.4 | 고객 |

Azure Policy를 사용 하 여 Azure 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. Azure Policy 별칭을 사용 하 여 Azure 리소스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 특정 리소스와 관련 된 기본 제공 정책 정의를 사용할 수도 있습니다.  또한 Azure Automation를 사용 하 여 구성 변경 내용을 배포할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [별칭을 사용 하는 방법](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.8 | 5.4 | 고객 |

Azure Automation 상태 구성은 모든 클라우드 또는 온-프레미스 데이터 센터에서 DSC (Desired State Configuration) 노드에 대 한 구성 관리 서비스입니다. 컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다. 

- [Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.9 | 5.5 | 고객 |

Azure Security Center를 사용 하 여 Azure 리소스에 대 한 기준 검색을 수행 합니다.  또한 Azure Policy를 사용 하 여 Azure 리소스 구성을 경고 하 고 감사 합니다.

- [Azure Security Center에서 권장 사항을 수정 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.10 | 5.5 | 고객 |

Azure Security Center를 사용 하 여 컨테이너에 대 한 OS 및 Docker 설정에 대 한 기준 검색을 수행 합니다.

- [Azure Security Center 컨테이너 권장 사항 이해](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.11 | 13.1 | 고객 |

Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 하 고 보호 합니다.

- [Azure 관리 Id와 통합 하는 방법](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Key Vault를 만드는 방법](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [관리 id를 사용 하 여 Key Vault 인증을 제공 하는 방법](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.12 | 4.1 | 고객 |

관리 되는 id를 사용 하 여 azure AD에서 자동으로 관리 되는 id를 Azure 서비스에 제공 합니다. 관리 ID를 사용하면 코드에 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

- [관리 Id를 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 7.13 | 18.1, 18.7 | 고객 |

자격 증명 스캐너를 구현 하 여 코드 내에서 자격 증명을 식별 합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다. 

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>다음 단계

- 다음 보안 제어를 참조 하세요.  [맬웨어 방어](security-control-malware-defense.md)