---
title: NFS 3.0 프로토콜 (미리 보기)을 사용 하 여 Azure Blob storage 탑재 | Microsoft Docs
description: NFS 3.0 프로토콜을 사용 하 여 온-프레미스에서 실행 되는 Azure VM (가상 머신) 또는 클라이언트에서 Blob 저장소에 컨테이너를 탑재 하는 방법에 대해 알아봅니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 09206b8189f03a37f8bd7d073238609a3f1bd3ad
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816102"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>NFS (네트워크 파일 시스템) 3.0 프로토콜 (미리 보기)을 사용 하 여 Blob storage 탑재

NFS 3.0 프로토콜을 사용 하 여 온-프레미스에서 실행 되는 Windows 또는 Linux 기반 Azure VM (가상 머신) 또는 Windows 또는 Linux 시스템에서 Blob 저장소에 컨테이너를 탑재할 수 있습니다. 이 문서에서는 단계별 지침을 제공 합니다. Blob storage에서 NFS 3.0 프로토콜 지원에 대 한 자세한 내용은 [Azure blob storage에서 nfs (네트워크 파일 시스템) 3.0 프로토콜 지원 (미리 보기)](network-file-system-protocol-support.md)을 참조 하세요.

> [!NOTE]
> NFS 3.0 Azure Blob storage의 프로토콜 지원은 공개 미리 보기로 제공 되며 미국 동부, 미국 중부, 미국 서 부 중부, 오스트레일리아 남동쪽, 서유럽, 영국 서부, 대한민국 중부, 한국 남부 및 캐나다 중부 지역에서 사용할 수 있습니다.

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>1 단계: 구독과 함께 NFS 3.0 프로토콜 기능 등록

1. PowerShell 명령 창을 엽니다. 

2. `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

3. Id가 둘 이상의 구독과 연결 된 경우 활성 구독을 설정 합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

4. `AllowNFSV3`다음 명령을 사용 하 여 기능을 등록 합니다.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. `PremiumHns`다음 명령을 사용 하 여 기능을 등록 합니다.

   ```powershell
   Register-AzProviderFeature -FeatureName PremiumHns -ProviderNamespace Microsoft.Storage  
   ```

6. 다음 명령을 사용 하 여 리소스 공급자를 등록 합니다.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>2 단계: 기능이 등록 되었는지 확인 

등록 승인은 최대 한 시간까지 걸릴 수 있습니다. 등록이 완료 되었는지 확인 하려면 다음 명령을 사용 합니다.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName PremiumHns  
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>3 단계: Azure Virtual Network (VNet) 만들기

저장소 계정은 VNet 내에 포함 되어야 합니다. VNet을 통해 클라이언트는 저장소 계정에 안전 하 게 연결할 수 있습니다. VNet에 대 한 자세한 내용 및 VNet을 만드는 방법에 대 한 자세한 내용은 [Virtual Network 설명서](https://docs.microsoft.com/azure/virtual-network/)를 참조 하세요.

> [!NOTE]
> 동일한 VNet의 클라이언트는 사용자 계정에 컨테이너를 탑재할 수 있습니다. 온-프레미스 네트워크에서 실행 되는 클라이언트에서 컨테이너를 탑재할 수도 있지만 먼저 온-프레미스 네트워크를 VNet에 연결 해야 합니다. [지원 되는 네트워크 연결](network-file-system-protocol-support.md#supported-network-connections)을 참조 하세요.

## <a name="step-4-configure-network-security"></a>4 단계: 네트워크 보안 구성

계정에서 데이터를 보호 하는 유일한 방법은 VNet 및 기타 네트워크 보안 설정을 사용 하는 것입니다. 계정 키 인증, AD (Azure Active Directory) 보안 및 Acl (액세스 제어 목록)을 포함 하 여 데이터를 보호 하는 데 사용 되는 다른 도구는 NFS 3.0 프로토콜 지원이 설정 된 계정에서 아직 지원 되지 않습니다. 

계정에서 데이터를 보호 하려면 권장 사항: [Blob 저장소에 대 한 네트워크 보안 권장](security-recommendations.md#networking)사항을 참조 하세요.

## <a name="step-5-create-and-configure-a-storage-account"></a>5 단계: 저장소 계정 만들기 및 구성

NFS 3.0를 사용 하 여 컨테이너를 탑재 하려면 구독에 기능을 등록 **한 후** 저장소 계정을 만들어야 합니다. 기능을 등록 하기 전에 존재 했던 계정은 사용 하도록 설정할 수 없습니다. 

이 기능의 미리 보기 릴리스에서는 NFS 3.0 프로토콜이 [Blockblobstorage](../blobs/storage-blob-create-account-block-blob.md) 계정 에서만 지원 됩니다.

계정을 구성 하는 경우 다음 값을 선택 합니다.

|Setting | 값|
|----|---|
|위치|미국 동부, 미국 중부, 미국 서 부 중부, 오스트레일리아 남동쪽, 서유럽, 영국 서부, 대한민국 중부, 한국 남부 및 캐나다 중부 지역 중 하나입니다. |
|성능|Premium|
|계정 종류|BlockBlobStorage|
|복제|LRS(로컬 중복 스토리지)|
|연결 방법|공용 끝점 (선택한 네트워크) 또는 개인 끝점|
|보안 전송 필요|사용 안 함|
|계층 구조 네임스페이스|사용|
|NFS V3|사용|

다른 모든 설정의 기본값을 그대로 사용할 수 있습니다. 

## <a name="step-6-create-a-container"></a>6단계: 컨테이너 만들기

다음 도구 또는 Sdk 중 하나를 사용 하 여 저장소 계정에 컨테이너를 만듭니다.

|도구|SDK|
|---|---|
|[Azure Portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AZCopy](../common/storage-use-azcopy-blobs.md#create-a-container)|[Java](data-lake-storage-directory-file-acl-java.md#create-a-container)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST (영문)](https://docs.microsoft.com/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>7 단계: 컨테이너 탑재

Windows 또는 Linux 시스템에서 디렉터리를 만든 다음 컨테이너를 저장소 계정에 탑재 합니다.

### <a name="linux"></a>[Linux](#tab/linux)

1. Linux 시스템에서 디렉터리를 만듭니다.

   ```
   mkdir -p /mnt/test
   ```

2. 다음 명령을 사용 하 여 컨테이너를 탑재 합니다.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - `<storage-account-name>`이 명령에 표시 되는 자리 표시자를 사용자의 저장소 계정 이름으로 바꿉니다.  

   - `<container-name>`자리 표시자를 컨테이너의 이름으로 바꿉니다.


### <a name="windows"></a>[Windows](#tab/windows)

1. **Windows 기능** 대화 상자를 연 다음 **NFS 용 클라이언트** 기능을 설정 합니다. 

   ![네트워크 파일 시스템용 클라이언트 기능](media/network-file-system-protocol-how-to/client-for-network-files-system-feature.png)

2. [Mount](https://docs.microsoft.com/windows-server/administration/windows-commands/mount) 명령을 사용 하 여 컨테이너를 탑재 합니다.

   ```
   mount -o nolock <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name> *
   ```

   - `<storage-account-name>`이 명령에 표시 되는 자리 표시자를 사용자의 저장소 계정 이름으로 바꿉니다.  

   - `<container-name>`자리 표시자를 컨테이너의 이름으로 바꿉니다.

3. 쓰기 권한이 필요한 경우 Windows에서 공유에 연결 하는 데 사용 하는 기본 UID 및 GID를 변경 해야 할 수 있습니다. 이렇게 하려면 관리자 권한으로 다음 PowerShell 명령을 실행 합니다.

   ```
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousUid -PropertyType DWord -Value 0
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousGid -PropertyType DWord -Value 0
   ```
   
   - NFS 클라이언트 서비스를 다시 시작 하거나이 변경 작업을 수행한 후 서버를 다시 부팅 하십시오.

---

## <a name="resolve-common-issues"></a>일반적인 문제 해결

|문제/오류 | 해결 방법|
|---|---|
|`Access denied by server while mounting`|지원되는 서브넷 내에서 클라이언트가 실행되고 있는지 확인합니다. [지원 되는 네트워크 위치](network-file-system-protocol-support.md#supported-network-connections)를 참조 하세요.|
|`No such file or directory`| 기능이 등록되었는지 확인한 후 탑재할 컨테이너가 생성되었는지 확인합니다. [2 단계: 기능이 등록 되었는지 확인](#step-2-verify-that-the-feature-is-registered)을 참조 하세요. 또한 탑재 명령과 매개 변수를 터미널에 직접 입력 해야 합니다. 이 명령의 일부를 복사하여 다른 애플리케이션의 터미널에 붙여넣는 경우 붙여넣은 정보에 숨겨진 문자가 있으면 이 오류가 발생할 수 있습니다.|

## <a name="see-also"></a>추가 정보

[Azure Blob storage에서 NFS (네트워크 파일 시스템) 3.0 프로토콜 지원 (미리 보기)](network-file-system-protocol-support.md)







