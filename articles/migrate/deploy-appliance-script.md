---
title: 스크립트를 사용 하 여 Azure Migrate 어플라이언스 설정
description: 스크립트를 사용 하 여 Azure Migrate 어플라이언스를 설정 하는 방법을 알아봅니다.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 43e47c6b4121a2f389dfec27873b90b7031c4f2c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929978"
---
# <a name="set-up-an-appliance-with-a-script"></a>스크립트를 사용 하 여 어플라이언스 설정

이 문서에 따라 VMware Vm 및 Hyper-v Vm의 평가/마이그레이션에 대 한 [Azure Migrate 어플라이언스](./migrate-appliance-architecture.md) 를 만들 수 있습니다. 스크립트를 실행 하 여 어플라이언스를 만들고 Azure에 연결할 수 있는지 확인 합니다. 

스크립트를 사용 하거나 Azure Portal에서 다운로드 한 템플릿을 사용 하 여 VMware 및 Hyper-v Vm에 대 한 어플라이언스를 배포할 수 있습니다. 다운로드 한 템플릿을 사용 하 여 VM을 만들 수 없는 경우 스크립트를 사용 하는 것이 유용 합니다.

- 템플릿을 사용 하려면 [VMware](tutorial-prepare-vmware.md) 또는 [hyper-v](tutorial-prepare-hyper-v.md)에 대 한 자습서를 따르세요.
- 물리적 서버용 어플라이언스를 설정 하려면 스크립트만 사용 하면 됩니다. [이 문서](how-to-set-up-appliance-physical.md)를 따릅니다.
- Azure Government 클라우드에서 어플라이언스를 설정 하려면 [이 문서](deploy-appliance-script-government.md)를 따릅니다.

## <a name="prerequisites"></a>필수 구성 요소

이 스크립트는 기존 물리적 컴퓨터 또는 VM에 Azure Migrate 어플라이언스를 설정 합니다.

- 어플라이언스 역할을 하는 컴퓨터는 다음 하드웨어 및 OS 요구 사항을 충족 해야 합니다.

시나리오 | 요구 사항
--- | ---
VMware | Windows Server 2016, 32 GB 메모리, 8 개 vCPUs 80, g b의 디스크 저장소
Hyper-V | Windows Server 2016, 16gb 메모리, 8 개 vCPUs, 80 GB의 디스크 저장소
- 컴퓨터에는 외부 가상 스위치도 필요 합니다. 고정 또는 동적 IP 주소와 인터넷에 대 한 액세스 권한이 필요 합니다.
- 어플라이언스를 배포 하기 전에 [VMware vm](migrate-appliance.md#appliance---vmware), [hyper-v vm](migrate-appliance.md#appliance---hyper-v)에 대 한 자세한 어플라이언스 요구 사항을 검토 하세요.
- 기존 Azure Migrate 어플라이언스에서 스크립트를 실행 하지 마세요.

## <a name="set-up-the-appliance-for-vmware"></a>VMware에 대 한 어플라이언스 설정

VMware에 대 한 어플라이언스를 설정 하려면 포털에서 또는 [여기](https://go.microsoft.com/fwlink/?linkid=2140334)에서 AzureMigrateInstaller-Server-Public.zip 이라는 zip 파일을 다운로드 하 고 콘텐츠를 추출 합니다. PowerShell 스크립트를 실행 하 여 어플라이언스 웹 앱을 시작 합니다. 어플라이언스를 설정 하 고 처음으로 구성 합니다. 그런 다음 Azure Migrate 프로젝트에 어플라이언스를 등록 합니다.


### <a name="verify-file-security"></a>파일 보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256```
3. Azure 공용 클라우드의 최신 어플라이언스 버전 및 스크립트를 확인 합니다.

    **알고리즘** | **다운로드** | **SHA256**
    --- | --- | ---
    VMware (85 MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140334) | 5d0a3dbce4b5010980d59d49859f809acfeb17f5a36f57af4dac44a0a62dde1f



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

1. 어플라이언스를 호스트 하는 컴퓨터의 폴더에 압축 파일 압축을 풉니다. 기존 Azure Migrate 어플라이언스의 머신에서 스크립트를 실행하지 않아야 합니다.
2. 관리자 권한 (관리자 권한)을 사용 하 여 컴퓨터에서 PowerShell을 시작 합니다.
3. PowerShell 디렉터리를 다운로드 한 압축 파일에서 추출한 콘텐츠를 포함 하는 폴더로 변경 합니다.
4. 다음과 같이 스크립트 **AzureMigrateInstaller.ps1**를 실행 합니다.

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. 스크립트가 성공적으로 실행 되 면 어플라이언스를 설정할 수 있도록 어플라이언스 웹 응용 프로그램을 시작 합니다. 문제가 발생 하는 경우 C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그를 검토 합니다.

### <a name="verify-access"></a>액세스 권한 확인

어플라이언스를 [공용](migrate-appliance.md#public-cloud-urls) 클라우드의 Azure url에 연결할 수 있는지 확인 합니다.

## <a name="set-up-the-appliance-for-hyper-v"></a>Hyper-v에 대 한 어플라이언스 설정

Hyper-v에 대 한 어플라이언스를 설정 하려면 포털 또는 [여기](https://go.microsoft.com/fwlink/?linkid=2105112)에서 AzureMigrateInstaller-Server-Public.zip 이라는 zip 파일을 다운로드 하 고 콘텐츠를 추출 합니다. PowerShell 스크립트를 실행 하 여 어플라이언스 웹 앱을 시작 합니다. 어플라이언스를 설정 하 고 처음으로 구성 합니다. 그런 다음 Azure Migrate 프로젝트에 어플라이언스를 등록 합니다.


### <a name="verify-file-security"></a>파일 보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256```

3. Azure 공용 클라우드의 최신 어플라이언스 버전 및 스크립트를 확인 합니다.

    **시나리오** | **다운로드** | **SHA256**
    --- | --- | ---
    Hyper-v (85) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140334) |  5d0a3dbce4b5010980d59d49859f809acfeb17f5a36f57af4dac44a0a62dde1f

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

1. 어플라이언스를 호스트 하는 컴퓨터의 폴더에 압축 파일 압축을 풉니다. 기존 Azure Migrate 어플라이언스의 머신에서 스크립트를 실행하지 않아야 합니다.
2. 관리자 권한 (관리자 권한)을 사용 하 여 컴퓨터에서 PowerShell을 시작 합니다.
3. PowerShell 디렉터리를 다운로드 한 압축 파일에서 추출한 콘텐츠를 포함 하는 폴더로 변경 합니다.
4. 다음과 같이 스크립트 **AzureMigrateInstaller.ps1**를 실행 합니다. 

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. 스크립트가 성공적으로 실행 되 면 어플라이언스를 설정할 수 있도록 어플라이언스 웹 응용 프로그램을 시작 합니다. 문제가 발생 하는 경우 C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그를 검토 합니다.

### <a name="verify-access"></a>액세스 권한 확인

어플라이언스를 [공용](migrate-appliance.md#public-cloud-urls) 클라우드의 Azure url에 연결할 수 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

어플라이언스를 배포한 후에는 처음으로 구성 하 고 Azure Migrate 프로젝트에 등록 해야 합니다.

- [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance)에 대 한 어플라이언스를 설정 합니다.
- [Hyper-v](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)에 대 한 어플라이언스를 설정 합니다.
