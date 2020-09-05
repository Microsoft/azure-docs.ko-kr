---
title: Azure Blob Storage를 Linux의 파일 시스템으로 탑재하는 방법 | Microsoft Docs
description: Linux의 가상 파일 시스템 드라이버용 blobfuse를 사용 하 여 Azure Blob 저장소 컨테이너를 탑재 하는 방법에 대해 알아봅니다.
author: rishabpoh
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.date: 2/1/2019
ms.author: ripohane
ms.reviewer: dineshm
ms.openlocfilehash: 297595c6c4a9c82c3d0293f2cea2db66ea9ca54a
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180408"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>blobfuse를 사용하여 Blob Storage를 파일 시스템으로 탑재하는 방법

## <a name="overview"></a>개요
[Blobfuse](https://github.com/Azure/azure-storage-fuse)는 Azure Blob Storage의 가상 파일 시스템 드라이버입니다. Blobfuse를 사용하여 Linux 파일 시스템을 통해 스토리지 계정의 기존 블록 Blob 데이터에 액세스할 수 있습니다. Blobfuse는 슬래시 ('/')를 구분 기호로 사용 하는 가상 디렉터리 체계를 사용 합니다.  

이 가이드에서는 blobfuse를 사용하고, Blob Storage 컨테이너를 Linux에 탑재하고, 데이터에 액세스하는 방법을 보여 줍니다. blobfuse에 대한 자세한 내용은 [blobfuse 리포지토리](https://github.com/Azure/azure-storage-fuse)의 세부 정보를 참조하세요.

> [!WARNING]
> blobfuse는 단순히 요청을 [Blob REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)로 변환하기 때문에 100% POSIX 규정 준수를 보장하지 않습니다. 예를 들어 이름 바꾸기 작업은 POSIX에서 원자성이지만, blobfuse에서는 그렇지 않습니다.
> 네이티브 파일 시스템과 blobfuse의 차이점에 대한 전체 목록은 [blobfuse 소스 코드 리포지토리](https://github.com/azure/azure-storage-fuse)를 방문하세요.
> 

## <a name="install-blobfuse-on-linux"></a>Linux에 blobfuse 설치
Blobfuse 이진 파일은 Ubuntu 및 RHEL 배포를 위한 [Linux용 Microsoft 소프트웨어 리포지토리](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)에서 사용할 수 있습니다. 해당 배포에서 Blobfuse를 설치하려면 목록에서 리포지토리 중 하나를 구성합니다. 배포에 사용할 수 있는 이진 파일이 없는 경우 [Azure Storage 설치 단계](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source)에 따라 소스 코드에서 이진 파일을 빌드할 수도 있습니다.

Blobfuse는 Ubuntu 14.04, 16.04 및 18.04에 대 한 설치를 지원 합니다. 이 명령을 실행하면 해당 버전 중 하나가 배포되었는지 확인할 수 있습니다.
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Microsoft 패키지 리포지토리 구성
[Microsoft 제품용 Linux 패키지 리포지토리](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)를 구성합니다.

예를 들어 Enterprise Linux 6 배포의 경우 다음과 같습니다.
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

마찬가지로 URL을 `.../rhel/7/...`로 변경하여 Enterprise Linux 7 배포를 가리킵니다.

Ubuntu 14.04 배포의 또 다른 예:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

마찬가지로 URL을 또는로 변경 `.../ubuntu/16.04/...` `.../ubuntu/18.04/...` 하 여 다른 Ubuntu 버전을 참조 합니다.

### <a name="install-blobfuse"></a>blobfuse 설치

Ubuntu/Debian 배포:
```bash
sudo apt-get install blobfuse
```

Enterprise Linux 배포:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>탑재 준비
Blobfuse는 열려있는 파일이 있으면 버퍼링하고 캐시하기 위해 파일 시스템에 임시 경로를 요청하여 네이티브와 비슷한 성능을 제공합니다. 이 임시 경로의 경우 성능이 가장 좋은 디스크를 선택하거나 최상의 성능을 위해 RAM 디스크를 사용합니다. 

> [!NOTE]
> blobfuse는 열려 있는 모든 파일 콘텐츠를 임시 경로에 저장합니다. 열려 있는 모든 파일을 수용하기에 충분한 공간이 있는지 확인합니다. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(선택 사항) 임시 경로로 RAM 디스크 사용
다음 예제에서는 blobfuse에 대한 디렉터리를 만들고 16GB의 RAM 디스크를 만듭니다. 필요에 따라 크기를 선택합니다. 이 RAM 디스크를 사용하면 blobfuse에서 최대 16GB 크기의 파일을 열 수 있습니다. 
```bash
sudo mkdir /mnt/ramdisk
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>SSD를 임시 경로로 사용
Azure에서는 VM에서 사용할 수 있는 임시 디스크(SSD)를 사용하여 blobfuse에 대기 시간이 짧은 버퍼를 제공할 수 있습니다. Ubuntu 배포판에서, 이 사용 후 삭제 디스크는 '/mnt'에 탑재됩니다. Red Hat 및 CentOS 배포판에서는 디스크가 '/mnt/resource/'에 탑재됩니다.

사용자가 임시 경로에 액세스할 수 있는지 확인합니다.
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>스토리지 계정 자격 증명 구성
blobfuse를 사용하려면 자격 증명을 다음 형식의 텍스트 파일에 저장해야 합니다. 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
는 `accountName` 저장소 계정에 대 한 접두사 이며 전체 URL이 아닙니다.

다음을 사용 하 여이 파일을 만듭니다.

```
touch ~/fuse_connection.cfg
```

이 파일을 만들고 편집한 후에는 다른 사용자가 읽을 수 없도록 액세스를 제한 해야 합니다.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Windows에서 구성 파일을 만든 경우 `dos2unix`를 실행하여 삭제하고 Unix 형식으로 파일을 변환해야 합니다. 
>

### <a name="create-an-empty-directory-for-mounting"></a>탑재할 빈 디렉터리 만들기
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>탑재

> [!NOTE]
> 탑재 옵션에 대한 전체 목록은 [blobfuse 리포지토리](https://github.com/Azure/azure-storage-fuse#mount-options)를 확인하세요.  
> 

blobfuse를 탑재하려면 사용자와 함께 다음 명령을 실행합니다. 이 명령은 '/path/to/fuse_connection.cfg'에 지정된 컨테이너를 '/mycontainer' 위치에 탑재합니다.

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

이제 일반 파일 시스템 API를 통해 블록 Blob에 액세스할 수 있습니다. 디렉터리를 탑재하는 사용자만 디렉터리에 액세스할 수 있으므로, 기본적으로, 액세스가 보호됩니다. 모든 사용자에게 액세스를 허용하려면 ```-o allow_other``` 옵션을 통해 탑재하면 됩니다. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>다음 단계

* [blobfuse 홈페이지](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [blobfuse 문제 보고](https://github.com/Azure/azure-storage-fuse/issues) 

