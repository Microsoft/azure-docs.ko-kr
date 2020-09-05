---
title: Azure Active Directory (미리 보기)를 사용 하 여 Azure에서 Windows 가상 머신에 로그인
description: Azure AD에서 Windows를 실행 하는 Azure VM에 로그인
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42b980f809074c275a5e62098d4162e4cb4340a0
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89259373"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Azure Active Directory 인증 (미리 보기)을 사용 하 여 Azure에서 Windows 가상 머신에 로그인

이제 조직에서는 **Windows Server 2019 Datacenter edition** 또는 **windows 10 1809** 이상을 실행 하는 Azure vm (가상 머신)에 대 한 AD (Azure Active Directory) 인증을 활용할 수 있습니다. Azure AD를 사용 하 여 Vm에 인증 하면 정책을 중앙에서 제어 하 고 적용할 수 있습니다. Azure RBAC (역할 기반 액세스 제어) 및 Azure AD 조건부 액세스와 같은 도구를 사용 하 여 VM에 액세스할 수 있는 사용자를 제어할 수 있습니다. 이 문서에서는 Azure AD 인증을 사용 하도록 Windows Server 2019 VM을 만들고 구성 하는 방법을 보여 줍니다.

> [!NOTE]
> Azure Windows Vm에 대 한 azure AD 로그인은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대 한 자세한 내용은  [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

Azure AD 인증을 사용 하 여 Azure에서 Windows Vm에 로그인 하는 경우 다음과 같은 여러 가지 이점이 있습니다.

- 일반적으로 사용 하는 것과 동일한 페더레이션 또는 관리 되는 Azure AD 자격 증명을 활용 합니다.
- 에서는 더 이상 로컬 관리자 계정을 관리할 필요가 없습니다.
- Azure RBAC를 사용 하면 필요에 따라 Vm에 적절 한 액세스 권한을 부여 하 고 더 이상 필요 하지 않을 때 제거할 수 있습니다.
- VM에 대 한 액세스를 허용 하기 전에 Azure AD 조건부 액세스에서 다음과 같은 추가 요구 사항을 적용할 수 있습니다. 
   - Multi-Factor Authentication
   - 로그인 위험 검사
- VDI 배포에 포함 되는 Azure Windows Vm의 Azure AD 조인을 자동화 하 고 크기를 조정 합니다.

> [!NOTE]
> 이 기능을 사용 하도록 설정 하면 azure의 Windows Vm이 Azure AD에 조인 됩니다. 온-프레미스 AD 또는 Azure AD DS와 같은 다른 도메인에 조인할 수 없습니다. 이 작업을 수행 해야 하는 경우 확장을 제거 하 여 Azure AD 테 넌 트에서 VM의 연결을 끊어야 합니다.

## <a name="requirements"></a>요구 사항

### <a name="supported-azure-regions-and-windows-distributions"></a>지원 되는 Azure 지역 및 Windows 배포

이 기능의 미리 보기 중에는 현재 다음과 같은 Windows 배포가 지원 됩니다.

- Windows Server 2019 Datacenter
- Windows 10 1809 이상

> [!IMPORTANT]
> Azure ad에 가입 된 Vm에 대 한 원격 연결은 Azure AD에 가입 된 Windows 10 Pc 또는 VM과 **동일한** 디렉터리에 조인 된 하이브리드 Azure ad 에서만 허용 됩니다. 

현재 이 기능의 미리 보기 기간 동안 다음과 같은 Azure 지역이 지원됩니다.

- 모든 Azure 글로벌 지역

> [!IMPORTANT]
> 이 미리 보기 기능을 사용 하려면 지원 되는 Windows 배포 및 지원 되는 Azure 지역에만 배포 합니다. 이 기능은 현재 Azure Government 또는 소 버린 클라우드에서 지원 되지 않습니다.

### <a name="network-requirements"></a>네트워크 요구 사항

Azure에서 Windows Vm에 대해 Azure AD 인증을 사용 하도록 설정 하려면 Vm 네트워크 구성에서 TCP 포트 443을 통해 다음 끝점에 대 한 아웃 바운드 액세스를 허용 하는지 확인 해야 합니다.

- https: \/ /enterpriseregistration.windows.net
- https:\//login.microsoftonline.com
- https: \/ /device.login.microsoftonline.com
- https: \/ /pas.windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Azure에서 Windows VM에 대 한 Azure AD 로그인 사용

Azure에서 Windows VM에 대해에서 Azure AD 로그인을 사용 하려면 먼저 Windows VM에 대해 Azure AD 로그인 옵션을 사용 하도록 설정 해야 합니다. 그런 다음 VM에 로그인 할 수 있는 권한이 있는 사용자에 대해 Azure 역할 할당을 구성 해야 합니다.
Windows VM에 대해 Azure AD 로그인을 사용 하도록 설정 하는 방법에는 여러 가지가 있습니다.

- Windows VM을 만들 때 Azure Portal 환경 사용
- Windows VM을 만들 때 **또는 기존 WINDOWS vm에 대 한** Azure Cloud Shell 환경 사용

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Azure Portal 사용 하 여 Azure AD 로그인을 사용 하도록 설정 하는 VM 환경 만들기

Windows Server 2019 Datacenter 또는 Windows 10 1809 이상 VM 이미지에 대해 Azure AD 로그인을 사용 하도록 설정할 수 있습니다. 

Azure AD 로그온을 사용 하 여 Azure에서 Windows Server 2019 Datacenter VM을 만들려면 다음을 수행 합니다. 

1. Vm을 만들 수 있는 권한이 있는 계정으로 [Azure Portal](https://portal.azure.com)에 로그인 하 고 **+ 리소스 만들기**를 선택 합니다.
1. Marketplace 검색 창에서 **Windows Server** 를 입력 합니다.
   1. **Windows server** 를 클릭 하 고 소프트웨어 계획 드롭다운 목록에서 **Windows server 2019 Datacenter** 를 선택 합니다.
   1. **만들기**를 클릭 합니다.
1. "관리" 탭에서 Azure Active Directory 섹션 아래의 **AAD 자격 증명으로 로그인 (미리 보기)** **옵션을 사용**하도록 설정 합니다.
1. Id 섹션의 **시스템 할당 관리 id** 가 **켜기**로 설정 되어 있는지 확인 합니다. Azure AD 자격 증명으로 로그인을 사용 하도록 설정 하면이 작업이 자동으로 수행 됩니다.
1. 가상 컴퓨터를 만드는 과정의 나머지 단계를 진행 합니다. 이 미리 보기 중에 VM에 대 한 관리자 사용자 이름 및 암호를 만들어야 합니다.

![Azure AD 자격 증명을 사용 하 여 로그인 VM 만들기](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Azure AD 자격 증명을 사용 하 여 VM에 로그인 하려면 먼저 아래 섹션 중 하나에 설명 된 대로 VM에 대 한 역할 할당을 구성 해야 합니다.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Azure Cloud Shell 환경을 사용 하 여 Azure AD 로그인 활성화

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 계정에서 사용할 수 있도록 공용 Azure 도구가 Cloud Shell에 사전 설치 및 구성되어 있습니다. 간단하게 복사 단추를 선택하여 코드를 복사하고, Cloud Shell에 붙여넣고, Enter 키를 눌러 실행하면 됩니다. Cloud Shell을 여는 몇 가지 방법이 있습니다.

코드 블록의 오른쪽 위 모서리에서 사용을 선택합니다.
브라우저에서 Cloud Shell을 엽니다.
[Azure Portal](https://portal.azure.com)의 오른쪽 위 모퉁이에 있는 메뉴에서 Cloud Shell 단추를 선택 합니다.

CLI를 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 문서에서는 Azure CLI 버전 2.0.31 이상을 실행 해야 합니다. 버전을 찾으려면 az --version을 실행합니다. 설치 또는 업그레이드 해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)문서를 참조 하세요.

1. [az group create](/cli/azure/group#az-group-create)를 사용하여 리소스 그룹을 만듭니다. 
1. 지원 되는 지역에서 지원 되는 배포를 사용 하 여 [az vm create](/cli/azure/vm#az-vm-create) 를 사용 하 여 vm을 만듭니다. 
1. Azure AD 로그인 VM 확장을 설치 합니다. 

다음 예제에서는 Win2019Datacenter를 사용 하는 myVM 이라는 VM을 southcentralus 지역의 Myvm 이라는 리소스 그룹에 배포 합니다. 다음 예제에서는 필요에 따라 고유한 리소스 그룹 및 VM 이름을 제공할 수 있습니다.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Azure AD 로그인 VM 확장을 설치 하기 전에 가상 머신에서 시스템 할당 관리 id를 사용 하도록 설정 해야 합니다.

VM과 지원 리소스를 만드는 데 몇 분이 걸립니다.

마지막으로, Azure AD 로그인 VM 확장을 설치 하 여 Windows VM에 대 한 Azure AD 로그인을 사용 하도록 설정 합니다. VM 확장은 Azure 가상 머신에서 배포 후 구성 및 Automation 작업을 제공하는 작은 애플리케이션입니다. [Az vm extension](/cli/azure/vm/extension#az-vm-extension-set) set을 사용 하 여 myvm 리소스 그룹에서 myvm 이라는 Vm에 AADLoginForWindows 확장을 설치 합니다.

> [!NOTE]
> 기존 Windows Server 2019 또는 Windows 10 1809 이상 VM에 AADLoginForWindows 확장을 설치 하 여 Azure AD 인증을 사용 하도록 설정할 수 있습니다. AZ CLI의 예는 다음과 같습니다.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

`provisioningState` `Succeeded` 확장이 VM에 설치 되 면의가 표시 됩니다.

## <a name="configure-role-assignments-for-the-vm"></a>VM에 대한 역할 할당 구성

이제 VM을 만들었으므로 VM에 로그인 할 수 있는 사용자를 결정 하는 Azure RBAC 정책을 구성 해야 합니다. VM 로그인에 권한을 부여 하는 데 사용 되는 두 개의 Azure 역할:

- **가상 컴퓨터 관리자 로그인**:이 역할이 할당 된 사용자는 관리자 권한으로 Azure 가상 컴퓨터에 로그인 할 수 있습니다.
- **가상 머신 사용자 로그인**: 이 역할이 할당된 사용자는 일반 사용자 권한으로 Azure 가상 머신에 로그인할 수 있습니다.

> [!NOTE]
> 사용자가 RDP를 통해 VM에 로그인 할 수 있도록 하려면 가상 컴퓨터 관리자 로그인 또는 가상 컴퓨터 사용자 로그인 역할을 할당 해야 합니다. VM에 대 한 소유자 또는 참가자 역할이 할당 된 Azure 사용자에 게는 RDP를 통해 VM에 로그인 할 수 있는 권한이 자동으로 부여 되지 않습니다. 가상 컴퓨터를 제어 하는 사용자 집합과 가상 컴퓨터에 액세스할 수 있는 사용자 집합 간에 감사 된 분리를 제공 하기 위한 것입니다.

VM에 대 한 역할 할당을 구성 하는 방법에는 여러 가지가 있습니다.

- Azure AD 포털 환경 사용
- Azure Cloud Shell 환경 사용

### <a name="using-azure-ad-portal-experience"></a>Azure AD 포털 환경 사용

Azure AD 사용 Windows Server 2019 Datacenter Vm에 대 한 역할 할당을 구성 하려면:

1. 특정 가상 컴퓨터 개요 페이지로 이동 합니다.
1. 메뉴 옵션에서 **액세스 제어 (IAM)** 를 선택 합니다.
1. **추가**, **역할 할당 추가** 를 선택 하 여 역할 할당 추가 창을 엽니다.
1. **역할** 드롭다운 목록에서 **가상 머신 관리자 로그인** 또는 **가상 머신 사용자 로그인**과 같은 역할을 선택 합니다.
1. **선택** 필드에서 사용자, 그룹, 서비스 주체 또는 관리 id를 선택 합니다. 목록에 보안 주체가 보이지 않으면 **선택** 상자에 직접 입력하여 표시 이름, 이메일 주소 및 개체 식별자에 대한 디렉터리를 검색할 수 있습니다.
1. **저장**을 선택 하 여 역할을 할당 합니다.

몇 분이 지나면 선택한 범위에서 보안 주체에 역할이 할당됩니다.

![VM에 액세스할 사용자에 게 역할을 할당 합니다.](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Azure Cloud Shell 환경 사용

다음 예제에서는 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)를 사용하여 현재 Azure 사용자의 VM에 대한 가상 머신 관리자 로그인 역할을 할당합니다. 활성 Azure 계정의 사용자 이름은 [az account show](/cli/azure/account#az-account-show)를 사용하여 가져옵니다. 또한 범위는 [az vm show](/cli/azure/vm#az-vm-show)를 사용하여 이전 단계에서 만든 VM으로 설정됩니다. 범위는 리소스 그룹 또는 구독 수준에서 할당 될 수도 있으며, 일반 Azure RBAC 상속 권한이 적용 됩니다. 자세한 내용은 [Azure Active Directory 인증을 사용 하 여 Azure에서 Linux 가상 머신에 로그인](../../virtual-machines/linux/login-using-aad.md)을 참조 하세요.

```   AzureCLI
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> AAD 도메인 및 로그온 사용자 이름 도메인이 일치 하지 않는 경우의 사용자 이름 뿐만 아니라 사용자 계정에 대 한 개체 ID를 지정 해야 합니다 `--assignee-object-id` `--assignee` . [az ad user list](/cli/azure/ad/user#az-ad-user-list)를 사용하여 사용자 계정의 개체 ID를 가져올 수 있습니다.

Azure RBAC를 사용 하 여 Azure 구독 리소스에 대 한 액세스를 관리 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure CLI를 사용 하 여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-cli.md)
- [Azure Portal을 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-portal.md)
- [Azure PowerShell를 사용 하 여 Azure 역할 할당을 추가 하거나 제거](../../role-based-access-control/role-assignments-powershell.md)합니다.

## <a name="using-conditional-access"></a>조건부 액세스 사용

Azure AD 로그인을 사용 하도록 설정 된 Azure에서 Windows Vm에 대 한 액세스 권한을 부여 하기 전에 multi-factor authentication 또는 사용자 로그인 위험 확인 등의 조건부 액세스 정책을 적용할 수 있습니다. 조건부 액세스 정책을 적용 하려면 클라우드 앱 또는 작업 할당 옵션에서 "Azure Windows VM 로그인" 앱을 선택 하 고, 로그인 위험을 조건으로 사용 하거나, 권한 부여 액세스 제어로 multi-factor authentication을 사용 해야 합니다. 

> [!NOTE]
> "Azure Windows VM 로그인" 앱에 대 한 액세스를 요청 하는 권한 부여 액세스 제어로 "multi-factor authentication 필요"를 사용 하는 경우 Azure에서 대상 Windows VM에 대 한 RDP 세션을 시작 하는 클라이언트의 일부로 multi-factor authentication 클레임을 제공 해야 합니다. Windows 10 클라이언트에서이를 수행 하는 유일한 방법은 비즈니스용 Windows Hello PIN 또는 RDP 클라이언트와의 생체 인식 인증을 사용 하는 것입니다. 생체 인식 인증에 대 한 지원이 Windows 10 버전 1809의 RDP 클라이언트에 추가 되었습니다. 비즈니스용 Windows Hello 인증을 사용 하는 원격 데스크톱은 인증서 신뢰 모델을 사용 하 고 현재 키 신뢰 모델에 사용할 수 없는 배포에 대해서만 사용할 수 있습니다.

> [!WARNING]
> 사용자 단위 사용/적용 Azure Multi-Factor Authentication은 VM 로그인에 대해 지원 되지 않습니다.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Azure AD 자격 증명을 사용 하 여 Windows VM에 로그인

> [!IMPORTANT]
> Azure ad에 가입 된 Vm에 대 한 원격 연결은 Azure AD가 등록 된 Windows 10 Pc (최소 필요한 빌드는 20H1) 또는 Azure AD 조인 또는 하이브리드 Azure AD가 VM과 **동일한** 디렉터리에 조인 된 경우에만 허용 됩니다. 또한 Azure AD 자격 증명을 사용 하는 RDP의 경우 사용자는 두 개의 Azure 역할, 가상 컴퓨터 관리자 로그인 또는 가상 컴퓨터 사용자 로그인 중 하나에 속해야 합니다. Azure AD에서 등록 된 Windows 10 PC를 사용 하는 경우 AzureAD\UPN 형식으로 자격 증명을 입력 해야 합니다 (예: AzureAD\john@contoso.com ). 지금은 AADLoginForWindows 확장을 사용 하 여 Azure Active Directory 인증을 사용 하 여 로그인 하는 데 Azure 방호를 사용할 수 없습니다. 직접 RDP만 지원 됩니다.

Azure AD를 사용 하 여 Windows Server 2019 가상 컴퓨터에 로그인 하려면: 

1. Azure AD 로그온을 사용 하도록 설정 된 가상 컴퓨터의 개요 페이지로 이동 합니다.
1. **연결** 을 선택 하 여 가상 머신에 연결 블레이드를 엽니다.
1. **RDP 파일 다운로드**를 선택합니다.
1. **열기** 를 선택 하 여 원격 데스크톱 연결 클라이언트를 시작 합니다.
1. **연결** 을 선택 하 여 Windows 로그온 대화 상자를 시작 합니다.
1. Azure AD 자격 증명을 사용 하 여 로그온 합니다.

이제 VM 사용자 또는 VM 관리자와 같이 할당 된 역할 권한을 사용 하 여 Windows Server 2019 Azure virtual machine에 로그인 합니다. 

> [!NOTE]
> 를 저장할 수 있습니다. Azure Portal에서 가상 컴퓨터 개요 페이지로 이동 하 여 연결 옵션을 사용 하는 대신 가상 컴퓨터에 대 한 원격 데스크톱 연결을 시작 하려면 컴퓨터의 로컬 RDP 파일입니다.

## <a name="troubleshoot"></a>문제 해결

### <a name="troubleshoot-deployment-issues"></a>배포 문제 해결

VM이 Azure AD 조인 프로세스를 완료 하려면 AADLoginForWindows 확장을 성공적으로 설치 해야 합니다. VM 확장이 올바르게 설치 되지 않으면 다음 단계를 수행 합니다.

1. 로컬 관리자 계정을 사용 하 여 VM에 RDP 하 고 아래에서 CommandExecuti'n를 검사 합니다.  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > 초기 실패 후 확장이 다시 시작 되 면 배포 오류가 발생 한 로그가 CommandExecution_YYYYMMDDHHMMSSSSS로 저장 됩니다. "
1. VM에서 명령 프롬프트를 열고 Azure 호스트에서 실행 되는 Instance Metadata Service (IMDS) 끝점에 대해 이러한 쿼리가 반환 되는지 확인 합니다.

   | 실행할 명령 | 예상 출력 |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Azure VM에 대 한 올바른 정보 |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Azure 구독과 연결 된 유효한 테 넌 트 ID |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | 이 VM에 할당 된 관리 id에 대해 Azure Active Directory에서 발급 한 유효한 액세스 토큰 |

   > [!NOTE]
   > 액세스 토큰은와 같은 도구를 사용 하 여 디코딩할 수 있습니다 [http://calebb.net/](http://calebb.net/) . 액세스 토큰의 "appid"가 VM에 할당 된 관리 id와 일치 하는지 확인 합니다.

1. 명령줄을 사용 하 여 VM에서 필요한 끝점에 액세스할 수 있는지 확인 합니다.
   
   - 말아 https: \/ /login.microsoftonline.com/-D –
   - 말아 https: \/ /login.microsoftonline.com/ `<TenantID>` /-D –

   > [!NOTE]
   > `<TenantID>`을 azure 구독과 연결 된 AZURE AD 테 넌 트 ID로 바꿉니다.

   - 말아 https: \/ /enterpriseregistration.windows.net/-D-
   - 말아 https: \/ /device.login.microsoftonline.com/-D-
   - 말아 https: \/ /pas.windows.net/-D-

1. 장치 상태는를 실행 하 여 볼 수 있습니다 `dsregcmd /status` . 목표는 장치 상태를로 표시 하는 것입니다 `AzureAdJoined : YES` .

   > [!NOTE]
   > Azure AD 조인 작업은 사용자 장치 Registration\Admin 로그 아래의 이벤트 뷰어에서 캡처됩니다.

AADLoginForWindows extension이 특정 오류 코드와 함께 실패 하는 경우 다음 단계를 수행할 수 있습니다.

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>문제 1: AADLoginForWindows 확장을 설치 하지 못했습니다 (터미널 오류 코드 ' 1007 ' 및 종료 코드:-2145648574).

확장에서 Azure AD 테 넌 트 정보를 쿼리할 수 없기 때문에이 종료 코드는 DSREG_E_MSI_TENANTID_UNAVAILABLE로 변환 됩니다.

1. Azure VM이 Instance Metadata Service에서 TenantID를 검색할 수 있는지 확인 합니다.

   - 로컬 관리자로 VM에 RDP를 실행 하 고 VM의 관리자 권한 명령줄에서이 명령을 실행 하 여 끝점이 유효한 테 넌 트 ID를 반환 하는지 확인 합니다.
      
      - 말아-H 메타 데이터: true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. VM 관리자가 AADLoginForWindows 확장을 설치 하려고 하지만, 시스템 할당 관리 id가 VM을 먼저 사용 하도록 설정 하지 않았습니다. VM의 Id 블레이드로 이동 합니다. 시스템 할당 됨 탭에서 상태가 켜기로 설정 되어 있는지 확인 합니다.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>문제 2: AADLoginForWindows 확장을 설치 하지 못하고 종료 코드:-2145648607

이 종료 코드는 확장이 끝점에 연결할 수 없기 때문에 DSREG_AUTOJOIN_DISC_FAILED로 변환 됩니다 `https://enterpriseregistration.windows.net` .

1. 명령줄을 사용 하 여 VM에서 필요한 끝점에 액세스할 수 있는지 확인 합니다.

   - 말아 https: \/ /login.microsoftonline.com/-D –
   - 말아 https: \/ /login.microsoftonline.com/ `<TenantID>` /-D –
   
   > [!NOTE]
   > `<TenantID>`을 azure 구독과 연결 된 AZURE AD 테 넌 트 ID로 바꿉니다. 테 넌 트 ID를 찾아야 하는 경우 계정 이름 위로 마우스를 이동 하 여 디렉터리/테 넌 트 ID를 가져오거나 Azure Portal에서 디렉터리 ID > Azure Active Directory > 속성을 선택할 수 있습니다.

   - 말아 https: \/ /enterpriseregistration.windows.net/-D-
   - 말아 https: \/ /device.login.microsoftonline.com/-D-
   - 말아 https: \/ /pas.windows.net/-D-

1. "호스트를 확인할 수 없습니다."와 함께 명령이 실패 하는 경우 `<URL>` 이 명령을 실행 하 여 VM에서 사용 중인 DNS 서버를 확인 하십시오.
   
   `nslookup <URL>`

   > [!NOTE] 
   > 를 `<URL>` 끝점에서 사용 하는 정규화 된 도메인 이름 (예: "login.microsoftonline.com")으로 바꿉니다.

1. 다음으로, 공용 DNS 서버를 지정 하면 명령이 성공 하도록 허용 하는 경우를 참조 하세요.

   `nslookup <URL> 208.67.222.222`

1. 필요한 경우 Azure VM이 속한 네트워크 보안 그룹에 할당 된 DNS 서버를 변경 합니다.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>문제 3: AADLoginForWindows 확장을 설치 하지 못함 종료 코드: 51

종료 코드 51은 "이 확장이 VM의 운영 체제에서 지원 되지 않습니다."로 변환 됩니다.

공개 미리 보기에서 AADLoginForWindows 확장은 Windows Server 2019 또는 Windows 10 (빌드 1809 이상)에만 설치할 수 있습니다. Windows 버전이 지원 되는지 확인 합니다. Windows 빌드를 지원 하지 않는 경우 VM 확장을 제거 합니다.

### <a name="troubleshoot-sign-in-issues"></a>로그인 문제 해결

Azure AD 자격 증명을 사용 하 여 RDP를 시도할 때 발생 하는 몇 가지 일반적인 오류에는 할당 된 Azure 역할, 권한 없는 클라이언트 또는 2FA 로그인 방법이 필요 하지 않습니다. 다음 정보를 사용 하 여 이러한 문제를 해결할 수 있습니다.

장치 및 SSO 상태는를 실행 하 여 볼 수 있습니다 `dsregcmd /status` . 목표는 장치 상태를 표시 하 고 표시 하는 것입니다 `AzureAdJoined : YES` `SSO State` `AzureAdPrt : YES` .

또한 Azure AD 계정을 사용 하는 RDP 로그인은 AAD\Operational 이벤트 로그 아래의 이벤트 뷰어에서 캡처됩니다.

#### <a name="azure-role-not-assigned"></a>Azure 역할이 할당 되지 않음

VM에 대 한 원격 데스크톱 연결을 시작할 때 다음과 같은 오류 메시지가 표시 되는 경우: 

- 계정이이 장치를 사용할 수 없도록 구성 되어 있습니다. 자세한 내용은 시스템 관리자에 게 문의 하십시오.

![계정이이 장치를 사용할 수 없도록 구성 되어 있습니다.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

사용자에 게 가상 머신 관리자 로그인 또는 가상 머신 사용자 로그인 역할을 부여 하는 VM에 대 한 [AZURE RBAC 정책을 구성](../../virtual-machines/linux/login-using-aad.md) 했는지 확인 합니다.
 
#### <a name="unauthorized-client"></a>권한 없는 클라이언트

VM에 대 한 원격 데스크톱 연결을 시작할 때 다음과 같은 오류 메시지가 표시 되는 경우: 

- 자격 증명이 작동 하지 않음

![자격 증명이 작동 하지 않음](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

원격 데스크톱 연결을 시작 하는 데 사용 하는 Windows 10 PC가 Azure AD에 가입 되어 있거나, VM이 조인 되는 동일한 Azure AD 디렉터리에 연결 된 하이브리드 Azure AD 인지 확인 합니다. 장치 id에 대 한 자세한 내용은 [장치 Id 정의](./overview.md)문서를 참조 하세요.

> [!NOTE]
> Windows 10 Build 20H1은 VM에 대 한 RDP 연결을 시작 하기 위해 Azure AD에 등록 된 PC에 대 한 지원을 추가 했습니다. VM에 대 한 연결을 시작 하기 위해 RDP 클라이언트로 등록 된 (Azure AD 조인 또는 하이브리드 Azure AD 조인 된) PC를 사용 하는 경우 AzureAD\UPn 형식으로 자격 증명을 입력 해야 합니다 (예: AzureAD\john@contoso.com ).

또한 Azure AD 조인이 완료 된 후 AADLoginForWindows 확장이 제거 되지 않았는지 확인 합니다.
 
#### <a name="mfa-sign-in-method-required"></a>MFA 로그인 방법이 필요 합니다.

VM에 대 한 원격 데스크톱 연결을 시작할 때 다음과 같은 오류 메시지가 표시 되는 경우: 

- 사용 하려는 로그인 방법은 허용 되지 않습니다. 다른 로그인 방법을 시도 하거나 시스템 관리자에 게 문의 하십시오.

![사용 하려는 로그인 방법은 허용 되지 않습니다.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

리소스에 액세스 하기 전에 MFA (multi-factor authentication)를 요구 하는 조건부 액세스 정책을 구성한 경우, VM에 대 한 원격 데스크톱 연결을 시작 하는 Windows 10 PC가 Windows Hello와 같은 강력한 인증 방법을 사용 하 여 로그인 하도록 해야 합니다. 원격 데스크톱 연결에 강력한 인증 방법을 사용 하지 않는 경우 이전 오류가 표시 됩니다.

비즈니스용 Windows Hello를 배포 하지 않은 경우 및 현재이 옵션을 선택 하지 않은 경우 MFA를 요구 하는 클라우드 앱 목록에서 "Azure Windows VM 로그인" 앱을 제외 하는 조건부 액세스 정책을 구성 하 여 MFA 요구 사항을 제외할 수 있습니다. 비즈니스용 Windows Hello에 대 한 자세한 내용은 [비즈니스용 Windows Hello 개요](/windows/security/identity-protection/hello-for-business/hello-identity-verification)를 참조 하세요.

> [!NOTE]
> Windows 10에서 RDP를 사용 하는 비즈니스용 windows Hello PIN 인증을 여러 버전에 대해 지원 했지만 RDP를 사용한 생체 인식 인증에 대 한 지원이 Windows 10 버전 1809에 추가 되었습니다. RDP 중 비즈니스용 Windows Hello 인증을 사용 하는 것은 인증서 신뢰 모델을 사용 하 고 현재 키 신뢰 모델에 사용할 수 없는 배포에만 사용할 수 있습니다.
 
## <a name="preview-feedback"></a>미리 보기 피드백

이 미리 보기 기능에 대 한 피드백을 공유 하거나 [AZURE AD 피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서이를 사용 하 여 문제를 보고 하세요.

## <a name="next-steps"></a>다음 단계

Azure Active Directory에 대한 자세한 내용은 [Azure Active Directory란?](../fundamentals/active-directory-whatis.md)을 참조하세요.