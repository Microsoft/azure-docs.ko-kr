---
title: Azure Active Directory 자격 증명을 사용 하 여 Linux VM에 로그인 합니다.
description: Azure Active Directory 인증을 사용 하 여 로그인 하는 Linux VM을 만들고 구성 하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/29/2019
ms.author: iainfou
ms.openlocfilehash: 9980ad7af4a9e5db1d93ffb389ef7b04209b8c43
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76544619"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>미리 보기: Azure Active Directory 인증을 사용 하 여 Azure에서 Linux 가상 머신에 로그인 합니다.

Azure에서 Linux VM(가상 머신)의 보안을 강화하려면 Azure AD(Active Directory) 인증으로 통합하면 됩니다. Linux VM에 Azure AD 인증을 사용하면 VM에 대한 액세스를 허용하거나 거부하는 정책을 중앙에서 제어하고 적용할 수 있습니다. 이 아티클에서는 Azure AD 인증을 사용하도록 Linux VM을 만들고 구성하는 방법을 보여줍니다.


> [!IMPORTANT]
> Azure Active Directory 인증은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
> 테스트 후에 삭제하려는 테스트 가상 머신에서 이 기능을 사용합니다.
>


Azure에서 Azure AD 인증을 사용하여 Linux VM에 로그인하는 경우 다음과 같은 많은 혜택이 있습니다.

- **보안 향상:**
  - 회사 AD 자격 증명을 사용하여 Azure Linux VM에 로그인할 수 있습니다. 로컬 관리자 계정을 만들고 자격 증명 수명을 관리하지 않아도 됩니다.
  - 로컬 관리자 계정에 대한 의존성을 줄임으로써 자격 증명 손실/도난, 약한 자격 증명을 구성하는 사용자 등을 걱정할 필요가 없습니다.
  - Azure AD 디렉터리에 구성된 암호 복잡성 및 암호 수명 정책을 통해 Linux VM을 보호할 수 있습니다.
  - Azure 가상 머신에 대한 로그인의 보안을 강화하려면 다단계 인증을 구성하면 됩니다.
  - Azure Active Directory를 사용하여 Linux VM에 로그인하는 기능은 [Federation Services](../../active-directory/hybrid/how-to-connect-fed-whatis.md)를 사용하는 고객에게도 적용됩니다.

- **원활한 협업:** RBAC(역할 기반 액세스 제어)를 사용하여 지정된 VM에 로그인하는 사용자를 일반 사용자로 또는 관리자 권한으로 지정할 수 있습니다. 사용자가 팀에 조인하거나 나가는 경우 적절한 액세스 권한을 부여하도록 VM에 대한 RBAC 정책을 업데이트할 수 있습니다. 이 환경은 불필요한 SSH 공개 키를 제거하기 위해 VM을 삭제하는 것보다 훨씬 더 간단합니다. 직원이 조직을 나가고 해당 사용자 계정을 비활성화하거나 Azure AD에서 제거한 경우 더 이상 리소스에 액세스할 수 없습니다.

## <a name="supported-azure-regions-and-linux-distributions"></a>지원되는 Azure 지역 및 Linux 배포

현재 이 기능의 미리 보기 기간 동안 다음과 같은 Linux 배포가 지원됩니다.

| 유통 | 버전 |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 및 Ubuntu Server 18.04 |


현재 이 기능의 미리 보기 기간 동안 다음과 같은 Azure 지역이 지원됩니다.

- 전 세계 모든 Azure 지역

>[!IMPORTANT]
> 이 미리 보기 기능을 사용하려면 지원되는 Linux 배포판만을 지원되는 Azure 지역에 배포합니다. Azure Government 또는 소버린 클라우드에서 이 기능이 지원되지 않습니다.


CLI를 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.31 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="network-requirements"></a>네트워크 요구 사항

Azure에서 Linux Vm에 대해 Azure AD 인증을 사용 하도록 설정 하려면 Vm 네트워크 구성에서 TCP 포트 443을 통해 다음 엔드포인트에 대 한 아웃 바운드 액세스를 허용 하는지 확인 해야 합니다.

* https:\//login.microsoftonline.com
* https:\//device.login.microsoftonline.com
* https:\//pas.windows.net
* https:\//management.azure.com
* https:\//packages.microsoft.com

> [!NOTE]
> 현재 azure AD 인증을 사용 하도록 설정 된 Vm에 대해 Azure 네트워크 보안 그룹을 구성할 수 없습니다.

## <a name="create-a-linux-virtual-machine"></a>Linux 가상 머신 만들기

[az group create](/cli/azure/group#az-group-create)를 사용하여 리소스 그룹을 만든 다음, 지원되는 지역에서 지원되는 배포판을 사용하는 [az vm create](/cli/azure/vm#az-vm-create)를 사용하여 VM을 만듭니다. 다음 예제에서는 *Ubuntu 16.04 LTS*를 사용하는 *myVM*이라는 VM을 *southcentralus* 지역에서 *myResourceGroup*이라는 리소스 그룹에 배포합니다. 다음 예제에서는 필요에 따라 고유한 리소스 그룹 및 VM 이름을 제공할 수 있습니다.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM 및 지원 리소스를 만드는 데 몇 분이 걸립니다.

## <a name="install-the-azure-ad-login-vm-extension"></a>Azure AD 로그인 VM 확장 설치

> [!NOTE]
> 이전에 만든 VM에이 확장을 배포 하는 경우 컴퓨터에 최소 1GB의 메모리가 할당 되어 있는지 확인 하 고, 그렇지 않으면 확장을 설치 하지 못합니다.

Azure AD 자격 증명을 사용 하 여 Linux VM에 로그인 하려면 Azure Active Directory 로그인 VM 확장을 설치 합니다. VM 확장은 Azure 가상 머신에서 배포 후 구성 및 Automation 작업을 제공하는 작은 애플리케이션입니다. [az vm extension set](/cli/azure/vm/extension#az-vm-extension-set)을 사용하여 *myResourceGroup* 리소스 그룹의 *myVM*이라는 VM에 *AADLoginForLinux* 확장을 설치합니다.

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

확장이 VM에 성공적으로 설치 되 면 *provisioningState* 의 *성공* 이 표시 됩니다.

## <a name="configure-role-assignments-for-the-vm"></a>VM에 대한 역할 할당 구성

Azure RBAC(역할 기반 액세스 제어) 정책은 VM에 로그인할 수 있는 사용자를 결정합니다. 두 개의 RBAC 역할을 사용하여 VM 로그인 권한을 부여합니다.

- **가상 머신 관리자 로그인**: 이 역할이 할당된 사용자는 Windows 관리자 또는 Linux 루트 사용자 권한으로 Azure 가상 머신에 로그인할 수 있습니다.
- **가상 머신 사용자 로그인**: 이 역할이 할당된 사용자는 일반 사용자 권한으로 Azure 가상 머신에 로그인할 수 있습니다.

> [!NOTE]
> 사용자가 SSH를 통해 VM에 로그인하려면 *가상 머신 관리자 로그인* 또는 *가상 머신 사용자 로그인* 역할 중 하나를 할당해야 합니다. VM에 대해 *소유자* 또는 *기여자* 역할이 할당된 Azure 사용자는 SSH를 통해 VM에 로그인하는 권한을 자동으로 갖지 않습니다.

다음 예제에서는 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)를 사용하여 현재 Azure 사용자의 VM에 대한 *가상 머신 관리자 로그인* 역할을 할당합니다. 활성 Azure 계정의 사용자 이름은 [az account show](/cli/azure/account#az-account-show)를 사용하여 가져옵니다. 또한 *범위*는 [az vm show](/cli/azure/vm#az-vm-show)를 사용하여 이전 단계에서 만든 VM으로 설정됩니다. 또한 리소스 그룹이나 구독 수준에서 범위를 할당할 수 있고 정상 RBAC 상속 사용 권한이 적용됩니다. 자세한 내용은 [역할 기반 액세스 제어](../../role-based-access-control/overview.md)를 참조하세요.

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> AAD 도메인과 로그온 사용자 이름 도메인이 일치하지 않으면 *--assignee*의 사용자 이름 외에도 *--assignee-object-id*가 있는 사용자 계정의 개체 ID를 지정해야 합니다. [az ad user list](/cli/azure/ad/user#az-ad-user-list)를 사용하여 사용자 계정의 개체 ID를 가져올 수 있습니다.

RBAC를 사용하여 Azure 구독 리소스에 대한 액세스 권한을 관리하는 방법에 대한 자세한 내용은 [Azure CLI](../../role-based-access-control/role-assignments-cli.md), [Azure Portal](../../role-based-access-control/role-assignments-portal.md) 또는 [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md) 사용을 참조하세요.

Linux 가상 머신에 로그인하는 특정 사용자에 대해 다단계 인증을 요구하도록 Azure AD를 구성할 수도 있습니다. 자세한 내용은 [클라우드에서 Azure Multi-Factor Authentication 시작](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)을 참조하세요.

## <a name="log-in-to-the-linux-virtual-machine"></a>Linux 가상 머신에 로그인

먼저 [az vm show](/cli/azure/vm#az-vm-show)를 사용하여 VM의 공용 IP 주소를 봅니다.

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Azure AD 자격 증명을 사용하여 Azure Linux 가상 머신에 로그인합니다. `-l` 매개 변수를 사용하면 고유한 Azure AD 계정 주소를 지정할 수 있습니다. 예제 계정을 사용자 고유의 계정으로 바꿉니다. 계정 주소는 모두 소문자로 입력해야 합니다. 예제 IP 주소를 이전 명령에서 VM의 공용 IP 주소로 바꿉니다.

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

[https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)에서 일회용 코드를 사용하여 Azure AD에 로그인하라는 메시지가 표시됩니다. 일회성 사용 코드를 복사 하 여 장치 로그인 페이지에 붙여 넣습니다.

메시지가 표시되면 로그인 페이지에서 Azure AD 로그인 자격 증명을 입력합니다. 

성공적으로 인증 되 면 웹 브라우저에 다음 메시지가 표시 됩니다. `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

브라우저 창을 닫고, SSH 프롬프트를 돌아가서, **Enter** 키를 누릅니다. 

이제 할당된 대로 *VM 사용자* 또는 *VM 관리자*와 같은 역할 권한이 있는 Azure Linux 가상 머신에 로그인했습니다. 사용자 계정에 *가상 컴퓨터 관리자 로그인* 역할이 할당 된 경우 `sudo`를 사용 하 여 루트 권한이 필요한 명령을 실행할 수 있습니다.

## <a name="sudo-and-aad-login"></a>Sudo 및 AAD 로그인

sudo를 처음 실행하면 두 번째에는 인증이 요청됩니다. sudo를 실행하기 위해 다시 인증하고 싶지 않다면 sudoer 파일(`/etc/sudoers.d/aad_admins`)을 편집하고 다음 줄로 교체할 수 있습니다.

```bash
%aad_admins ALL=(ALL) ALL
```
다음 줄로 바꿉니다.

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>로그인 문제 해결

Azure AD 자격 증명을 사용하여 SSH하려고 할 때 발생하는 몇 가지 일반적인 오류에는 RBAC 역할이 할당되지 않거나 로그인하라는 프롬프트가 반복되는 문제가 있습니다. 이러한 문제를 해결하려면 다음 섹션을 사용합니다.

### <a name="access-denied-rbac-role-not-assigned"></a>액세스 거부: RBAC 역할 할당되지 않음

SSH 프롬프트에서 다음 오류를 표시하는 경우 사용자에게 *가상 머신 관리자 로그인* 또는 *가상 머신 사용자 로그인* 역할을 부여한 VM에 대한 RBAC 정책을 구성했는지 확인합니다.

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>SSH 로그인 프롬프트 반복

웹 브라우저에서 인증 단계를 성공적으로 완료한 경우 즉시 새 코드를 사용하여 다시 로그인하라는 메시지가 표시될 수 있습니다. 이 오류는 일반적으로 SSH 프롬프트에서 지정한 로그인 이름과 Azure AD에 로그인한 계정 간의 불일치로 인해 발생합니다. 이 문제를 수정하려면:

- SSH 프롬프트에 지정한 로그인 이름이 올바른지 확인합니다. 로그인 이름의 오타로 인해 SSH 프롬프트에서 지정한 로그인 이름과 Azure AD에 로그인한 계정 간의 불일치가 발생할 수 있습니다. 예를 들어 *azureuser\@contoso.onmicrosoft.com*대신 *azuresuer\@contoso.onmicrosoft.com* 를 입력 했습니다.
- 여러 사용자 계정이 있는 경우 Azure AD에 로그인할 때 브라우저 창에서 다른 사용자 계정을 입력하지 않도록 확인합니다.
- Linux는 대/소문자 구분 운영 체제입니다. 'Azureuser@contoso.onmicrosoft.com'및'azureuser@contoso.onmicrosoft.com' 간의 차이로 인해 불일치가 발생할 수 있습니다. SSH 프롬프트에서 대/소문자 구분이 올바른 UPN을 지정했는지 확인합니다.

### <a name="other-limitations"></a>기타 제한 사항

중첩 된 그룹 또는 역할 할당을 통해 액세스 권한을 상속 하는 사용자는 현재 지원 되지 않습니다. 사용자 또는 그룹에 게 [필요한 역할 할당](#configure-role-assignments-for-the-vm)을 직접 할당 해야 합니다. 예를 들어 관리 그룹 또는 중첩 된 그룹 역할 할당을 사용 하는 경우 사용자가 로그인 할 수 있도록 올바른 권한을 부여 하지 않습니다.

## <a name="preview-feedback"></a>미리 보기 피드백

[Azure AD 피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서 이 미리 보기 기능에 대한 사용자 의견을 공유하거나, 사용에 관한 문제를 보고할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Active Directory에 대한 자세한 내용은 [Azure Active Directory란?](../../active-directory/fundamentals/active-directory-whatis.md)을 참조하세요.
