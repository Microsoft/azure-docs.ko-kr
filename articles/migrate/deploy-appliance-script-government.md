---
title: Azure Government에서 Azure Migrate 어플라이언스 설정
description: 에서 Azure Migrate 어플라이언스를 설정 하는 방법에 대해 알아봅니다 Azure Government
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: c4ca8d8ac24ac174158957e44b5eabe4a89a5340
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775207"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Azure Government에서 어플라이언스 설정 

이 문서에 따라 VMware 환경의 서버, Hyper-v의 서버 및 물리적 서버에 대 한 [Azure Migrate 어플라이언스](./migrate-appliance-architecture.md) 를 Azure Government 클라우드에 배포 합니다. 스크립트를 실행 하 여 어플라이언스를 만들고 Azure에 연결할 수 있는지 확인 합니다. 공용 클라우드에서 어플라이언스를 설정 하려면 [이 문서](deploy-appliance-script.md)를 따릅니다.


> [!NOTE]
> 템플릿을 사용 하 여 어플라이언스를 배포 하는 옵션 (VMware 환경 및 Hyper-v 환경의 서버)은 Azure Government 지원 되지 않습니다.


## <a name="prerequisites"></a>사전 요구 사항

이 스크립트는 기존 물리적 서버 또는 가상화 된 서버에 Azure Migrate 어플라이언스를 설정 합니다.

- 어플라이언스 역할을 하는 서버는 Windows Server 2016, 32 GB 메모리, 8 개의 vCPUs, 80 g b의 디스크 저장소 및 외부 가상 스위치를 실행 해야 합니다. 고정 또는 동적 IP 주소와 인터넷에 대 한 액세스 권한이 필요 합니다.
- 어플라이언스를 배포 하기 전에 Hyper-v 및 [물리적 서버](migrate-appliance.md#appliance---physical) [에서](migrate-appliance.md#appliance---hyper-v) [VMware의 서버](migrate-appliance.md#appliance---vmware)에 대 한 자세한 어플라이언스 요구 사항을 검토 합니다.
- 기존 Azure Migrate 어플라이언스에서 스크립트를 실행 하지 마세요.

## <a name="set-up-the-appliance-for-vmware"></a>VMware에 대 한 어플라이언스 설정

VMware에 대 한 어플라이언스를 설정 하려면 Azure Portal에서 zip 파일을 다운로드 하 고 콘텐츠를 추출 합니다. PowerShell 스크립트를 실행 하 여 어플라이언스 웹 앱을 시작 합니다. 어플라이언스를 설정 하 고 처음으로 구성 합니다. 그런 다음 프로젝트에 어플라이언스를 등록 합니다.

### <a name="download-the-script"></a>스크립트 다운로드

1. **마이그레이션 목표**  >  **Windows, Linux 및 SQL server**  >  **Azure Migrate: 검색 및 평가** 에서 **검색** 을 클릭 합니다.
2. 서버 **를**  >  **가상화 하 시겠습니까?** 에서 **예, VMware vSphere 하이퍼바이저** 를 선택 합니다.
3. **다운로드** 를 클릭 하 여 압축 된 파일을 다운로드 합니다.

### <a name="verify-file-security"></a>파일 보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. 최신 어플라이언스 버전 및 해시 값을 확인 합니다.

    **알고리즘** | **다운로드** | **SHA256**
    --- | --- | ---
    VMware(85.8MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


### <a name="run-the-script"></a>스크립트 실행

스크립트에서 수행 하는 작업은 다음과 같습니다.

- 에이전트와 웹 응용 프로그램을 설치 합니다.
- Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯 한 Windows 역할을 설치 합니다.
- IIS 다시 쓰기 가능한 모듈을 다운로드 하 여 설치 합니다. [자세히 알아보기](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure Migrate에 대 한 영구 설정을 사용 하 여 레지스트리 키 (HKLM)를 업데이트 합니다.
- 는 다음과 같이 로그 및 구성 파일을 만듭니다.
    - **구성 파일**:%ProgramData%\Microsoft Azure\Config
    - **로그 파일**:%ProgramData%\Microsoft Azure\Logs

스크립트를 실행하려면

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다. 기존 Azure Migrate 어플라이언스를 사용 하는 서버에서 스크립트를 실행 하지 않아야 합니다.
2. 관리자 권한 (관리자 권한)을 사용 하 여 서버에서 PowerShell을 시작 합니다.
3. PowerShell 디렉터리를 다운로드 한 압축 파일에서 추출한 콘텐츠를 포함 하는 폴더로 변경 합니다.
4. 다음과 같이 스크립트 **AzureMigrateInstaller.ps1** 를 실행 합니다.
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. 스크립트가 성공적으로 실행 되 면 어플라이언스를 설정할 수 있도록 어플라이언스 웹 응용 프로그램을 시작 합니다. 문제가 발생 하는 경우 C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그를 검토 합니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스를 사용 하 여 [정부 클라우드의](migrate-appliance.md#government-cloud-urls)Azure url에 연결할 수 있는지 확인 합니다.


## <a name="set-up-the-appliance-for-hyper-v"></a>Hyper-v에 대 한 어플라이언스 설정

Hyper-v에 대 한 어플라이언스를 설정 하려면 Azure Portal에서 압축 된 파일을 다운로드 하 고 콘텐츠를 추출 합니다. PowerShell 스크립트를 실행 하 여 어플라이언스 웹 앱을 시작 합니다. 어플라이언스를 설정 하 고 처음으로 구성 합니다. 그런 다음 프로젝트에 어플라이언스를 등록 합니다.

### <a name="download-the-script"></a>스크립트 다운로드

1.  **마이그레이션 목표**  >  **Windows, Linux 및 SQL server**  >  **Azure Migrate: 검색 및 평가** 에서 **검색** 을 클릭 합니다.
2.  서버 를  >  **가상화 하는** 서버 검색에서 **예, hyper-v 사용** 을 선택 합니다.
3.  **다운로드** 를 클릭 하 여 압축 된 파일을 다운로드 합니다. 


### <a name="verify-file-security"></a>파일 보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. 최신 어플라이언스 버전 및 해시 값을 확인 합니다.

    **시나리오** | **다운로드** | **SHA256**
    --- | --- | ---
    Hyper-V(85.8MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

### <a name="run-the-script"></a>스크립트 실행

스크립트에서 수행 하는 작업은 다음과 같습니다.

- 에이전트와 웹 응용 프로그램을 설치 합니다.
- Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯 한 Windows 역할을 설치 합니다.
- IIS 다시 쓰기 가능한 모듈을 다운로드 하 여 설치 합니다. [자세히 알아보기](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure Migrate에 대 한 영구 설정을 사용 하 여 레지스트리 키 (HKLM)를 업데이트 합니다.
- 는 다음과 같이 로그 및 구성 파일을 만듭니다.
    - **구성 파일**:%ProgramData%\Microsoft Azure\Config
    - **로그 파일**:%ProgramData%\Microsoft Azure\Logs

스크립트를 실행하려면

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다. 기존 Azure Migrate 어플라이언스를 실행 하는 서버에서 스크립트를 실행 하지 않아야 합니다.
2. 관리자 권한 (관리자 권한)을 사용 하 여 서버에서 PowerShell을 시작 합니다.
3. PowerShell 디렉터리를 다운로드 한 압축 파일에서 추출한 콘텐츠를 포함 하는 폴더로 변경 합니다.
4. 다음과 같이 스크립트 **AzureMigrateInstaller.ps1** 를 실행 합니다. 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. 스크립트가 성공적으로 실행 되 면 어플라이언스를 설정할 수 있도록 어플라이언스 웹 응용 프로그램을 시작 합니다. 문제가 발생 하는 경우 C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그를 검토 합니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스를 사용 하 여 [정부 클라우드의](migrate-appliance.md#government-cloud-urls)Azure url에 연결할 수 있는지 확인 합니다.


## <a name="set-up-the-appliance-for-physical-servers"></a>물리적 서버에 대 한 어플라이언스 설정

VMware에 대 한 어플라이언스를 설정 하려면 Azure Portal에서 zip 파일을 다운로드 하 고 콘텐츠를 추출 합니다. PowerShell 스크립트를 실행 하 여 어플라이언스 웹 앱을 시작 합니다. 어플라이언스를 설정 하 고 처음으로 구성 합니다. 그런 다음 프로젝트에 어플라이언스를 등록 합니다.

### <a name="download-the-script"></a>스크립트 다운로드

1. **마이그레이션 목표**  >  **Windows, Linux 및 SQL server**  >  **Azure Migrate: 검색 및 평가** 에서 **검색** 을 클릭 합니다.
2. 서버 를  >  **가상화 하는** 서버 검색에서 **가상화 되지 않음/기타** 를 선택 합니다.
3. **다운로드** 를 클릭 하 여 압축 된 파일을 다운로드 합니다.

### <a name="verify-file-security"></a>파일 보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일이 다운로드 된 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. 최신 어플라이언스 버전 및 해시 값을 확인 합니다.

    **시나리오** | **다운로드** _ | _ *해시 값**
    --- | --- | ---
    물리적(85MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

### <a name="run-the-script"></a>스크립트 실행

스크립트에서 수행 하는 작업은 다음과 같습니다.

- 에이전트와 웹 응용 프로그램을 설치 합니다.
- Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯 한 Windows 역할을 설치 합니다.
- IIS 다시 쓰기 가능한 모듈을 다운로드 하 여 설치 합니다. [자세히 알아보기](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure Migrate에 대 한 영구 설정을 사용 하 여 레지스트리 키 (HKLM)를 업데이트 합니다.
- 는 다음과 같이 로그 및 구성 파일을 만듭니다.
    - **구성 파일**:%ProgramData%\Microsoft Azure\Config
    - **로그 파일**:%ProgramData%\Microsoft Azure\Logs

스크립트를 실행하려면

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다. 기존 Azure Migrate 어플라이언스를 실행 하는 서버에서 스크립트를 실행 하지 않아야 합니다.
2. 관리자 권한 (관리자 권한)을 사용 하 여 서버에서 PowerShell을 시작 합니다.
3. PowerShell 디렉터리를 다운로드 한 압축 파일에서 추출한 콘텐츠를 포함 하는 폴더로 변경 합니다.
4. 다음과 같이 스크립트 **AzureMigrateInstaller.ps1** 를 실행 합니다.

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. 스크립트가 성공적으로 실행 되 면 어플라이언스를 설정할 수 있도록 어플라이언스 웹 응용 프로그램을 시작 합니다. 문제가 발생 하는 경우 C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그를 검토 합니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스를 사용 하 여 [정부 클라우드의](migrate-appliance.md#government-cloud-urls)Azure url에 연결할 수 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

어플라이언스를 배포한 후에는 처음으로 구성 하 고 프로젝트에 등록 해야 합니다.

- [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)에 대 한 어플라이언스를 설정 합니다.
- [Hyper-v](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)에 대 한 어플라이언스를 설정 합니다.
- [물리적 서버](how-to-set-up-appliance-physical.md)에 대 한 어플라이언스를 설정 합니다.
