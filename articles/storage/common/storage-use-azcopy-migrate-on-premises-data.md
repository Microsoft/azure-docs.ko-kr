---
title: '자습서: AzCopy를 사용하여 Azure Storage로 온-프레미스 데이터 마이그레이션 | Microsoft Docs'
description: 이 자습서에서는 AzCopy를 사용하여 Blob, 테이블 및 파일 콘텐츠 간 데이터를 마이그레이션하거나 복사합니다. 로컬 스토리지에서 Azure Storage로 데이터를 쉽게 마이그레이션합니다.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: f2b46e37241cf142f751f2f65a1ef9f092166ec8
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86120639"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>자습서: AzCopy를 사용하여 클라우드 스토리지로 온-프레미스 데이터 마이그레이션

AzCopy는 간단한 명령을 사용하여 Azure Blob Storage, Azure Files 및 Azure Table Storage 간 데이터를 복사하기 위한 명령줄 도구입니다. 명령은 최적의 성능을 위해 설계되었습니다. AzCopy를 사용하여 파일 시스템과 스토리지 계정 간 또는 스토리지 계정 간에 데이터를 복사할 수 있습니다. AzCopy를 사용하여 로컬(온-프레미스) 데이터를 스토리지 계정으로 복사할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 스토리지 계정을 만듭니다. 
> * AzCopy를 사용하여 모든 데이터를 업로드합니다.
> * 테스트를 위해 데이터를 수정합니다.
> * 예약된 작업이나 cron 작업을 만들어 업로드할 새 파일을 식별합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 최신 버전의 AzCopy를 다운로드합니다. [AzCopy 시작](storage-use-azcopy-v10.md)을 참조하세요.

Windows를 사용하는 경우 [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx)가 필요합니다. 이 자습서에서는 작업을 예약하기 위해 Schtasks를 사용하기 때문입니다. Linux 사용자는 crontab 명령을 대신 사용합니다.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>컨테이너 만들기

Blob은 항상 컨테이너에 업로드해야 하므로 첫 번째 단계는 컨테이너 만들기입니다. 컴퓨터의 파일을 폴더에 정리하듯이, 컨테이너는 Blob 그룹을 정리하는 방법으로 사용됩니다.

다음 단계에 따라 컨테이너를 만듭니다.

1. 기본 페이지에서 **스토리지 계정** 단추를 선택하고 만든 스토리지 계정을 선택합니다.
2. **서비스** 아래에서 **Blob**을 선택한 다음 **컨테이너**를 선택합니다.

   ![컨테이너 만들기](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
컨테이너 이름은 문자 또는 숫자로 시작해야 합니다. 문자, 숫자 및 하이픈(-) 문자만 포함할 수 있습니다. Blob 및 컨테이너의 이름을 지정하는 방법에 대한 자세한 규칙은 [컨테이너, Blob, 메타데이터 이름 명명 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

## <a name="download-azcopy"></a>AzCopy 다운로드

AzCopy V10 실행 파일을 다운로드합니다.

- [Windows](https://aka.ms/downloadazcopy-v10-windows)(zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)(tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)(zip)

컴퓨터의 아무 위치에 AzCopy 파일을 배치합니다. 컴퓨터에 모든 폴더에서 이 실행 파일을 참조할 수 있도록 시스템 경로 변수에 이 파일의 위치를 추가합니다.

## <a name="authenticate-with-azure-ad"></a>Azure AD를 사용하여 인증

먼저, [Storage Blob 데이터 기여자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor) 역할을 ID에 추가합니다. [Azure Portal에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 부여](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)를 참조하세요.

그런 다음, 명령 프롬프트를 열고 다음 명령을 입력한 다음, ENTER 키를 누릅니다.

```azcopy
azcopy login
```

이 명령은 웹 사이트의 인증 코드와 URL을 반환합니다. 웹 사이트를 열고, 코드를 입력하고, **다음** 단추를 선택합니다.

![컨테이너 만들기](media/storage-use-azcopy-v10/azcopy-login.png)

로그인 창이 나타납니다. 이 창에서 Azure 계정 자격 증명을 사용하여 Azure 계정에 로그인합니다. 로그인에 성공하면 브라우저 창을 닫고 AzCopy를 사용할 수 있습니다.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>폴더의 콘텐츠를 Blob Storage에 업로드

AzCopy를 사용하여 폴더의 모든 파일을 [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) 또는 [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux)의 Blob Storage에 업로드할 수 있습니다. 폴더의 모든 Blob을 업로드하려면 다음 AzCopy 명령을 입력합니다.

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* `<local-folder-path>` 자리 표시자를 파일이 포함된 폴더의 경로로 바꿉니다(예: `C:\myFolder` 또는 `/mnt/myFolder`).

* `<storage-account-name>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

* `<container-name>` 자리 표시자를 앞에서 만든 컨테이너 이름으로 바꿉니다.

지정된 디렉터리의 콘텐츠를 Blob 스토리지에 재귀적으로 업로드하려면 `--recursive` 옵션을 지정합니다. 이 옵션으로 AzCopy를 실행하면 모든 하위 폴더 및 해당 파일도 업로드됩니다.

## <a name="upload-modified-files-to-blob-storage"></a>Blob Storage에 수정된 파일 업로드

AzCopy를 사용하여 마지막으로 수정된 시간에 따라 파일을 업로드할 수 있습니다. 

이 작업을 시도하려면 테스트를 위해 원본 디렉터리에서 새 파일을 수정하거나 만듭니다. 그런 다음, AzCopy `sync` 명령을 사용합니다.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* `<local-folder-path>` 자리 표시자를 파일이 포함된 폴더의 경로로 바꿉니다(예: `C:\myFolder` 또는 `/mnt/myFolder`).

* `<storage-account-name>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

* `<container-name>` 자리 표시자를 앞에서 만든 컨테이너 이름으로 바꿉니다.

`sync` 명령에 대한 자세한 내용은 [파일 동기화](storage-use-azcopy-blobs.md#synchronize-files)를 참조하세요.

## <a name="create-a-scheduled-task"></a>예약된 작업 만들기

AzCopy 명령 스크립트를 실행하는 예약된 작업 또는 cron 작업을 만들 수 있습니다. 스크립트는 새 온-프레미스 데이터를 식별하고 특정 시간 간격에 새 온-프레미스 데이터를 클라우드 스토리지에 업로드합니다.

AzCopy 명령을 텍스트 편집기에 복사합니다. AzCopy 명령의 매개 변수 값을 적절한 값으로 업데이트합니다. 파일을 AzCopy에 대해 `script.sh`(Linux) 또는 `script.bat`(Windows)로 저장합니다. 

이 예제에서는 폴더 이름이 `myFolder`, 스토리지 계정 이름이 `mystorageaccount`, 컨테이너 이름이 `mycontainer`라고 가정합니다.

> [!NOTE]
> Linux 예제는 SAS 토큰을 추가합니다. 명령에 하나를 제공해야 합니다. 현재 버전의 AzCopy V10은 cron 작업에 Azure AD 인증을 지원하지 않습니다.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true
```

# <a name="windows"></a>[Windows](#tab/windows)

```bash
azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true
```

---

이 자습서에서 [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx)는 Windows에서 예약된 작업을 만드는 데 사용됩니다. [Crontab](http://crontab.org/) 명령은 Linux에서 cron 작업을 만드는 데 사용됩니다.

 **Schtasks**를 통해 관리자는 로컬 또는 원격 컴퓨터에서 예약된 작업을 만들고, 삭제하고, 쿼리하고, 변경하고, 실행 및 종료할 수 있습니다. **Cron**을 통해 Linux 및 Unix 사용자는 [cron 식](https://en.wikipedia.org/wiki/Cron#CRON_expression)을 사용하여 지정된 날짜와 시간에 명령 또는 스크립트를 실행할 수 있습니다.

# <a name="linux"></a>[Linux](#tab/linux)

Linux에서 cron 작업을 만들려면 터미널에 다음 명령을 입력합니다.

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

명령에서 cron 식 `*/5 * * * *`을 지정하면 셸 스크립트 `script.sh`에서 5분마다 실행해야 함을 나타냅니다. 스크립트를 매일, 매월 또는 매년 특정 시간에 실행하도록 예약할 수 있습니다. 작업 실행을 위한 날짜 및 시간 설정에 대해 자세히 알아보려면 [cron 식](https://en.wikipedia.org/wiki/Cron#CRON_expression)을 참조하세요.

# <a name="windows"></a>[Windows](#tab/windows)

Windows에서 예약된 작업을 만들려면 명령 프롬프트 또는 PowerShell에서 다음 명령을 입력합니다.

이 예제에서는 스크립트가 컴퓨터의 루트 드라이브에 있다고 가정하지만, 스크립트는 사용자가 원하는 어느 위치에 있어도 됩니다.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

명령은 다음을 사용합니다.
- 분 일정을 지정하는 `/SC` 매개 변수
- 5분의 간격을 지정하는 `/MO` 매개 변수
- 작업 이름을 지정하는 `/TN` 매개 변수
- `script.bat` 파일에 대한 경로를 지정하는 `/TR` 매개 변수

Windows에서 예약된 작업 만들기에 대해 자세히 알아보려면 [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes)를 참조하세요.

---

예약된 작업/cron 작업이 제대로 실행되는지 확인하려면 `myFolder` 디렉터리에 새 파일을 만듭니다. 새 파일이 스토리지 계정에 업로드되었는지 확인하도록 5분을 기다립니다. 예약된 작업 또는 cron 작업의 출력 로그를 보려면 로그 디렉터리로 이동합니다.

## <a name="next-steps"></a>다음 단계

온-프레미스 데이터를 Azure Storage로 또는 그 반대로 이동하는 방법을 자세히 알아보려면 다음 링크를 따라 이동하세요.

* [Azure Storage의 데이터 이동](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

AzCopy에 대한 자세한 내용은 다음 문서를 참조하세요.

* [AzCopy 시작](storage-use-azcopy-v10.md)

* [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](storage-use-azcopy-blobs.md)

* [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)

* [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)
 
* [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)
