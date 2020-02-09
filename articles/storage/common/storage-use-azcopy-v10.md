---
title: AzCopy v10를 사용 하 여 데이터를 Azure Storage 복사 또는 이동 Microsoft Docs
description: AzCopy은 저장소 계정 간에 데이터를 복사 하거나 저장소 계정 간에 데이터를 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에서는 AzCopy를 다운로드 하 고, 저장소 계정에 연결한 다음, 파일을 전송 하는 방법을 설명 합니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: c4e2681121a15e0b84a11c7cf35119c3f1b69f11
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443731"
---
# <a name="get-started-with-azcopy"></a>AzCopy 시작

AzCopy는 blob 또는 파일을 저장소 계정에 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에서는 AzCopy를 다운로드 하 고, 저장소 계정에 연결한 다음, 파일을 전송 하는 방법을 설명 합니다.

> [!NOTE]
> AzCopy **V10** 는 현재 지원 되는 AzCopy 버전입니다.
>
> AzCopy **v 8.1**을 사용 해야 하는 경우이 문서의 [이전 버전의 AzCopy 사용](#previous-version) 섹션을 참조 하세요.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>AzCopy 다운로드

먼저 AzCopy V10 실행 파일을 컴퓨터의 모든 디렉터리에 다운로드 합니다. AzCopy V10는 실행 파일 일 뿐 이므로 설치할 항목이 없습니다.

- [Windows 64](https://aka.ms/downloadazcopy-v10-windows) 비트 (zip)
- [Windows 32](https://aka.ms/downloadazcopy-v10-windows-32bit) 비트 (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)(tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)(zip)

이러한 파일은 zip 파일 (Windows 및 Mac) 또는 tar 파일 (Linux)로 압축 됩니다. Linux에서 tar 파일을 다운로드 하 고 압축을 풀려면 Linux 배포에 대 한 설명서를 참조 하세요.

> [!NOTE]
> [Azure Table storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) 서비스로 데이터를 복사 하려면 [AzCopy 버전 7.3](https://aka.ms/downloadazcopynet)을 설치 합니다.


## <a name="run-azcopy"></a>AzCopy 실행

편의상, 사용 편의성을 위해 AzCopy 실행 파일의 디렉터리 위치를 시스템 경로에 추가 하는 것이 좋습니다. 이렇게 하면 시스템의 모든 디렉터리에서 `azcopy`를 입력할 수 있습니다.

AzCopy 디렉터리를 경로에 추가 하지 않도록 선택 하는 경우 AzCopy 실행 파일의 위치로 디렉터리를 변경 하 고 Windows PowerShell 명령 프롬프트에서 `azcopy` 또는 `.\azcopy`를 입력 해야 합니다.

명령 목록을 보려면 `azcopy -h`를 입력 한 다음 ENTER 키를 누릅니다.

특정 명령에 대 한 자세한 내용을 보려면 명령 이름 (예: `azcopy list -h`)을 포함 하면 됩니다.

![인라인 도움말](media/storage-use-azcopy-v10/azcopy-inline-help.png)

각 명령 및 명령 매개 변수에 대 한 자세한 참조 설명서를 찾으려면 [azcopy](storage-ref-azcopy.md) 를 참조 하세요.

> [!NOTE] 
> Azure Storage 계정의 소유자는 데이터에 액세스할 수 있는 권한을 자동으로 할당 하지 않습니다. AzCopy를 사용 하 여 의미 있는 작업을 수행 하려면 먼저 저장소 서비스에 권한 부여 자격 증명을 제공 하는 방법을 결정 해야 합니다. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>권한 부여 자격 증명을 제공 하는 방법을 선택 합니다.

AD (Azure Active Directory)를 사용 하거나 SAS (공유 액세스 서명) 토큰을 사용 하 여 권한 부여 자격 증명을 제공할 수 있습니다.

이 표를 가이드로 사용 합니다.

| 스토리지 유형 | 현재 지원 되는 권한 부여 방법 |
|--|--|
|**Blob Storage** | Azure AD & SAS |
|**Blob 저장소 (계층적 네임 스페이스)** | Azure AD & SAS |
|**File Storage** | SAS 전용 |

### <a name="option-1-use-azure-active-directory"></a>옵션 1: Azure Active Directory 사용

Azure Active Directory를 사용 하 여 각 명령에 SAS 토큰을 추가 하지 않고 자격 증명을 한 번 제공할 수 있습니다.  

> [!NOTE]
> 현재 릴리스에서 저장소 계정 간에 blob을 복사 하려는 경우 각 원본 URL에 SAS 토큰을 추가 해야 합니다. SAS 토큰은 대상 URL 에서만 생략할 수 있습니다. 예제는 [저장소 계정 간에 Blob 복사](storage-use-azcopy-blobs.md)를 참조 하세요.

필요한 권한 부여 수준은 파일을 업로드할 것인지 아니면 다운로드할지를 기준으로 합니다.

파일을 다운로드 하려는 경우에는 [저장소 Blob 데이터 판독기](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) 가 사용자 id, 관리 id 또는 서비스 주체에 할당 되었는지 확인 합니다.

> 사용자 id, 관리 id 및 서비스 주체는 각각 *보안 주체의*유형 이므로이 문서의 나머지 부분에는 *보안 주체* 라는 용어를 사용 합니다.

파일을 업로드 하려면 다음 역할 중 하나가 보안 주체에 할당 되었는지 확인 합니다.

- [Storage Blob 데이터 기여자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Storage Blob 데이터 소유자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

이러한 모든 범위에서 보안 주체에 이러한 역할을 할당할 수 있습니다.

- 컨테이너 (파일 시스템)
- Storage 계정
- 리소스 그룹
- Subscription

역할을 확인 하 고 할당 하는 방법에 대 한 자세한 내용은 [Azure Portal에서 Azure blob에 대 한 액세스 권한 부여 및 RBAC를 사용 하 여 데이터 큐](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

> [!NOTE]
> RBAC 역할 할당을 전파 하는 데 최대 5 분이 걸릴 수 있다는 점에 유의 하세요.

보안 주체를 대상 컨테이너 또는 디렉터리의 ACL (액세스 제어 목록)에 추가 하는 경우 이러한 역할 중 하나를 보안 주체에 할당 하지 않아도 됩니다. ACL에서 보안 주체에는 대상 디렉터리에 대 한 쓰기 권한과 컨테이너 및 각 부모 디렉터리에 대 한 실행 권한이 필요 합니다.

자세히 알아보려면 [Azure Data Lake Storage Gen2의 Access control](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)을 참조 하세요.

#### <a name="authenticate-a-user-identity"></a>사용자 id 인증

사용자 id에 필요한 권한 부여 수준이 지정 되었는지 확인 한 후 명령 프롬프트를 열고 다음 명령을 입력 한 다음 ENTER 키를 누릅니다.

```azcopy
azcopy login
```

둘 이상의 조직에 속한 경우 저장소 계정이 속한 조직의 테넌트 ID를 포함 합니다.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

`<tenant-id>` 자리 표시자를 저장소 계정이 속한 조직의 테넌트 ID로 바꿉니다. 테넌트 ID를 찾으려면 Azure Portal에서 **디렉터리 id > Azure Active Directory > 속성** 을 선택 합니다.

이 명령은 웹 사이트의 인증 코드와 URL을 반환합니다. 웹 사이트를 열고, 코드를 입력하고, **다음** 단추를 선택합니다.

![컨테이너 만들기](media/storage-use-azcopy-v10/azcopy-login.png)

로그인 창이 나타납니다. 이 창에서 Azure 계정 자격 증명을 사용하여 Azure 계정에 로그인합니다. 로그인에 성공하면 브라우저 창을 닫고 AzCopy를 사용할 수 있습니다.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>서비스 사용자 인증

이 옵션은 특히 온-프레미스에서 실행 하는 경우 사용자 개입 없이 실행 되는 스크립트 내에서 AzCopy를 사용 하려는 경우에 유용 합니다. Azure에서 실행 되는 Vm에서 AzCopy를 실행 하려는 경우 관리 되는 서비스 id를 관리 하기가 더 쉽습니다. 자세히 알아보려면이 문서의 [관리 Id 인증](#managed-identity) 섹션을 참조 하세요.

스크립트를 실행 하기 전에 서비스 주체의 자격 증명을 사용 하 여 AzCopy을 제공할 수 있도록 한 번 이상 대화형으로 로그인 해야 합니다.  이러한 자격 증명은 스크립트가 중요 한 정보를 제공할 필요가 없도록 보안 및 암호화 된 파일에 저장 됩니다.

클라이언트 암호를 사용 하거나 서비스 주체의 앱 등록과 연결 된 인증서의 암호를 사용 하 여 계정에 로그인 할 수 있습니다.

서비스 주체를 만드는 방법에 대해 자세히 알아보려면 [방법: 포털을 사용 하 여 리소스에 액세스할 수 있는 AZURE AD 응용 프로그램 및 서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)를 참조 하세요.

일반적으로 서비스 주체에 대 한 자세한 내용은 [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) 를 참조 하세요.

##### <a name="using-a-client-secret"></a>클라이언트 암호 사용

`AZCOPY_SPA_CLIENT_SECRET` 환경 변수를 서비스 주체의 앱 등록에 대 한 클라이언트 암호로 설정 하 여 시작 합니다.

> [!NOTE]
> 운영 체제의 환경 변수 설정이 아니라 명령 프롬프트에서이 값을 설정 해야 합니다. 이렇게 하면 현재 세션에만 값을 사용할 수 있습니다.

이 예제에서는 PowerShell에서이 작업을 수행 하는 방법을 보여 줍니다.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> 이 예제에 나와 있는 것 처럼 프롬프트를 사용 하는 것이 좋습니다. 이렇게 하면 사용자의 암호가 콘솔의 명령 기록에 표시 되지 않습니다.  

다음 명령을 입력 하 고 ENTER 키를 누릅니다.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

`<application-id>` 자리 표시자를 서비스 사용자의 앱 등록에 대 한 응용 프로그램 ID로 바꿉니다. `<tenant-id>` 자리 표시자를 저장소 계정이 속한 조직의 테넌트 ID로 바꿉니다. 테넌트 ID를 찾으려면 Azure Portal에서 **디렉터리 id > Azure Active Directory > 속성** 을 선택 합니다. 

##### <a name="using-a-certificate"></a>인증서 사용

권한 부여를 위해 자체 자격 증명을 사용 하려는 경우 앱 등록에 인증서를 업로드 한 다음 해당 인증서를 사용 하 여 로그인 할 수 있습니다.

앱 등록에 인증서를 업로드 하는 것 외에도 AzCopy가 실행 되는 컴퓨터 또는 VM에 인증서 복사본을 저장 해야 합니다. 인증서 복사본은에 있어야 합니다. PFX 또는. PEM 형식 및에는 개인 키가 포함 되어야 합니다. 개인 키는 암호로 보호 되어야 합니다. Windows를 사용 하는 경우 인증서가 인증서 저장소에만 있는 경우 해당 인증서를 PFX 파일 (개인 키 포함)로 내보내야 합니다. 지침은 [get-pfxcertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps) 를 참조 하세요.

그런 다음 `AZCOPY_SPA_CERT_PASSWORD` 환경 변수를 인증서 암호로 설정 합니다.

> [!NOTE]
> 운영 체제의 환경 변수 설정이 아니라 명령 프롬프트에서이 값을 설정 해야 합니다. 이렇게 하면 현재 세션에만 값을 사용할 수 있습니다.

이 예제에서는 PowerShell에서이 작업을 수행 하는 방법을 보여 줍니다.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

다음 명령을 입력 하 고 ENTER 키를 누릅니다.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

`<path-to-certificate-file>` 자리 표시자를 인증서 파일의 상대 또는 정규화 된 경로로 바꿉니다. AzCopy는이 인증서에 대 한 경로를 저장 하지만 인증서 복사본을 저장 하지 않으므로 해당 인증서를 그대로 유지 해야 합니다. `<tenant-id>` 자리 표시자를 저장소 계정이 속한 조직의 테넌트 ID로 바꿉니다. 테넌트 ID를 찾으려면 Azure Portal에서 **디렉터리 id > Azure Active Directory > 속성** 을 선택 합니다.

> [!NOTE]
> 이 예제에 나와 있는 것 처럼 프롬프트를 사용 하는 것이 좋습니다. 이렇게 하면 사용자의 암호가 콘솔의 명령 기록에 표시 되지 않습니다. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>관리 id 인증

사용자 개입 없이 실행 되는 스크립트 내에서 AzCopy를 사용 하 고 Azure VM (가상 컴퓨터)에서 스크립트를 실행 하려는 경우이 옵션을 사용 하는 것이 좋습니다. 이 옵션을 사용 하는 경우 VM에 자격 증명을 저장할 필요가 없습니다.

VM에서 사용 하도록 설정한 시스템 차원의 관리 되는 id를 사용 하거나 VM에 할당 된 사용자 할당 관리 id의 클라이언트 ID, 개체 ID 또는 리소스 ID를 사용 하 여 계정에 로그인 할 수 있습니다.

시스템 차원의 관리 되는 id를 사용 하도록 설정 하는 방법 또는 사용자 할당 관리 id를 만드는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 VM에서 Azure 리소스에 대 한 관리 되는 Id 구성](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)을 참조 하세요.

##### <a name="using-a-system-wide-managed-identity"></a>시스템 차원의 관리 되는 id 사용

먼저 VM에서 시스템 차원의 관리 되는 id를 사용 하도록 설정 했는지 확인 합니다. [시스템 할당 관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)를 참조 하십시오.

그런 다음 명령 콘솔에서 다음 명령을 입력 하 고 ENTER 키를 누릅니다.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>사용자 할당 관리 id 사용

먼저 VM에서 사용자 할당 관리 id를 사용 하도록 설정 했는지 확인 합니다. [사용자 할당 관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity)를 참조 하십시오.

그런 다음 명령 콘솔에서 다음 명령 중 하나를 입력 하 고 ENTER 키를 누릅니다.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

`<client-id>` 자리 표시자를 사용자 할당 관리 id의 클라이언트 ID로 바꿉니다.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

`<object-id>` 자리 표시자를 사용자 할당 관리 id의 개체 ID로 바꿉니다.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

`<resource-id>` 자리 표시자를 사용자 할당 관리 id의 리소스 ID로 바꿉니다.

### <a name="option-2-use-a-sas-token"></a>옵션 2: SAS 토큰 사용

AzCopy 명령에서 사용 하는 각 원본 또는 대상 URL에 SAS 토큰을 추가할 수 있습니다.

이 예제 명령은 로컬 디렉터리에서 blob 컨테이너로 데이터를 재귀적으로 복사 합니다. 가상의 SAS 토큰이 컨테이너 URL의 끝에 추가 됩니다.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS 토큰 및 SAS 토큰을 구하는 방법에 대 한 자세한 내용은 [sas (공유 액세스 서명) 사용](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)을 참조 하세요.

## <a name="transfer-files"></a>파일 전송

Id를 인증 하거나 SAS 토큰을 얻은 후 파일 전송을 시작할 수 있습니다.

예제 명령을 찾으려면 다음 문서 중 하나를 참조 하세요.

- [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](storage-use-azcopy-blobs.md)

- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)

- [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)

- [AzCopy 및 Azure Stack 저장소를 사용 하 여 데이터 전송](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>스크립트에서 AzCopy 사용

### <a name="obtain-a-static-download-link"></a>정적 다운로드 링크 가져오기

시간이 지남에 따라 AzCopy [다운로드 링크](#download-and-install-azcopy) 는 새 버전의 AzCopy를 가리킵니다. 스크립트에서 AzCopy를 다운로드 하는 경우 새 버전의 AzCopy가 스크립트가 종속 된 기능을 수정 하면 스크립트가 작동을 중지할 수 있습니다.

이러한 문제를 방지 하려면 AzCopy의 현재 버전에 대 한 정적 (변경 취소) 링크를 가져옵니다. 이렇게 하면 스크립트는 실행 될 때마다 동일한 버전의 AzCopy를 다운로드 합니다.

링크를 가져오려면 다음 명령을 실행 합니다.

| 운영 체제  | 명령 |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Linux의 경우 `tar` 명령에서 `--strip-components=1` 버전 이름을 포함 하는 최상위 폴더를 제거 하 고 대신 이진 파일을 현재 폴더로 직접 추출 합니다. 이렇게 하면 `wget` URL만 업데이트 하 여 새 버전의 `azcopy`로 스크립트를 업데이트할 수 있습니다.

URL은이 명령의 출력에 표시 됩니다. 그런 다음 스크립트에서 해당 URL을 사용 하 여 AzCopy를 다운로드할 수 있습니다.

| 운영 체제  | 명령 |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>SAS 토큰의 이스케이프 특수 문자

`.cmd` 확장이 있는 배치 파일에서 SAS 토큰에 표시 되는 `%` 문자를 이스케이프 해야 합니다. SAS 토큰 문자열의 기존 `%` 문자 옆에 추가 `%` 문자를 추가 하 여이 작업을 수행할 수 있습니다.

### <a name="run-scripts-by-using-jenkins"></a>Jenkins를 사용 하 여 스크립트 실행

[Jenkins](https://jenkins.io/) 를 사용 하 여 스크립트를 실행 하려는 경우 스크립트의 시작 부분에 다음 명령을 입력 해야 합니다.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Azure Storage 탐색기에서 AzCopy 사용

[Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) AzCopy를 사용 하 여 모든 데이터 전송 작업을 수행 합니다. AzCopy의 성능 이점을 활용 하려는 경우에는 [Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) 를 사용할 수 있지만 명령줄이 아닌 그래픽 사용자 인터페이스를 사용 하 여 파일을 조작 하는 것이 좋습니다.

Storage 탐색기는 계정 키를 사용 하 여 작업을 수행 하므로 Storage 탐색기에 로그인 한 후에는 추가 권한 부여 자격 증명을 제공할 필요가 없습니다.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>이전 버전의 AzCopy 사용

이전 버전의 AzCopy (AzCopy v 8.1)를 사용 해야 하는 경우 다음 링크 중 하나를 참조 하세요.

- [Windows의 AzCopy (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [Linux의 AzCopy (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy 구성, 최적화 및 문제 해결

[AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

질문, 문제 또는 일반 피드백이 있는 경우 [GitHub 페이지에서](https://github.com/Azure/azure-storage-azcopy) 제출 합니다.
