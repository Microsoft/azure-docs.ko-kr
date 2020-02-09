---
title: Linux Vm에 대 한 Azure Disk Encryption 사용
description: 이 문서에서는 Linux Vm에 대해 Microsoft Azure 디스크 암호화를 사용 하도록 설정 하는 지침을 제공 합니다.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 96ac1becfed74141b3b1544646f5d82bd0985045
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988423"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Linux Vm에 대 한 Azure Disk Encryption 

Azure Disk Encryption는 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호 하 고 보호 합니다. Linux의 [DM](https://en.wikipedia.org/wiki/Dm-crypt) 기능을 사용 하 여 Azure vm (가상 머신)의 OS 및 데이터 디스크에 대 한 볼륨 암호화를 제공 하 고, 디스크 암호화 키 및 비밀을 제어 하 고 관리 하는 데 도움을 주는 [Azure Key Vault](../../key-vault/index.yml) 와 통합 됩니다. 

[Azure Security Center](../../security-center/index.yml)사용 하는 경우 암호화 되지 않은 vm이 있는 경우 경고가 표시 됩니다. 이 경고는 심각도가 높다고 표시되며 이러한 VM을 암호화하도록 권장합니다.

![Azure Security Center 디스크 암호화 경고](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 이전에 Azure AD에서 Azure Disk Encryption를 사용 하 여 VM을 암호화 한 경우에는이 옵션을 계속 사용 하 여 VM을 암호화 해야 합니다. 자세한 내용은 [AZURE AD (이전 릴리스)를 사용 하 여 Azure Disk Encryption](disk-encryption-overview-aad.md) 를 참조 하세요. 
> - 특정 권장 사항으로 인해 데이터, 네트워크 또는 컴퓨팅 리소스 사용량이 증가할 수 있으며 이로 인해 라이선스 또는 구독 비용이 발생합니다. 사용자는 유효한 활성 Azure 구독을 포함하여 지원되는 지역에서 Azure에 리소스를 만들어야 합니다.
> - 현재 2 세대 Vm은 Azure Disk Encryption를 지원 하지 않습니다. 자세한 내용은 [Azure의 2 세대 vm에 대 한 지원](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) 을 참조 하세요.

[Azure CLI를 사용 하 여 LINUX Vm 만들기 및 암호화](disk-encryption-cli-quickstart.md) 빠른 시작 또는 [Azure Powershell을 사용 하 여 linux vm 만들기 및 암호화 빠른](disk-encryption-powershell-quickstart.md)시작을 사용 하 여 몇 분만에 linux 용 Azure Disk Encryption의 기본 사항을 배울 수 있습니다.

## <a name="supported-vms-and-operating-systems"></a>지원 되는 Vm 및 운영 체제

### <a name="supported-vm-sizes"></a>지원되는 VM 크기

Linux Vm은 [다양 한 크기로](sizes.md)사용할 수 있습니다. Azure Disk Encryption는 [기본, A 시리즈 vm](https://azure.microsoft.com/pricing/details/virtual-machines/series/)또는 이러한 최소 메모리 요구 사항을 충족 하지 않는 가상 머신에서 사용할 수 없습니다.

| 가상 머신 | 최소 메모리 요구 사항 |
|--|--|
| 데이터 볼륨만 암호화 하는 경우 Linux Vm| 2GB |
| 데이터 및 OS 볼륨을 모두 암호화 하는 경우와 루트 (/) 파일 시스템 사용량이 4GB 이하인 경우 Linux Vm | 8GB |
| 데이터 및 OS 볼륨을 모두 암호화 하는 경우와 루트 (/) 파일 시스템 사용량이 4GB 보다 큰 경우 Linux Vm | 루트 파일 시스템 사용량 * 2. 예를 들어 16gb의 루트 파일 시스템 사용에는 32GB 이상의 RAM이 필요 합니다. |

Linux 가상 머신에서 OS 디스크 암호화 프로세스가 완료 되 면 메모리를 줄이고 실행 하도록 VM을 구성할 수 있습니다. 

Azure Disk Encryption은 프리미엄 저장소가 있는 Vm에도 사용할 수 있습니다. 

### <a name="supported-operating-systems"></a>지원되는 운영 체제

Azure Disk Encryption는 [Azure 보증 linux 배포판](endorsed-distros.md)의 하위 집합에서 지원 됩니다. 즉, 모든 linux server에서 사용할 수 있는 배포의 하위 집합입니다.

![Azure Disk Encryption를 지 원하는 Linux 서버 배포판의 벤 다이어그램](./media/disk-encryption/ade-supported-distros.png)

Azure에서 보증 되지 않는 Linux 서버 배포는 Azure Disk Encryption을 지원 하지 않습니다. 보증 인 경우 다음 배포 및 버전 에서만 Azure Disk Encryption 지원 됩니다.

| Linux 배포 | 버전 | 암호화에 지원되는 볼륨 유형|
| --- | --- |--- |
| Ubuntu | 18.04| OS 및 데이터 디스크 |
| Ubuntu | 16.04| OS 및 데이터 디스크 |
| Ubuntu | 14.04.5</br>[4.15 이상으로 업데이트된 Azure 튜닝 커널 포함](disk-encryption-troubleshooting.md) | OS 및 데이터 디스크 |
| RHEL | 7.7 | OS 및 데이터 디스크 (아래 참고 참조) |
| RHEL | 7.6 | OS 및 데이터 디스크 (아래 참고 참조) |
| RHEL | 7.5 | OS 및 데이터 디스크 (아래 참고 참조) |
| RHEL | 7.4 | OS 및 데이터 디스크 (아래 참고 참조) |
| RHEL | 7.3 | OS 및 데이터 디스크 (아래 참고 참조) |
| RHEL | 7.2 | OS 및 데이터 디스크 (아래 참고 참조) |
| RHEL | 6.8 | 데이터 디스크 (아래 참고 참조) |
| RHEL | 6.7 | 데이터 디스크 (아래 참고 참조) |
| CentOS | 7.7 | OS 및 데이터 디스크 |
| CentOS | 7.6 | OS 및 데이터 디스크 |
| CentOS | 7.5 | OS 및 데이터 디스크 |
| CentOS | 7.4 | OS 및 데이터 디스크 |
| CentOS | 7.3 | OS 및 데이터 디스크 |
| CentOS | 7.2n | OS 및 데이터 디스크 |
| CentOS | 6.8 | 데이터 디스크 |
| openSUSE | 42.3 | 데이터 디스크 |
| SLES | 12-SP4 | 데이터 디스크 |
| SLES | 12-SP3 | 데이터 디스크 |

> [!NOTE]
> 새 Azure Disk Encryption 구현은 RHEL7 종 량 제 이미지에 대 한 RHEL OS 및 데이터 디스크에 대해 지원 됩니다.  
>
> 또한 ADE는 구독이 등록 된 **후** 에만 RHEL 골드 이미지에 대해 지원 됩니다. 자세한 내용은 [Azure에서 사용자 고유의 구독 골드 이미지 Red Hat Enterprise Linux](../workloads/redhat/byos.md##encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) 를 참조 하세요.

## <a name="additional-vm-requirements"></a>추가 VM 요구 사항

Azure Disk Encryption 시스템에 dm 및 vfat 모듈이 있어야 합니다. 기본 이미지에서 vfat을 제거 하거나 사용 하지 않도록 설정 하면 시스템에서 키 볼륨을 읽고 이후 다시 부팅 시 디스크 잠금을 해제 하는 데 필요한 키를 가져올 수 없습니다. 시스템에서 vfat 모듈을 제거 하는 시스템 강화 단계는 Azure Disk Encryption와 호환 되지 않습니다. 

암호화를 사용 하도록 설정 하기 전에 암호화 될 데이터 디스크가/etc/fstab에 적절히 나열 되어 있어야 합니다. "/dev/sdX" 형식의 디바이스 이름은 특히 암호화가 적용된 후 다시 부팅할 때 동일한 디스크에 연결될 수 없으므로 이 항목에 영구 블록 디바이스 이름을 사용합니다. 이 동작에 대한 자세한 내용은 [Linux VM 디바이스 이름 변경 문제 해결](troubleshoot-device-names-problems.md)을 참조하세요.

/etc/fstab 설정이 탑재에 대해 올바르게 구성되었는지 확인합니다. 이러한 설정을 구성하려면 mount -a 명령을 실행하거나 VM을 다시 부팅하고 다시 탑재를 트리거합니다. 이러한 작업이 완료되면 lsblk 명령의 출력을 확인하여 드라이브가 여전히 탑재되어 있는지 확인합니다. 
- 암호화를 사용하도록 설정하기 전에 /etc/fstab 파일에서 드라이브를 올바르게 탑재하지 않으면 Azure Disk Encryption에서 해당 드라이브를 올바르게 탑재할 수 없습니다.
- Azure Disk Encryption 프로세스는 암호화 프로세스의 일환으로 탑재 정보를 /etc/fstab에서 자체의 구성 파일로 이동합니다. 데이터 드라이브 암호화가 완료된 후 /etc/fstab에서 누락된 항목을 보고 놀라지 마세요.
- 암호화를 시작 하기 전에 탑재 된 데이터 디스크에 쓸 수 있는 모든 서비스 및 프로세스를 중지 하 고이를 사용 하지 않도록 설정 하 여 다시 부팅 한 후 자동으로 다시 시작 하지 않도록 합니다. 이러한 파티션에 파일을 열어 두면 암호화 프로시저에서 파일을 다시 탑재 하 여 암호화에 실패 하 게 됩니다. 
- 다시 부팅한 후 Azure Disk Encryption 프로세스에서 새로 암호화된 디스크를 탑재하는 데 시간이 걸립니다. 이러한 디스크는 다시 부팅한 후에 즉시 사용할 수 없습니다. 다른 프로세스가 액세스할 수 있게 되기 전에 이 프로세스에서 암호화된 드라이브를 시작, 잠금 해제 및 탑재하는 데 시간이 필요합니다. 이 프로세스는 시스템 특성에 따라 다시 부팅 후 2분 이상 걸릴 수 있습니다.

데이터 디스크를 탑재 하 고 필요한/etc/fstab 항목을 만드는 데 사용할 수 있는 명령의 예는 [Azure Disk Encryption 필수 구성 요소 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started) (244-248 줄) 및 [Azure Disk Encryption 필수 구성 요소 PowerShell 스크립트](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)에서 찾을 수 있습니다. 

## <a name="networking-requirements"></a>네트워킹 요구 사항

Azure Disk Encryption 기능을 사용 하도록 설정 하려면 Linux Vm이 다음 네트워크 엔드포인트 구성 요구 사항을 충족 해야 합니다.
  - 키 자격 증명 모음에 연결 하는 토큰을 가져오려면 Linux VM이 Azure Active Directory 엔드포인트 \[login.microsoftonline.com\]에 연결할 수 있어야 합니다.
  - 키 자격 증명 모음에 암호화 키를 쓰려면 Linux VM에서 키 자격 증명 모음 엔드포인트에 연결할 수 있어야 합니다.
  - Linux VM은 Azure 확장 리포지토리를 호스팅하는 Azure storage 엔드포인트 및 VHD 파일을 호스팅하는 Azure storage 계정에 연결할 수 있어야 합니다.
  -  보안 정책이 Azure VM에서 인터넷으로 액세스를 제한하는 경우 이전 URI를 확인하고 IP에 대한 아웃바운드 연결을 허용하도록 특정 규칙을 구성할 수 있습니다. 자세한 내용은 [방화벽 뒤에 있는 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)를 참조하세요.  

## <a name="encryption-key-storage-requirements"></a>암호화 키 저장소 요구 사항  

Azure Disk Encryption에서 디스크 암호화 키와 암호를 제어 하 고 관리 하는 Azure Key Vault 필요 합니다. 주요 자격 증명 모음 및 Vm은 동일한 Azure 지역 및 구독에 있어야 합니다.

자세한 내용은 [Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)을 참조 하세요.

## <a name="terminology"></a>용어
다음 표에서는 Azure disk encryption 설명서에서 사용 되는 일반적인 용어 중 일부를 정의 합니다.

| 용어 | 정의 |
| --- | --- |
| Azure Key Vault | Key Vault는 FIPS(Federal Information Processing Standard) 검증 하드웨어 보안 모듈을 기반으로 하는 암호화 키 관리 서비스입니다. 이러한 표준은 암호화 키 및 중요한 비밀을 보호하는 데 도움이 됩니다. 자세한 내용은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서 및 [Azure Disk Encryption에 대 한 주요 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)을 참조 하세요. |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli)는 명령줄에서 Azure 리소스를 관리하고 관리하는 데 최적화되어 있습니다.|
| DM-Crypt |[DM](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) 은 linux vm에서 디스크 암호화를 사용 하도록 설정 하는 데 사용 되는 linux 기반의 투명 한 디스크 암호화 하위 시스템입니다. |
| 키 암호화 키 (KEK) | 비밀을 보호 하거나 래핑하는 데 사용할 수 있는 비대칭 키 (RSA 2048)입니다. HSM(하드웨어 보안 모듈) 보호 키 또는 소프트웨어 보호 키를 제공할 수 있습니다. 자세한 내용은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서 및 [Azure Disk Encryption에 대 한 주요 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)을 참조 하세요. |
| PowerShell cmdlet | 자세한 내용은 [Azure PowerShell cmdlet](/powershell/azure/overview)을 참조하세요. |


## <a name="next-steps"></a>다음 단계

- [빠른 시작-Azure CLI를 사용 하 여 Linux VM 만들기 및 암호화](disk-encryption-cli-quickstart.md)
- [빠른 시작-Azure Powershell을 사용 하 여 Linux VM 만들기 및 암호화](disk-encryption-powershell-quickstart.md)
- [Linux VM에 대한 Azure Disk Encryption 시나리오](disk-encryption-linux.md)
- [Azure Disk Encryption 필수 구성 요소 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started)
- [필수 조건 PowerShell 스크립트 Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)


