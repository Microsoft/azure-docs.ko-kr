---
title: Windows Vm에 대 한 Azure Disk Encryption 사용
description: 이 문서에서는 Windows Vm에 대 한 Microsoft Azure 디스크 암호화를 사용 하도록 설정 하는 지침을 제공 합니다.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 05db717f5d3adc2429431503f588f2cc7f79aef6
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72435742"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Windows Vm에 대 한 Azure Disk Encryption 

Azure Disk Encryption는 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호 하 고 보호 합니다. Windows의 [Bitlocker](https://en.wikipedia.org/wiki/BitLocker) 기능을 사용 하 여 Azure vm (가상 머신)의 OS 및 데이터 디스크에 대 한 볼륨 암호화를 제공 하 고, 디스크 암호화 키 및 비밀을 제어 하 고 관리 하는 데 도움을 주는 [Azure Key Vault](../../key-vault/index.yml) 와 통합 됩니다. 

[Azure Security Center](../../security-center/index.yml)사용 하는 경우 암호화 되지 않은 vm이 있는 경우 경고가 표시 됩니다. 이 경고는 심각도가 높다고 표시되며 이러한 VM을 암호화하도록 권장합니다.

![Azure Security Center 디스크 암호화 경고](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 이전에 Azure AD에서 Azure Disk Encryption 사용 하 여 VM을 암호화 한 경우 계속이 옵션을 사용 하 여 VM을 암호화 해야 합니다. 자세한 내용은 [AZURE AD (이전 릴리스)를 사용 하 여 Azure Disk Encryption](disk-encryption-overview-aad.md) 를 참조 하세요. 
> - 특정 권장 사항으로 인해 데이터, 네트워크 또는 컴퓨팅 리소스 사용량이 증가할 수 있으며 이로 인해 라이선스 또는 구독 비용이 발생합니다. 사용자는 유효한 활성 Azure 구독을 포함하여 지원되는 지역에서 Azure에 리소스를 만들어야 합니다.

[Azure CLI를 사용 하 여 WINDOWS Vm 만들기 및 암호화 빠른](disk-encryption-cli-quickstart.md) 시작 또는 [Azure Powershell을 사용 하 여 windows vm 만들기 및 암호화 빠른](disk-encryption-powershell-quickstart.md)시작을 사용 하 여 몇 분만에 windows 용 Azure Disk Encryption의 기본 사항을 배울 수 있습니다.

## <a name="supported-vms-and-operating-systems"></a>지원 되는 Vm 및 운영 체제

### <a name="supported-vm-sizes"></a>지원되는 VM 크기

Windows Vm은 [다양 한 크기로](sizes-general.md)사용할 수 있습니다. Azure Disk Encryption는 [기본, a 시리즈 vm](https://azure.microsoft.com/pricing/details/virtual-machines/series/)또는 2gb 미만의 메모리가 있는 가상 컴퓨터에서 사용할 수 없습니다.

Azure Disk Encryption은 프리미엄 저장소가 있는 Vm에도 사용할 수 있습니다.

### <a name="supported-operating-systems"></a>지원되는 운영 체제

- Windows 클라이언트: Windows 8 이상
- Windows Server: Windows Server 2008 R2 이상  
 
> [!NOTE]
> Windows Server 2008 r 2를 사용 하려면 암호화를 위해 .NET Framework 4.5이 설치 되어 있어야 합니다. 선택적 업데이트 Microsoft .NET Framework 4.5.2 for Windows Server 2008 R2 x64 기반 systems (x64 기반 시스템) ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983))를 사용 하 여 Windows 업데이트에서 설치 합니다.  
>  
> Windows Server 2012 R2 Core 및 Windows Server 2016 Core를 사용 하려면 암호화를 위해 VM에 bdehdcfg 구성 요소를 설치 해야 합니다.


## <a name="networking-requirements"></a>네트워킹 요구 사항
Azure Disk Encryption를 사용 하도록 설정 하려면 Vm이 다음 네트워크 엔드포인트 구성 요구 사항을 충족 해야 합니다.
  - 키 자격 증명 모음에 연결 하는 토큰을 가져오려면 Windows VM이 Azure Active Directory 엔드포인트 \[login.microsoftonline.com\]에 연결할 수 있어야 합니다.
  - 키 자격 증명 모음에 암호화 키를 쓰려면 Windows VM에서 키 자격 증명 모음 엔드포인트에 연결할 수 있어야 합니다.
  - Windows VM은 Azure 확장 리포지토리를 호스팅하는 Azure storage 엔드포인트 및 VHD 파일을 호스팅하는 Azure storage 계정에 연결할 수 있어야 합니다.
  -  보안 정책이 Azure VM에서 인터넷으로 액세스를 제한하는 경우 이전 URI를 확인하고 IP에 대한 아웃바운드 연결을 허용하도록 특정 규칙을 구성할 수 있습니다. 자세한 내용은 [방화벽 뒤에 있는 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)를 참조하세요.    


## <a name="group-policy-requirements"></a>그룹 정책 요구 사항

Azure Disk Encryption는 Windows Vm에 대해 BitLocker 외부 키 보호기를 사용 합니다. 도메인 가입 VM의 경우 TPM 보호기를 적용하는 그룹 정책을 푸시하지 않습니다. "호환되는 TPM이 없이 BitLocker 허용"에 대한 그룹 정책 정보는 [BitLocker 그룹 정책 참조](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)를 참조하세요.

사용자 지정 그룹 정책을 사용 하는 도메인에 가입 된 가상 컴퓨터의 BitLocker 정책에는 [bitlocker 복구 정보의 사용자 저장소 구성-256 비트 복구 키 허용 >](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)설정이 포함 되어야 합니다. BitLocker에 대한 사용자 지정 그룹 정책 설정이 호환되지 않으면 Azure Disk Encryption이 실패합니다. 올바른 정책 설정이 없는 머신에서 새 정책을 적용하고, 새 정책을 강제로 업데이트한(gpupdate.exe /force) 다음, 다시 시작해야 할 수 있습니다.

도메인 수준 그룹 정책이 BitLocker에서 사용 되는 AES CBC 알고리즘을 차단 하는 경우 Azure Disk Encryption 실패 합니다.

## <a name="encryption-key-storage-requirements"></a>암호화 키 저장소 요구 사항  

Azure Disk Encryption에서 디스크 암호화 키와 암호를 제어 하 고 관리 하는 Azure Key Vault 필요 합니다. 주요 자격 증명 모음 및 Vm은 동일한 Azure 지역 및 구독에 있어야 합니다.

자세한 내용은 [Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)을 참조 하세요.

## <a name="terminology"></a>용어
다음 표에서는 Azure disk encryption 설명서에서 사용 되는 일반적인 용어 중 일부를 정의 합니다.

| 용어 | 정의 |
| --- | --- |
| Azure Key Vault | Key Vault는 FIPS(Federal Information Processing Standard) 검증 하드웨어 보안 모듈을 기반으로 하는 암호화 키 관리 서비스입니다. 이러한 표준은 암호화 키 및 중요한 비밀을 보호하는 데 도움이 됩니다. 자세한 내용은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서 및 [Azure Disk Encryption에 대 한 주요 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)을 참조 하세요. |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli)는 명령줄에서 Azure 리소스를 관리하고 관리하는 데 최적화되어 있습니다.|
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) 는 windows vm에서 디스크 암호화를 사용 하도록 설정 하는 데 사용 되는 업계에서 인식 되는 windows 볼륨 암호화 기술입니다. |
| 키 암호화 키 (KEK) | 비밀을 보호 하거나 래핑하는 데 사용할 수 있는 비대칭 키 (RSA 2048)입니다. HSM(하드웨어 보안 모듈) 보호 키 또는 소프트웨어 보호 키를 제공할 수 있습니다. 자세한 내용은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서 및 [Azure Disk Encryption에 대 한 주요 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)을 참조 하세요. |
| PowerShell cmdlet | 자세한 내용은 [Azure PowerShell cmdlet](/powershell/azure/overview)을 참조하세요. |


## <a name="next-steps"></a>다음 단계

- [빠른 시작-Azure CLI를 사용 하 여 Windows VM 만들기 및 암호화](disk-encryption-cli-quickstart.md)
- [빠른 시작-Azure Powershell을 사용 하 여 Windows VM 만들기 및 암호화](disk-encryption-powershell-quickstart.md)
- [Windows VM에 대한 Azure Disk Encryption 시나리오](disk-encryption-windows.md)
- [Azure Disk Encryption 필수 구성 요소 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started)
- [필수 조건 PowerShell 스크립트 Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)


