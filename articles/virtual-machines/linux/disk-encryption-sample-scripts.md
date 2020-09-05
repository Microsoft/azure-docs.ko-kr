---
title: Azure Disk Encryption 샘플 스크립트
description: 이 문서는 Linux Vm에 대 한 Microsoft Azure 디스크 암호화에 대 한 부록입니다.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: abf805d24d164ba31daa4d77d6360629632bfcf0
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072709"
---
# <a name="azure-disk-encryption-sample-scripts-for-linux-vms"></a>Linux Vm에 대 한 Azure Disk Encryption 샘플 스크립트

이 문서에서는 미리 암호화 된 Vhd 및 기타 작업을 준비 하기 위한 샘플 스크립트를 제공 합니다.

 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Azure Disk Encryption용 샘플 PowerShell 스크립트 

- **구독에서 암호화된 VM 모두 나열**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **키 자격 증명 모음에서 VM 암호화에 사용된 디스크 암호화 비밀 모두 나열** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a> Azure Disk Encryption 필수 구성 요소 PowerShell 스크립트 사용
Azure Disk Encryption에 대한 필수 구성 요소에 이미 익숙한 경우 [Azure Disk Encryption 필수 구성 요소 PowerShell 스크립트](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )를 사용할 수 있습니다. 이 PowerShell 스크립트 사용의 예는 [VM 암호화 빠른 시작](disk-encryption-powershell-quickstart.md)를 참조하세요. 줄 211에서 시작하는 스크립트의 섹션에서 주석을 제거하여 기존 리소스 그룹의 기존 VM에 대한 모든 디스크를 암호화할 수 있습니다. 

다음 표는 PowerShell 스크립트에서 사용할 수 있는 매개 변수를 보여줍니다. 


|매개 변수|설명|필수?|
|------|------|------|
|$resourceGroupName| KeyVault가 속해 있는 리소스 그룹의 이름입니다.  이 이름을 가진 새 리소스 그룹이 없는 경우 생성됩니다.| True|
|$keyVaultName|암호화 키가 배치된 KeyVault의 이름입니다. 이 이름을 가진 새 자격 증명 모음이 없는 경우 생성됩니다.| True|
|$location|KeyVault의 위치입니다. 암호화할 KeyVault 및 VM이 동일한 위치에 있는지 확인합니다. `Get-AzLocation`을 사용하여 위치 목록을 가져옵니다.|True|
|$subscriptionId|사용할 Azure 구독의 식별자입니다.  구독 ID는 `Get-AzSubscription`을 사용하여 가져올 수 있습니다.|True|
|$aadAppName|KeyVault에 비밀을 쓰는 데 사용할 Azure AD 애플리케이션의 이름입니다. 이 이름을 가진 새 애플리케이션이 없는 경우 생성됩니다. 이 앱이 이미 있는 경우 스크립트에 aadClientSecret 매개 변수를 전달합니다.|거짓|
|$aadClientSecret|이전에 만든 Azure AD 애플리케이션의 클라이언트 비밀입니다.|거짓|
|$keyEncryptionKeyName|KeyVault의 선택적 키 암호화 키의 이름입니다. 이 이름을 가진 새 키가 없는 경우 생성됩니다.|거짓|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Azure AD 앱 없이 VM을 암호화 또는 암호 해독

- [기존 또는 실행 중인 Linux VM에서 디스크 암호화를 사용 하도록 설정](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [실행 중인 Linux VM에서 디스크 암호화 사용 안 함](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - 암호화 사용 안 함은 Linux VM용 데이터 볼륨에서만 허용됩니다.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Azure AD 앱으로 VM을 암호화 또는 암호 해독(이전 릴리스) 
 
- [기존 또는 실행 중인 Linux VM에서 디스크 암호화를 사용 하도록 설정](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [실행 중인 Linux VM에서 디스크 암호화 사용 안 함](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - 암호화 사용 안 함은 Linux VM용 데이터 볼륨에서만 허용됩니다. 


- [미리 암호화된 VHD/스토리지 Blob에서 새로운 암호화된 관리 디스크 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - 미리 암호화된 VHD 및 해당 암호화 설정을 제공하는 새로운 암호화된 관리 디스크 만들기





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>실행 중인 Linux VM에서 OS 드라이브 암호화

### <a name="prerequisites-for-os-disk-encryption"></a>OS 디스크 암호화를 위한 필수 조건

* VM은 [Azure Disk Encryption 지원 되는 운영 체제](disk-encryption-overview.md#supported-vms) 에 나열 된 대로 OS 디스크 암호화와 호환 되는 배포를 사용 해야 합니다. 
* Azure Resource Manager의 Marketplace 이미지에서 VM을 만들어야 합니다.
* 4GB 이상의 RAM이 있는 Azure VM(권장 크기는 7GB)이 있어야 합니다.
* (RHEL 및 CentOS) SELinux를 사용하지 않도록 설정합니다. SELinux를 사용하지 않도록 설정하려면 VM에 대한 [SELinux 사용자 및 관리자 가이드](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux)에서 "4.4.2. SELinux 사용 안 함"을 참조하세요.
* SELinux를 사용하지 않도록 설정한 후 VM을 한 번 이상 다시 부팅합니다.

### <a name="steps"></a>단계
1. 이전에 지정한 배포판 중 하나를 사용하여 VM을 만듭니다.

   CentOS 7.2의 경우 특수 이미지를 통해 OS 디스크 암호화가 지원됩니다. 이 이미지를 사용하려면 VM을 만들 때 SKU로 "7.2n"을 지정합니다.

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. 필요에 따라 VM을 구성합니다. 모든(OS + 데이터) 드라이브를 암호화하려는 경우 데이터 드라이브를 지정하고 /etc/fstab에서 탑재할 수 있어야 합니다.

   > [!NOTE]
   > 블록 디바이스 이름(예: /dev/sdb1)을 지정하는 대신, UUID=...를 사용하여 /etc/fstab에서 데이터 드라이브를 지정합니다. 암호화하는 동안 드라이브의 순서는 VM에서 변경됩니다. VM이 블록 디바이스의 특정 순서에 의존하는 경우 암호화 후 탑재하지 못합니다.

3. SSH 세션에서 로그아웃합니다.

4. OS를 암호화 하려면 암호화를 사용 하도록 설정 하는 경우 볼륨을 **모두** 또는 **os** 로 지정 합니다.

   > [!NOTE]
   > `systemd` 서비스로 실행되지 않는 모든 사용자 공간 프로세스는 `SIGKILL`로 중지됩니다. VM을 다시 부팅합니다. 실행 중인 VM에서 OS 디스크 암호화를 사용할 경우 VM의 가동 중지 시간을 계획하세요.

5. [다음 섹션](#monitoring-os-encryption-progress)의 지침에 따라 암호화 진행 상태를 주기적으로 모니터링합니다.

6. AzVmDiskEncryptionStatus에 "VMRestartPending"가 표시 되 면 해당 VM에 로그인 하거나 포털, PowerShell 또는 CLI를 사용 하 여 VM을 다시 시작 합니다.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   VM을 다시 부팅하기 전에 VM의 [부트 진단](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)을 저장하는 것이 좋습니다.

## <a name="monitoring-os-encryption-progress"></a>OS 암호화 진행 상태 모니터링
OS 암호화 진행 상태를 모니터링하는 방법은 세 가지가 있습니다.

* `Get-AzVmDiskEncryptionStatus` cmdlet을 사용하고 ProgressMessage 필드를 검사합니다.
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Premium 스토리지 지원 VM의 경우 VM이 &quot;OS 디스크 암호화 시작됨&quot;에 도달한 후 약 40~50분이 소요됩니다.

  WALinuxAgent에서 [문제 #388](https://github.com/Azure/WALinuxAgent/issues/388)으로 인해 일부 배포판에서 `OsVolumeEncrypted` 및 `DataVolumesEncrypted`가 `Unknown`으로 표시됩니다. WALinuxAgent 2.1.5 버전 이상에서는 이 문제가 자동으로 수정됩니다. 출력에 `Unknown`이 표시되는 경우 Azure Resource Explorer를 사용하여 디스크 암호화 상태를 확인할 수 있습니다.

  [Azure Resource Explorer](https://resources.azure.com/)로 이동한 후 왼쪽의 선택 패널에서 이 계층 구조를 확장합니다.

  ~~~~
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ~~~~                

  InstanceView에서 아래로 스크롤하여 드라이브의 암호화 상태를 확인합니다.

  ![VM 인스턴스 보기](./media/disk-encryption/vm-instanceview.png)

* [부트 진단](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)을 살펴봅니다. ADE 확장의 메시지에는 `[AzureDiskEncryption]`이라는 접두사가 붙습니다.

* SSH를 통해 VM에 로그온하고 다음에서 확장 로그를 가져옵니다.

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  OS 암호화가 진행 중인 동안에는 VM에 로그온하지 않는 것이 좋습니다. 다른 두 가지 방법이 실패한 경우에만 로그가 복사됩니다.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>사전에 암호화된 Linux VHD 준비
미리 암호화된 VHD에 대한 준비는 배포에 따라 달라질 수 있습니다. Ubuntu 16, openSUSE 13.2 및 CentOS 7 준비에 대한 예제를 참고할 수 있습니다. 

### <a name="ubuntu-16"></a>Ubuntu 16
다음 단계를 수행하여 배포를 설치하는 도중에 암호화를 구성합니다.

1. 디스크를 분할할 때 **암호화된 볼륨 구성**을 선택합니다.

   ![Ubuntu 16.04 설치 - 암호화된 볼륨 구성](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. 암호화되지 않아야 하는 별도의 부트 드라이브를 만듭니다. 루트 드라이브를 암호화합니다.

   ![Ubuntu 16.04 설치 - 암호화할 디바이스 선택](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. 암호를 제공합니다. 키 자격 증명 모음에 업로드한 암호입니다.

   ![Ubuntu 16.04 설치 - 암호 제공](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. 분할을 완료합니다.

   ![Ubuntu 16.04 설치 - 분할 완료](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. VM을 부팅하고 암호를 묻는 메시지가 표시되면 3단계에서 제공한 암호를 사용합니다.

   ![Ubuntu 16.04 설치 - 부팅 시 암호 제공](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. [이 지침](./create-upload-ubuntu.md?toc=/azure/virtual-machines/linux/toc.json)을 사용하여 Azure에 업로드하기 위한 VM을 준비합니다. 마지막 단계(VM 프로비전 해제)를 아직 실행하지 마세요.

다음 단계를 수행하여 Azure로 작업하는 암호화를 구성합니다.

1. 다음 스크립트 내용으로 /usr/local/sbin/azure_crypt_key.sh라는 파일을 만듭니다. Azure에서 사용된 암호 파일 이름이므로 KeyFileName에 주의해야 합니다.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. */etc/crypttab*에서 암호화 구성을 변경합니다. 다음과 같이 표시됩니다.
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. 스크립트에 실행 권한을 추가합니다.
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. 줄을 추가하여 */etc/initramfs-tools/modules*를 편집합니다.
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. initramfs를 업데이트하는 `update-initramfs -u -k all`을 실행하여 `keyscript`를 적용합니다.

7. 이제 VM을 프로비전 해제할 수 있습니다.

   ![Ubuntu 16.04 설치 - update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. 다음 단계를 계속하여 Azure에 VHD를 업로드합니다.

### <a name="opensuse-132"></a>openSUSE 13.2
배포 설치 중에 암호화를 구성하려면 다음 단계를 수행합니다.
1. 디스크를 파티션하는 경우 **볼륨 그룹 암호화**를 선택하고 암호를 입력합니다. 키 자격 증명 모음에 업로드할 암호입니다.

   ![openSUSE 13.2 설치 - 볼륨 그룹 암호화](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. 암호를 사용하여 VM을 부팅합니다.

   ![openSUSE 13.2 설치 - 부팅 시 암호 제공](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. [Azure용 SLES 또는 openSUSE 가상 머신 준비](./suse-create-upload-vhd.md?toc=/azure/virtual-machines/linux/toc.json#prepare-opensuse-131)의 지침에 따라 Azure에 업로드할 VM을 준비합니다. 마지막 단계(VM 프로비전 해제)를 아직 실행하지 마세요.

Azure로 작업할 암호화를 구성하려면 다음 단계를 수행합니다.
1. /etc/dracut.conf를 편집하고 다음 줄을 추가합니다.
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. /usr/lib/dracut/modules.d/90crypt/module-setup.sh 파일의 끝에서 다음 줄을 주석 처리합니다.
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh 파일의 시작 부분에 다음 줄을 추가합니다.
   ```bash
    DRACUT_SYSTEMD=0
   ```
   다음 항목을 그 아래 항목으로 바꿉니다.
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   다음과 같이 변경합니다.
   ```bash
    if [ 1 ]; then
   ```
4. Ask.sh를 편집 하 고 "# Open LUKS device"에 추가 합니다.

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. `/usr/sbin/dracut -f -v`를 실행하여 initrd를 업데이트합니다.

6. 이제 VM을 프로비전 해제하고 VHD를 Azure에 업로드할 수 있습니다.

### <a name="centos-7-and-rhel-7"></a>CentOS 7 및 RHEL 7

배포 설치 중에 암호화를 구성하려면 다음 단계를 수행합니다.
1. 디스크를 분할할 때 **내 데이터 암호화**를 선택합니다.

   ![CentOS 7 설치 - 설치 대상](./media/disk-encryption/centos-encrypt-fig1.png)

2. 루트 파티션에 대해 **암호화**가 선택되어 있는지 확인합니다.

   ![CentOS 7 설치 - 루트 파티션에 대한 암호화 선택](./media/disk-encryption/centos-encrypt-fig2.png)

3. 암호를 제공합니다. 키 자격 증명 모음에 업로드할 암호입니다.

   ![CentOS 7 설치 - 암호 제공](./media/disk-encryption/centos-encrypt-fig3.png)

4. VM을 부팅하고 암호를 묻는 메시지가 표시되면 3단계에서 제공한 암호를 사용합니다.

   ![CentOS 7 설치 - 부팅 시 암호 입력](./media/disk-encryption/centos-encrypt-fig4.png)

5. [Azure용 CentOS 기반 가상 머신 준비](./create-upload-centos.md?toc=/azure/virtual-machines/linux/toc.json#centos-70)의 "CentOS 7.0+" 지침에 따라 Azure에 업로드할 VM을 준비합니다. 마지막 단계(VM 프로비전 해제)를 아직 실행하지 마세요.

6. 이제 VM을 프로비전 해제하고 VHD를 Azure에 업로드할 수 있습니다.

Azure로 작업할 암호화를 구성하려면 다음 단계를 수행합니다.

1. /etc/dracut.conf를 편집하고 다음 줄을 추가합니다.
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. /usr/lib/dracut/modules.d/90crypt/module-setup.sh 파일의 끝에서 다음 줄을 주석 처리합니다.
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh 파일의 시작 부분에 다음 줄을 추가합니다.
   ```bash
    DRACUT_SYSTEMD=0
   ```
   다음 항목을 그 아래 항목으로 바꿉니다.
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   다음으로 변경:
   ```bash
    if [ 1 ]; then
   ```
4. Ask.sh를 편집 하 고 "# Open LUKS device" 뒤에 다음을 추가 합니다.
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. "/Usr/sbin/dracut-f-v"를 실행 하 여 initrd를 업데이트 합니다.

    ![CentOS 7 설치 - /usr/sbin/dracut -f -v 실행](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Azure Storage 계정에 암호화된 VHD 업로드
DM 암호화를 사용 하도록 설정한 후에는 로컬 암호화 된 VHD를 저장소 계정에 업로드 해야 합니다.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a> 미리 암호화된 VM에 대한 비밀을 키 자격 증명 모음에 업로드
Azure AD 앱(이전 릴리스)을 사용하여 암호화하는 경우 이전에 가져온 디스크 암호화 비밀을 키 자격 증명 모음에 비밀로 업로드해야 합니다. Key Vault에는 디스크 암호화 및 Azure AD 클라이언트를 위해 설정된 사용 권한이 있어야 합니다.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>KEK로 암호화되지 않은 디스크 암호화 암호
키 자격 증명 모음에서 비밀을 설정 하려면 [AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret)를 사용 합니다. 암호는 base64 문자열로 인코딩된 다음 키 자격 증명 모음에 업로드 됩니다. 또한 Key Vault에서 비밀을 만들 때 다음 태그가 설정되었는지 확인합니다.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


[KEK를 사용하지 않고 OS 디스크를 연결](#without-using-a-kek)하기 위해 다음 단계에서 `$secretUrl`을 사용합니다.

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>KEK로 암호화된 디스크 암호화 암호
비밀을 Key Vault에 업로드하기 전에 주요 암호화 키를 사용하여 선택적으로 암호화할 수 있습니다. 먼저 래핑 [API](/rest/api/keyvault/wrapkey)를 사용하여 주요 암호화 키로 비밀을 암호화합니다. 이 wrap 작업의 출력은 base64 URL 인코딩 문자열로, cmdlet을 사용 하 여 암호로 업로드할 수 있습니다 [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

`$KeyEncryptionKey` 및 `$secretUrl`은 [KEK를 사용하여 OS 디스크를 연결](#using-a-kek)하기 위해 다음 단계에서 사용합니다.

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>OS 디스크를 연결할 때 비밀 URL 지정

###  <a name="without-using-a-kek"></a>KEK 사용 안 함
OS 디스크를 연결하는 동안 `$secretUrl`을 전달해야 합니다. URL은 "KEK로 암호화되지 않은 디스크 암호화 비밀" 섹션에서 생성했습니다.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>KEK 사용
OS 디스크를 연결할 때 `$KeyEncryptionKey` 및 `$secretUrl`을 전달합니다. URL은 "KEK로 암호화된 디스크 암호화 비밀" 섹션에서 생성했습니다.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
