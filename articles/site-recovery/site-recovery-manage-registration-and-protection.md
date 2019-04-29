---
title: 서버 제거 및 보호 사용 안 함 | Microsoft Docs
description: 이 문서에서는 사이트 복구 자격 증명 모음에서 서버 등록을 취소하고 가상 머신 및 물리적 서버의 보호를 사용하지 않도록 설정하는 방법을 설명합니다.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: c22acb1ae82e5c1e781598e8545c7f1625cc1c09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61277164"
---
# <a name="remove-servers-and-disable-protection"></a>서버 제거 및 보호 사용 안 함

이 문서에는 Recovery Services 자격 증명 모음에서 서버의 등록을 취소하는 방법 및 Site Recovery를 통해 보호되는 컴퓨터에 대한 보호를 사용하지 않도록 설정하는 방법을 설명합니다.


## <a name="unregister-a--configuration-server"></a>구성 서버 등록 취소

VMware VM 또는 Windows/Linux 물리적 서버를 Azure에 복제하는 경우 다음과 같이 자격 증명 모음에서 연결되지 않은 구성 서버를 등록 취소할 수 있습니다.

1. [가상 머신의 보호를 사용하지 않도록 설정합니다](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. 복제본 정책을 [분리 또는 삭제합니다](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).
3. [구성 서버 삭제](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>VMM 서버 등록 취소

1. 제거할 VMM에서 클라우드에서 가상 머신 복제를 중지합니다.
2. 삭제할 VMM 서버에서 클라우드에 사용된 모든 네트워크 매핑을 삭제합니다. **사이트 복구 인프라** > **System Center VMM** > **네트워크 매핑**에서 네트워크 매핑을 마우스 오른쪽 단추로 클릭하고 > **삭제**를 클릭합니다.
3. VMM 서버의 ID를 메모해 둡니다.
4. 제거할 VMM 서버의 클라우드에서 복제 정책을 연결 해제합니다.  **사이트 복구 인프라** > **System Center VMM** >  **복제 정책**에서 연결된 정책을 두 번 클릭합니다. 클라우드를 마우스 오른쪽 단추로 클릭하고 > **연결 해제**를 클릭합니다.
5. VMM 서버 또는 활성 노드를 삭제합니다. **사이트 복구 인프라** > **System Center VMM** > **VMM 서버**에서 서버를 마우스 오른쪽 단추로 클릭하고 > **삭제**를 클릭합니다.
6. VMM 서버가 연결 분리 상태인 경우 VMM 서버에서 [정리 스크립트](https://aka.ms/asr-cleanup-script-vmm)를 다운로드하여 실행합니다. 기본(LocalMachine) 범위에 대한 실행 정책을 변경하려면 **관리자 권한으로 실행** 옵션으로 PowerShell을 엽니다. 스크립트에서 제거할 VMM 서버의 ID를 지정합니다. 이 스크립트는 서버에서 등록 및 클라우드 페어링을 제거합니다.
5. 모든 보조 VMM 서버에서 정리 스크립트를 실행합니다.
6. 공급자가 설치된 다른 모든 수동 VMM 클러스터 노드에서 정리 스크립트를 실행합니다.
7. VMM 서버에서 공급자를 수동으로 제거합니다. 클러스터가 있는 경우 모든 노드에서 제거합니다.
8. 가상 머신을 Azure에 복제한 경우 삭제된 클라우드의 Hyper-V 호스트에서 Microsoft Recovery Services 에이전트를 제거해야 합니다.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Hyper-V 사이트에서 Hyper-V 호스트 등록 취소

VMM에 의해 관리되지 않는 Hyper-V 호스트가 Hyper-V 사이트로 수집됩니다. 다음과 같이 Hyper-V 사이트에서 호스트를 제거합니다.

1. 호스트에 있는 Hyper-V VM에 대한 복제를 사용하지 않도록 설정합니다.
2. Hyper-V 사이트에 대한 정책을 연결 해제합니다. **사이트 복구 인프라** > **Hyper-V 사이트** >  **복제 정책**에서 연결된 정책을 두 번 클릭합니다. 사이트를 마우스 오른쪽 단추로 클릭하고 > **연결 해제**를 클릭합니다.
3. Hyper-V 호스트를 삭제합니다. **사이트 복구 인프라** > **Hyper-V 사이트** > **Hyper-V 호스트**에서 서버를 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.
4. 모든 호스트가 제거되었으면 Hyper-V 사이트를 삭제합니다. **사이트 복구 인프라** > **Hyper-V 사이트** > **Hyper-V 사이트**에서 사이트를 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.
5. Hyper-V 호스트가 **연결 분리** 상태인 경우 제거한 각각의 Hyper-V 호스트에서 다음 스크립트를 실행합니다. 스크립트는 서버에서 설정을 정리하고 자격 증명 모음에서 설정을 등록 취소합니다.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>VMware VM 또는 물리적 서버(VMware에서 Azure로)에 대해 보호 사용 안 함

1. **보호된 항목** > **복제된 항목**에서 컴퓨터를 마우스 오른쪽 단추로 클릭한 후 **복제 사용 안 함**을 클릭합니다.
2. **복제 사용 안 함** 페이지에서 다음 옵션 중 하나를 선택합니다.
    - **복제 사용 안 함 및 제거(권장)** - 이 옵션을 사용하면 Azure Site Recovery에서 복제된 항목을 제거하고 컴퓨터에 대한 복제가 중지됩니다. 구성 서버에서 복제 구성을 정리하고 이 보호되는 서버에 대한 Site Recovery 청구가 중지됩니다.  이 옵션은 구성 서버가 연결된 상태에 있는 경우에만 사용할 수 있습니다.
    - **제거** - 이 옵션은 원본 환경이 삭제되었거나 액세스할 수 없는 경우(연결 안 됨)에만 사용합니다. 이 옵션은 Azure Site Recovery에서 복제된 항목을 제거합니다(청구 중지). 구성 서버에서의 복제 구성은 정리되지 **않습니다**. 

> [!NOTE]
> 두 옵션 모두에서 모바일 서비스는 보호되는 서버에서 제거되지 않으므로 수동으로 제거해야 합니다. 동일한 구성 서버를 사용하여 서버를 보호하려는 경우 모바일 서비스 제거를 생략할 수 있습니다.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Hyper-V 가상 머신에 대해 보호 사용 안 함(Hyper-V에서 Azure로)

> [!NOTE]
> VMM 서버 없이 Hyper-V VM을 Azure에 복제하는 경우 이 절차를 사용합니다. **System Center VMM에서 Azure로** 시나리오를 사용하여 가상 머신을 복제하는 경우 System Center VMM에서 Azure로 시나리오를 사용하여 복제하는 Hyper-V 가상 머신에 보호 사용 안 함의 지침을 따릅니다.

1. **보호된 항목** > **복제된 항목**에서 컴퓨터를 마우스 오른쪽 단추로 클릭한 후 **복제 사용 안 함**을 클릭합니다.
2. **복제 사용 안 함**에서 다음 옵션을 선택할 수 있습니다.
   - **복제 사용 안 함 및 제거(권장)** - 이 옵션을 사용하면 Azure Site Recovery에서 복제된 항목을 제거하고 컴퓨터에 대한 복제가 중지됩니다. 온-프레미스 가상 머신에서 복제 구성을 정리하고 이 보호되는 서버에 대한 Site Recovery 청구가 중지됩니다. 
   - **제거** - 이 옵션은 원본 환경이 삭제되었거나 액세스할 수 없는 경우(연결 안 됨)에만 사용합니다. 이 옵션은 Azure Site Recovery에서 복제된 항목을 제거합니다(청구 중지). 온-프레미스 가상 머신에서 복제 구성이 정리되지 **않습니다**. 

     > [!NOTE]
     > **제거** 옵션을 선택한 경우 다음 스크립트 집합을 실행하여 온-프레미스 Hyper-V 서버의 복제 설정을 정리합니다.
1. 원본 Hyper-V 호스트 서버에서 가상 머신에 대한 복제를 제거합니다. SQLVM1을 가상 머신 이름으로 바꾸고 관리 권한이 있는 PowerShell에서 스크립트를 실행합니다.

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>System Center VMM에서 Azure로 시나리오를 사용하여 Azure에 복제하는 Hyper-V 가상 컴퓨터에 보호 사용 안 함

1. **보호된 항목** > **복제된 항목**에서 컴퓨터를 마우스 오른쪽 단추로 클릭한 후 **복제 사용 안 함**을 클릭합니다.
2. **복제 사용 안 함** 페이지에서 다음 옵션 중 하나를 선택합니다.

   - **복제 사용 안 함 및 제거(권장)** - 이 옵션을 사용하면 Azure Site Recovery에서 복제된 항목을 제거하고 컴퓨터에 대한 복제가 중지됩니다. 온-프레미스 가상 머신에서 복제 구성을 정리하고 이 보호되는 서버에 대한 Site Recovery 청구가 중지됩니다. 
   - **제거** - 이 옵션은 원본 환경이 삭제되었거나 액세스할 수 없는 경우(연결 안 됨)에만 사용합니다. 이 옵션은 Azure Site Recovery에서 복제된 항목을 제거합니다(청구 중지). 온-프레미스 가상 머신에서 복제 구성이 정리되지 **않습니다**. 

     > [!NOTE]
     > **제거** 옵션을 선택한 경우 다음 스크립트를 실행하여 온-프레미스 VMM 서버의 복제 설정을 정리합니다.
3. VMM 콘솔에서 PowerShell을 사용하여(관리자 권한 필요) 원본 VMM 서버에서 이 스크립트를 실행합니다. 자리 표시자 **SQLVM1**을 가상 머신의 이름으로 바꿉니다.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. 위의 단계는 VMM 서버에서 복제 설정을 지웁니다. Hyper-V 호스트 서버에서 실행하는 가상 컴퓨터의 복제를 중지하려면 이 스크립트를 실행합니다. SQLVM1을 가상 컴퓨터의 이름으로 바꾸고 host01.contoso.com을 Hyper-V 호스트 서버의 이름으로 바꿉니다.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>System Center VMM에서 Azure로 시나리오를 사용하여 보조 VMM에 복제하는 Hyper-V 가상 머신에 보호 사용 안 함

1. **보호된 항목** > **복제된 항목**에서 컴퓨터를 마우스 오른쪽 단추로 클릭한 후 **복제 사용 안 함**을 클릭합니다.
2. **복제 사용 안 함** 페이지에서 다음 옵션 중 하나를 선택합니다.

   - **복제 사용 안 함 및 제거(권장)** - 이 옵션을 사용하면 Azure Site Recovery에서 복제된 항목을 제거하고 컴퓨터에 대한 복제가 중지됩니다. 온-프레미스 가상 머신에서 복제 구성을 정리하고 이 보호되는 서버에 대한 Site Recovery 청구가 중지됩니다. 
   - **제거** - 이 옵션은 원본 환경이 삭제되었거나 액세스할 수 없는 경우(연결 안 됨)에만 사용합니다. 이 옵션은 Azure Site Recovery에서 복제된 항목을 제거합니다(청구 중지). 온-프레미스 가상 머신에서 복제 구성이 정리되지 **않습니다**. 다음 스크립트 집합을 실행하여 온-프레미스 가장 컴퓨터의 복제 설정을 정리합니다.
     > [!NOTE]
     > **제거** 옵션을 선택한 경우 다음 스크립트를 실행하여 온-프레미스 VMM 서버의 복제 설정을 정리합니다.

3. VMM 콘솔에서 PowerShell을 사용하여(관리자 권한 필요) 원본 VMM 서버에서 이 스크립트를 실행합니다. 자리 표시자 **SQLVM1**을 가상 머신의 이름으로 바꿉니다.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. 보조 VMM 서버에서 이 스크립트를 실행하여 보조 가상 머신의 설정을 정리합니다.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. 두 번째 VMM 서버에서 Hyper-V 호스트 서버에서 가상 머신을 새로 고쳐 보조 VM이 VMM 콘솔에서 다시 감지되도록 합니다.
6. 위의 단계는 VMM 서버에서 복제 설정을 정리합니다. 가상 머신에 대한 복제를 중지하려면 주 및 보조 VM에서 다음 스크립트를 실행합니다. SQLVM1을 가상 머신의 이름으로 바꿉니다.

        Remove-VMReplication –VMName “SQLVM1”




