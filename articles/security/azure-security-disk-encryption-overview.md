---
title: 개요 - IaaS VM용 Azure Disk Encryption | Microsoft Docs
description: 이 문서에서는 IaaS VM용 Microsoft Azure Disk Encryption에 대해 간략히 설명합니다.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/16/2019
ms.custom: seodec18
ms.openlocfilehash: 66d788aec83e3e57a49b063f2ca80484360f639d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60612090"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>IaaS VM용 Azure Disk Encryption

Microsoft Azure는 데이터 프라이버시 및 데이터 독립성을 보장하기 위해 노력하고 있습니다. Azure를 사용하면 암호화 키를 암호화, 제어 및 관리하고 데이터 액세스를 제어 및 감사하는 광범위한 고급 기술을 통해 Azure 호스팅 데이터를 제어할 수 있습니다. Azure 고객은 이러한 제어를 통해 비즈니스 요구에 가장 적합한 솔루션을 유연하게 선택할 수 있습니다. 이 문서에서는 기술 솔루션: “Windows 및 Linux IaaS VM(가상 머신)에 대한 Azure Disk Encryption”을 소개합니다. 이 기술을 통해 고객 조직의 보안 및 규정 준수 약정에 맞게 데이터를 안전하게 보호할 수 있습니다. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>개요

Azure Disk Encryption은 Windows 및 Linux IaaS VM 디스크를 암호화할 수 있도록 하는 기능입니다. 디스크 암호화는 업계 표준인 Windows의 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 기능과 Linux의 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 기능을 활용하여 OS 및 데이터 디스크를 위한 볼륨 암호화를 제공합니다. 이 솔루션은 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/)와 통합되어 디스크 암호화 키와 비밀을 제어 및 관리할 수 있습니다. 이 솔루션은 VM 디스크에 있는 모든 데이터가 미사용 시 Azure Storage에 암호화되도록 합니다.

Windows 및 Linux IaaS VM용 Disk Encryption은 표준 VM 및 Azure Premium Storage가 있는 VM을 위해 모든 Azure 공용 지역 및 Azure Government 지역에서 일반 공급되고 있습니다. 디스크 암호화 관리 솔루션을 적용할 때 다음 비즈니스 요구 사항을 충족할 수 있습니다.

* IaaS VM은 업계 표준 암호화 기술을 사용하여 휴지 상태에서 보호되어 조직의 보안 및 규정 준수 요구 사항을 처리합니다.
* IaaS VM은 고객 제어 키 및 정책에 따라 부팅됩니다. 키 자격 증명 모음에서 사용량을 감사할 수 있습니다.

Azure Security Center를 사용하는 경우 암호화되지 않은 VM이 있는 경우 경고 메시지가 표시됩니다. 이 경고는 심각도가 높다고 표시되며 이러한 VM을 암호화하도록 권장합니다.

![Azure Security Center 디스크 암호화 경고](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> 특정 권장 사항으로 인해 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 라이선스 또는 구독 비용이 발생할 수 있습니다.


## <a name="encryption-scenarios"></a>암호화 시나리오

디스크 암호화 솔루션은 다음의 고객 시나리오를 지원합니다.

* 미리 암호화된 VHD 및 암호화 키에서 만든 새 Windows IaaS VM에서 암호화 사용
* 지원되는 Azure 갤러리 이미지에서 만든 새 IaaS VM에서 암호화 사용
* Azure에서 실행 중인 기존 IaaS VM에 대해 암호화 사용
* Windows 가상 머신 확장 집합에서 암호화 사용
* Linux 가상 머신 확장 집합에 대한 데이터 드라이브에서 암호화 사용
* Windows IaaS VM에서 암호화 사용 안 함
* Linux IaaS VM에 대한 데이터 드라이브에서 암호화 사용 안 함
* Windows 가상 머신 확장 집합에서 암호화 사용 안 함
* Linux 가상 머신 확장 집합에 대한 데이터 드라이브에서 암호화 사용 안 함
* 관리 디스크 VM의 암호화 사용
* 기존의 암호화된 Premium Storage VM 및 비 Premium Storage VM의 암호화 설정 업데이트
* 암호화된 VM 백업 및 복원

이 솔루션은 IaaS VM에 대한 다음 시나리오가 Microsoft Azure에서 사용되도록 설정될 경우 해당 시나리오를 지원합니다.

* Azure Key Vault와 통합
* 표준 계층 VM: [A, D, DS, G, GS, F 등, 시리즈 IaaS VM](https://azure.microsoft.com/pricing/details/virtual-machines/). 이러한 계층 내의 [Linux VM](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)은 7GB의 최소 메모리 요구 사항을 충족해야 함
* 지원되는 Azure 갤러리 이미지의 Windows 및 Linux IaaS VM, 관리 디스크, 확장 집합 VM에서 암호화 사용
* Windows IaaS VM, 확장 집합 VM 및 관리 디스크 VM에 대한 OS 및 데이터 드라이브에서 암호화 사용 안 함
* Linux IaaS VM, 확장 집합 VM 및 관리 디스크 VM에 대한 데이터 드라이브에서 암호화 사용 안 함
* Windows 클라이언트 OS를 실행하는 IaaS VM에서 암호화 사용
* 탑재 경로가 있는 볼륨에서 암호화 사용
* mdadm을 사용하여 디스크 스트라이프(RAID)로 구성된 Linux VM에서 암호화 사용
* 데이터 디스크에 대해 LVM을 사용하는 Linux VM에서 암호화 사용
* Linux VM OS 및 데이터 디스크에서 암호화 사용

   > [!NOTE]
   > 일부 Linux 배포에 대해서는 OS 드라이브 암호화가 지원되지 않습니다. 자세한 내용은 [Azure Disk Encryption FAQ](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 문서를 참조하세요.
   
* Windows Server 2016에서 Windows 저장소 공간 시작 부분으로 구성 된 Vm에서 암호화를 사용 합니다.
* 기존의 암호화된 Premium Storage VM 및 비 Premium Storage VM의 암호화 설정 업데이트
* KEK(키 암호화 키)를 사용하는 시나리오와 사용하지 않는 시나리오 모두에서 암호화된 VM의 백업 및 복원
* 모든 Azure 공용 지역 및 Azure Government 지역 지원됨

이 솔루션은 다음 시나리오, 기능 및 기술을 지원하지 않습니다.

* 기본 계층 IaaS VM
* Linux IaaS VM에 대한 OS 드라이브에서 암호화 사용 안 함
* OS 드라이브가 Linux IaaS VM에 대해 암호화되는 경우 데이터 드라이브에 암호화 사용 안 함
* Linux 가상 머신 확장에 대 한 OS 드라이브 암호화를 설정합니다.
* 클래식 VM 만들기 방법을 사용하여 만든 IaaS VM
* Linux IaaS VM에서 고객 사용자 지정 이미지의 암호화 사용
* 온-프레미스 키 관리 시스템과의 통합
* Azure 파일(공유 파일 시스템)
* NFS(네트워크 파일 시스템)
* 동적 볼륨
* 소프트웨어 기반 RAID 시스템으로 구성된 Windows VM

## <a name="encryption-features"></a>암호화 기능

Azure IaaS VM에 대한 Disk Encryption을 사용하도록 설정하고 배포할 때 제공된 구성에 따라 다음 기능이 활성화됩니다.

* 사용자 스토리지에서 미사용 부팅 볼륨을 보호하기 위해 OS 볼륨 암호화
* 사용자 스토리지에서 미사용 데이터 볼륨을 보호하기 위해 데이터 볼륨 암호화
* Windows IaaS VM에 대한 OS 및 데이터 드라이브에서 암호화 사용 안 함
* Linux IaaS VM에 대한 데이터 드라이브에서 암호화 사용 안 함(OS 드라이브가 암호화되지 않는 경우에만)
* 사용자의 Azure Key Vault 구독에서 암호화 키 및 비밀 보호
* 암호화된 IaaS VM의 암호화 상태 보고
* IaaS VM에서 디스크 암호화 구성 설정 제거
* Azure Backup 서비스로 암호화된 VM 백업 및 복원

Windows 및 Linux 솔루션용 IaaS VM에 대한 Azure Disk Encryption에는 다음 내용이 포함됩니다.

* Windows용 디스크 암호화 확장
* Linux용 디스크 암호화 확장
* PowerShell 디스크 암호화 cmdlet
* Azure CLI 디스크 암호화 cmdlet
* Azure Resource Manager 디스크 암호화 템플릿

Azure Disk Encryption 솔루션은 Windows 또는 Linux OS를 실행하는 IaaS VM에서 지원됩니다. 지원되는 운영 체제에 대한 자세한 내용은 [필수 구성 요소](azure-security-disk-encryption-prerequisites.md) 문서를 참조하세요.

> [!NOTE]
> Azure Disk Encryption으로 VM 디스크를 암호화하는 작업에 대한 추가 요금은 없습니다. 표준 [Key Vault 가격](https://azure.microsoft.com/pricing/details/key-vault/)은 암호화 키 저장에 사용되는 키 자격 증명 모음에 적용됩니다. 


## <a name="encryption-workflow"></a>암호화 워크플로

Windows 및 Linux VM용 디스크 암호화를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [암호화 시나리오](#encryption-scenarios) 섹션에 나열된 시나리오에서 암호화 시나리오를 선택합니다.

1. 고객은 Azure Disk Encryption Resource Manager 템플릿, PowerShell cmdlet 또는 Azure CLI를 통해 디스크 암호화를 사용하도록 선택하고 암호화 구성을 지정합니다.

   * 고객 암호화 VHD 시나리오의 경우, 암호화된 VHD를 저장소 계정에, 암호화 키 자료를 Key Vault에 업로드합니다. 그런 다음 암호화 구성을 제공하여 새로운 IaaS VM에서 암호화를 사용하도록 합니다.
   * Marketplace에서 만든 새 VM과 Azure에서 이미 실행 중인 기존의 VM에 대해 암호화 구성을 제공하여 IaaS VM에서 암호화를 사용하도록 합니다.

1. Azure 플랫폼에 대한 액세스를 부여하여 Key Vault에서 암호화 키 자료(Windows 시스템의 경우 BitLocker 암호화 키, Linux의 경우 Passphrase)를 읽고 IaaS VM에서 암호화를 사용하도록 합니다.

1. Azure에서는 암호화 및 Key Vault 구성으로 VM 서비스 모델을 업데이트하고 암호화된 VM을 설정합니다.

   ![Azure의 Microsoft 맬웨어 방지](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>암호 해독 워크플로
IaaS VM용 디스크 암호화를 사용하지 않도록 설정하려면 다음 대략적인 단계를 완료합니다.

1. Azure에서 실행되는 IaaS VM에 대해 암호화(암호 해독)를 사용하지 않도록 설정하고 암호 해독 구성을 지정합니다. Azure Disk Encryption Resource Manager 템플릿, PowerShell cmdlet 또는 Azure CLI를 통해 사용하지 않도록 설정할 수 있습니다.

   이 단계는 실행 중인 Windows IaaS VM에서 OS 또는 데이터 볼륨이나 둘 모두의 암호화를 사용하지 않습니다. 이전 섹션에 나와 있는 것처럼 Linux에 대해 OS 디스크를 암호화하지 않도록 설정하는 것은 지원되지 않습니다. 암호 해독 단계는 OS 디스크가 암호화되지 않는 한 Linux VM의 데이터 드라이브에만 허용됩니다.

1. Azure에서는 VM 서비스 모델을 업데이트하고 IaaS VM은 암호가 해독되었다고 표시합니다. VM의 콘텐츠는 더 이상 미사용 상태에서 암호화되지 않습니다.

   > [!NOTE]
   > 암호화 사용 안 함 작업은 Key Vault 및 암호화 키 자료(Windows 시스템의 경우 BitLocker 암호화 키, Linux의 경우 Passphrase)를 삭제하지 않습니다.
   >
   > Linux OS 디스크 암호화는 사용하지 않도록 설정할 수 없습니다. 암호 해독 단계는 Linux VM에서 데이터 드라이브에만 허용됩니다.
   >
   > OS 드라이브가 암호화되는 경우 Linux에 데이터 디스크 암호화를 사용하지 않도록 설정할 수 없습니다.


## <a name="encryption-workflow-previous-release"></a>암호화 워크플로(이전 릴리스)

Azure Disk Encryption의 새 릴리스는 Azure AD(Azure Active Directory) 애플리케이션 매개 변수 제공에 대한 요구 사항을 제거하여 VM 디스크 암호화를 사용하도록 설정합니다. 새 릴리스를 사용하면 암호화 단계를 사용하는 동안 더 이상 Azure AD 자격 증명을 제공할 필요가 없습니다. 새 릴리스를 사용하는 경우 모든 새 VM은 Azure AD 애플리케이션 매개 변수를 사용하지 않고 암호화되어야 합니다. Azure AD 애플리케이션 매개 변수를 사용하여 이미 암호화된 VM도 여전히 지원되며 Azure AD 구문을 사용하여 계속 유지 관리되어야 합니다. Windows 및 Linux VM(이전 릴리스)용 디스크 암호화를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [암호화 시나리오](#encryption-scenarios) 섹션에 나열된 시나리오에서 암호화 시나리오를 선택합니다.

1. 고객은 Azure Disk Encryption Resource Manager 템플릿, PowerShell cmdlet 또는 Azure CLI를 통해 디스크 암호화를 사용하도록 선택하고 암호화 구성을 지정합니다.

   * 고객 암호화 VHD 시나리오의 경우, 암호화된 VHD를 저장소 계정에, 암호화 키 자료를 Key Vault에 업로드합니다. 그런 다음 암호화 구성을 제공하여 새로운 IaaS VM에서 암호화를 사용하도록 합니다.
   * Marketplace에서 만든 새 VM과 Azure에서 이미 실행 중인 기존의 VM에 대해 암호화 구성을 제공하여 IaaS VM에서 암호화를 사용하도록 합니다.

1. Azure 플랫폼에 대한 액세스를 부여하여 Key Vault에서 암호화 키 자료(Windows 시스템의 경우 BitLocker 암호화 키, Linux의 경우 Passphrase)를 읽고 IaaS VM에서 암호화를 사용하도록 합니다.

1. 암호화 키 자료를 Key Vault에 기록하기 위한 Azure AD 애플리케이션 ID를 제공합니다. 이 단계에서는 2단계에서 설명한 시나리오인 경우 IaaS VM에서 암호화가 가능합니다.

1. Azure에서는 암호화 및 Key Vault 구성으로 VM 서비스 모델을 업데이트하고 암호화된 VM을 설정합니다.


## <a name="terminology"></a>용어
다음 표에서는 이 기술에 사용되는 몇 가지 일반적인 용어를 정의합니다.

| 용어 | 정의 |
| --- | --- |
| Azure AD | [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) 계정은 키 자격 증명 모음에서 비밀을 인증, 저장 및 검색하는 데 사용됩니다. |
| Azure Key Vault | Key Vault는 FIPS(Federal Information Processing Standard) 검증 하드웨어 보안 모듈을 기반으로 하는 암호화 키 관리 서비스입니다. 이러한 표준은 암호화 키 및 중요한 비밀을 보호하는 데 도움이 됩니다. 자세한 내용은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서를 참조하세요. |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx)는 Windows IaaS VM에서 디스크 암호화를 설정하는 데 사용되는 업계에서 공인된 Windows 볼륨 암호화 기술입니다. |
| BEK | BEK(BitLocker 암호화 키)는 OS 부팅 볼륨 및 데이터 볼륨을 암호화하는 데 사용됩니다. BEK는 Key Vault에서 비밀 보안 수단입니다. |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli)는 명령줄에서 Azure 리소스를 관리하고 관리하는 데 최적화되어 있습니다.|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)는 Linux IaaS VM에서 디스크 암호화를 설정하는 데 사용되는 Linux 기반의 투명한 디스크 암호화 하위 시스템입니다. |
| KEK | KEK(주요 암호화 키)는 비밀을 보호하거나 래핑하는 데 사용할 수 있는 비대칭 키(RSA 2048)입니다. HSM(하드웨어 보안 모듈) 보호 키 또는 소프트웨어 보호 키를 제공할 수 있습니다. 자세한 내용은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서를 참조하세요. |
| PowerShell cmdlet | 자세한 내용은 [Azure PowerShell cmdlet](/powershell/azure/overview)을 참조하세요. |

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Disk Encryption 필수 구성 요소](azure-security-disk-encryption-prerequisites.md)
