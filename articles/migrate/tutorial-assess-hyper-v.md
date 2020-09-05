---
title: Azure Migrate를 사용하여 Hyper-V VM 평가 및 Azure로 마이그레이션 | Microsoft Docs
description: Azure Migrate 서버 평가를 사용하여 Azure로 마이그레이션할 온-프레미스 Hyper-V VM을 평가하는 방법을 설명합니다.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 4c95916bf5f92f8a82b9dfae50aa311891857e7a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86114247"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Azure Migrate 서버 평가를 사용하여 Hyper-V VM 평가

이 문서에서는 [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용하여 온-프레미스 Hyper-V VM을 평가하는 방법을 보여줍니다.


이 자습서는 Hyper-V VM을 평가하고 Azure로 마이그레이션하는 방법을 보여 주는 시리즈의 두 번째 자습서입니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Migrate 프로젝트를 설정합니다.
> * Azure Migrate 어플라이언스를 설정하고 등록합니다.
> * 온-프레미스 VM의 연속 검색을 시작합니다.
> * 검색된 VM을 그룹화하고 해당 그룹을 평가합니다.
> * 평가를 검토합니다.

> [!NOTE]
> 자습서에서는 개념 증명을 빠르게 설정할 수 있도록 시나리오에 대한 가장 간단한 배포 경로를 보여 줍니다. 자습서는 가능한 경우 기본 옵션을 사용하며, 가능한 모든 설정과 경로는 보여 주지 않습니다. 자세한 지침은 방법 문서를 검토하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.


## <a name="prerequisites"></a>필수 구성 요소

- 이 시리즈의 첫 번째 자습서를 [완료](tutorial-prepare-hyper-v.md)합니다. 그렇지 않으면 이 자습서의 지침이 작동하지 않습니다.
- 첫 번째 자습서에서 수행해야 하는 작업은 다음과 같습니다.
    - Azure Migrate를 사용할 수 있게 [Azure를 준비](tutorial-prepare-hyper-v.md#prepare-azure)합니다.
    - [Hyper-V 준비](tutorial-prepare-hyper-v.md#prepare-for-assessment) 호스트 및 VM 평가.
    - [Hyper-V 평가용 Azure Migrate 어플라이언스를 배포하기 위해 필요한 내용을 ](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment)확인합니다.

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate 프로젝트 설정

1. Azure Portal > **모든 서비스**에서 **Azure Migrate**를 검색합니다.
2. 검색 결과에서 **Azure Migrate**를 선택합니다.
3. **개요**의 **서버 검색, 평가 및 마이그레이션** 아래에서 **서버 평가 및 마이그레이션**을 클릭합니다.

    ![서버 검색 및 평가](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. **시작**에서**도구 추가**를 클릭합니다.
5. **프로젝트 마이그레이션** 탭에서 Azure 구독을 선택하고, 아직 없는 경우 리소스 그룹을 만듭니다.
6. **프로젝트 세부 정보**에서 프로젝트 이름과 프로젝트를 만들려는 지역을 지정합니다. [퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.

    - 프로젝트 지역은 온-프레미스 VM에서 수집된 메타데이터를 저장하는 데만 사용됩니다.
    - 다른 Azure 대상 지역은 VM을 마이그레이션할 때 선택할 수 있습니다. 모든 Azure 지역이 마이그레이션 대상으로 지원됩니다.

    ![Azure Migrate 프로젝트 만들기](./media/tutorial-assess-hyper-v/migrate-project.png)

7. **다음**을 클릭합니다.
8. **평가 도구 선택**에서 **Azure Migrate: 서버 평가** > **다음**을 차례로 선택합니다.

    ![Azure Migrate 프로젝트 만들기](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. **마이그레이션 도구 선택**에서 **마이그레이션 도구 추가 건너뛰기** > **다음**을 차례로 선택합니다.
10. **검토 + 도구 추가**에서 설정을 검토하고, **도구 추가**를 클릭합니다.
11. Azure Migrate 프로젝트가 배포될 때까지 몇 분 정도 기다립니다. 프로젝트 페이지로 이동합니다. 프로젝트가 표시되지 않으면 Azure Migrate 대시보드의 **서버**에서 액세스할 수 있습니다.

## <a name="set-up-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스 설정


Azure Migrate:Server Assessment는 간단한 Azure Migrate 어플라이언스를 사용합니다. 이 어플라이언스는 VM 검색을 수행하고, VM 메타데이터와 성능 데이터를 Azure Migrate에 보냅니다. 어플라이언스는 여러 가지 방법으로 설정할 수 있습니다.

- 다운로드한 Hyper-V VHD를 사용하여 Hyper-V VM에 설정합니다. 이는 이 자습서에서 사용하는 방법입니다.
- PowerShell 설치 관리자 스크립트를 사용하여 Hyper-V VM 또는 물리적 머신에 설정합니다. VHD를 사용하여 VM을 설정할 수 없거나 Azure Government에 있는 경우 [이 방법](deploy-appliance-script.md)을 사용해야 합니다.

어플라이언스를 만든 후 Azure Migrate:Server Assessment에 연결하여, 처음으로 구성하고, Azure Migrate 프로젝트에 등록할 수 있는지 확인합니다.

### <a name="download-the-vhd"></a>VHD 다운로드

어플라이언스에 대한 압축된 VHD 템플릿을 다운로드합니다.

1. **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 평가**에서 **검색**을 클릭합니다.
2. **머신 검색** > **머신이 가상화되어 있습니까?** 에서 **예, Hyper-V 사용**을 클릭합니다.
3. **다운로드**를 클릭하여 VHD 파일을 다운로드합니다.

    ![VM 다운로드](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.

2. 다음 PowerShell 명령을 실행하여 ZIP 파일에 대한 해시를 생성합니다.
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - 사용 예: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  최신 어플라이언스 버전 및 해시 값을 확인합니다.

    - Azure 퍼블릭 클라우드의 경우:

        **시나리오** | **다운로드** | **SHA256**
        --- | --- | ---
        Hyper-V(8.93GB) | [최신 버전](https://aka.ms/migrate/appliance/hyperv) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1

    - Azure Government의 경우:

        **시나리오*** | **다운로드** | **SHA256**
        --- | --- | ---
        Hyper-V(63.1MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae468934408e43ab55ff397b7da200b92121972e683f9aa3


### <a name="create-the-appliance-vm"></a>어플라이언스 VM 만들기

다운로드한 파일을 가져오고 VM을 만듭니다.

1. 어플라이언스 VM이 배치될 Hyper-V 호스트로 압축된 VHD 파일을 다운로드한 후 압축된 파일을 추출합니다.
    - 추출된 위치에서 파일은 **AzureMigrateAppliance_VersionNumber**라는 폴더로 압축을 풉니다.
    - 이 폴더에는 **AzureMigrateAppliance_VersionNumber**라고도 하는 하위 폴더가 있습니다.
    - 이 하위 폴더에는 세 개의 추가 하위 폴더인 **스냅샷**, **가상 하드 디스크** 및 **Virtual Machines**가 포함되어 있습니다.

2. Hyper-V 관리자를 엽니다. **작업**에서 **Virtual Machine 가져오기**를 클릭합니다.

    ![VHD 배포](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. Virtual Machine 가져오기 마법사 > **시작하기 전에**에서 **다음**을 클릭합니다.
3. **폴더 찾기**에서**Virtual Machines** 폴더를 선택합니다. 그런 후 **Next** 를 클릭합니다.
1. **Virtual Machine 선택**에서 **다음**을 클릭합니다.
2. **가져오기 유형 선택**에서 **가상 머신 복사(새로운 고유 ID 만들기)** 를 클릭합니다. 그런 후 **Next** 를 클릭합니다.
3. **대상 선택**에서 기본 설정을 그대로 둡니다. **다음**을 클릭합니다.
4. **스토리지 폴더**에서 기본 설정을 그대로 둡니다. **다음**을 클릭합니다.
5. **네트워크 선택**에서 VM에서 사용할 가상 스위치를 지정합니다. 이 스위치에는 데이터를 Azure로 보내기 위한 인터넷 연결이 필요합니다. 가상 스위치를 만드는 방법에 대해 [알아봅니다](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines).
6. **요약** 에서 설정을 검토합니다. **마침**을 클릭합니다.
7. Hyper-V 관리자 > **Virtual Machines**에서 VM을 시작합니다.


## <a name="verify-appliance-access-to-azure"></a>Azure에 대한 어플라이언스 액세스 확인

어플라이언스 VM에서 [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드의 Azure URL에 연결할 수 있는지 확인합니다.

### <a name="configure-the-appliance"></a>어플라이언스 구성

어플라이언스를 처음으로 설정합니다.

> [!NOTE]
> 다운로드한 VHD 대신 [PowerShell 스크립트](deploy-appliance-script.md)를 사용하여 어플라이언스를 설정하는 경우 이 절차의 처음 두 단계는 관련이 없습니다.

1. Hyper-V 관리자 > **Virtual Machines**에서 마우스 오른쪽 단추로 VM > **연결**을 클릭합니다.
2. 어플라이언스에 대한 언어, 표준 시간대 및 암호를 제공합니다.
3. VM에 연결할 수 있는 모든 머신에서 브라우저를 열고, 어플라이언스 웹앱의 URL(**https://*어플라이언스 이름 또는 IP 주소*: 44368**)을 엽니다.

   또는 앱 바로 가기를 클릭하여 어플라이언스 데스크톱에서 앱을 열 수 있습니다.
1. 웹앱 > **필수 구성 요소 설정**에서 다음을 수행합니다.
    - **라이선스**: 사용 조건에 동의하고 타사 정보를 읽습니다.
    - **연결**: 앱에서 VM이 인터넷에 액세스할 수 있는지 확인합니다. VM에서 프록시를 사용하는 경우:
      - **프록시 설정**을 클릭하고, 프록시 주소와 수신 포트를 http://ProxyIPAddress 또는 http://ProxyFQDN 형식으로 지정합니다.
      - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
      - HTTP 프록시만 지원됩니다.
    - **시간 동기화**: 시간이 확인됩니다. VM 검색이 제대로 작동하려면 어플라이언스의 시간이 인터넷 시간과 동기화되어야 합니다.
    - **업데이트 설치**: Azure Migrate 서버 평가에서 어플라이언스에 최신 업데이트가 설치되어 있는지 확인합니다.

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. **로그인**을 클릭합니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.
2. 새로 만들기 탭에서 Azure 자격 증명을 사용하여 로그인합니다.
    - 사용자 이름과 암호를 사용하여 로그인합니다.
    - PIN을 사용한 로그인은 지원되지 않습니다.
3. 성공적으로 로그인하면 웹앱으로 돌아갑니다.
4. Azure Migrate 프로젝트가 만들어진 구독을 선택합니다. 그런 다음, 해당 프로젝트를 선택합니다.
5. 어플라이언스의 이름을 지정합니다. 이름은 14자 이하의 영숫자여야 합니다.
6. **등록**을 클릭합니다.


### <a name="delegate-credentials-for-smb-vhds"></a>SMB VHD에 대한 자격 증명 위임

SMB에서 VHD를 실행하는 경우 자격 증명을 어플라이언스에서 Hyper-V 호스트로 위임하도록 설정해야 합니다. 이렇게 하려면 각 호스트가 어플라이언스에 대한 대리자로 작동하도록 설정합니다. 자습서를 순서대로 따랐다면 평가 및 마이그레이션을 위해 Hyper-V를 준비할 때 이전 자습서에서 이 작업을 수행했습니다. 호스트에 대한 CredSSP를 [수동으로](tutorial-prepare-hyper-v.md#enable-credssp-to-delegate-credentials) 설정하거나, 이를 수행하는 [스크립트를 실행](tutorial-prepare-hyper-v.md#run-the-script)하여 설정해야 합니다.

다음과 같이 어플라이언스에서 사용하도록 설정합니다.

#### <a name="option-1"></a>옵션 1

어플라이언스 VM에서 다음 명령을 실행합니다. HyperVHost1/HyperVHost2는 예제 호스트 이름입니다.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
```

예: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>옵션 2

또는 어플라이언스의 로컬 그룹 정책 편집기에서 다음을 수행합니다.

1. **로컬 컴퓨터 정책** > **컴퓨터구성**에서 **관리 템플릿** > **시스템** > **자격 증명 위임**을 차례로 클릭합니다.
2. **새로운 자격 증명 위임 허용**을 두 번 클릭하고 **사용**을 선택합니다.
3. **옵션**에서 **표시**를 클릭하고, **wsman/** 을 접두사로 사용하여 검색하려는 각 Hyper-V 호스트를 목록에 추가합니다.
4. 그런 다음, **자격 증명 위임**에서 **서버 인증이 NTLM 전용일 경우 새로운 자격 증명 허용**을 두 번 클릭합니다. **wsman/** 을 접두사로 사용하여 검색하려는 각 Hyper-V 호스트를 목록에 추가합니다.

## <a name="start-continuous-discovery"></a>연속 검색 시작

어플라이언스에서 Hyper-V 호스트 또는 클러스터에 연결하고, VM 검색을 시작합니다.

1. **사용자 이름** 및 **암호**에서 어플라이언스가 VM을 검색하는 데 사용할 읽기 전용 계정 자격 증명을 지정합니다. 친숙한 자격 증명 이름을 지정하고, **세부 정보 저장**을 클릭합니다.
2. **호스트 추가**를 클릭하고, Hyper-V 호스트/클러스터 세부 정보를 지정합니다.
3. **유효성 검사**를 클릭합니다. 유효성 검사가 완료되면 각 호스트/클러스터에서 검색할 수 있는 VM 수가 표시됩니다.
    - 호스트에 대한 유효성 검사가 실패하면 마우스로 **상태** 열의 아이콘 위를 가리켜서 오류를 검토합니다. 문제를 해결하고, 유효성을 다시 검사합니다.
    - 호스트 또는 클러스터를 제거하려면 **삭제**를 선택합니다.
    - 클러스터에서 특정 호스트를 제거할 수 없습니다. 전체 클러스터만 제거할 수 있습니다.
    - 클러스터의 특정 호스트에 문제가 있는 경우에도 클러스터를 추가할 수 있습니다.
4. 유효성 검사가 완료되면 **저장 및 검색 시작**을 클릭하여 검색 프로세스를 시작합니다.

그러면 검색을 시작합니다. 검색된 서버의 메타데이터가 Azure Portal에 표시되는 데 호스트당 약 1.5분이 걸립니다.

### <a name="verify-vms-in-the-portal"></a>포털에서 VM 확인

검색이 완료되면 Azure Portal에서 해당 VM이 표시되는지 확인할 수 있습니다.

1. Azure Migrate 대시보드를 엽니다.
2. **Azure Migrate - 서버** > **Azure Migrate: 서버 평가** 페이지에서 **검색된 서버**의 수를 표시하는 아이콘을 클릭합니다.

## <a name="set-up-an-assessment"></a>평가 설정

Azure Migrate 서버 평가를 사용하여 실행할 수 있는 평가에는 두 가지 유형이 있습니다.

**평가** | **세부 정보** | **Data**
--- | --- | ---
**성능 기반** | 수집된 성능 데이터를 기반으로 하는 평가 | **추천 VM 크기**: CPU 및 메모리 사용률 데이터를 기반으로 합니다.<br/><br/> **추천 디스크 유형(표준 또는 프리미엄 관리 디스크)** : 온-프레미스 디스크의 IOPS 및 처리량을 기반으로 합니다.
**온-프레미스로** | 온-프레미스 크기 조정을 기반으로 하는 평가 | **추천 VM 크기**: 온-프레미스 VM 크기를 기반으로 합니다.<br/><br> **추천 디스크 유형**: 평가를 위해 선택한 스토리지 유형 설정을 기반으로 합니다.



### <a name="run-an-assessment"></a>평가 실행

다음과 같이 평가를 실행합니다.

1. 평가를 만드는 방법에 대한 [모범 사례](best-practices-assessment.md)를 검토합니다.
2. **서버** > **Azure Migrate: 서버 평가**에서 **평가**를 클릭합니다.

    ![평가](./media/tutorial-assess-hyper-v/assess.png)

3. **서버 평가**에서 평가 이름을 지정합니다.
4. 평가 속성을 검토하려면 **모두 보기**를 클릭합니다.

    ![평가 속성](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. **그룹 선택 또는 만들기**에서 **새로 만들기**를 선택하고 그룹 이름을 지정합니다. 그룹은 평가를 위해 하나 이상의 VM을 수집합니다.
4. **그룹에 머신 추가**에서 그룹에 추가할 VM을 선택합니다.
5. **평가 만들기**를 클릭하여 그룹을 만들고, 평가를 실행합니다.

    ![평가 만들기](./media/tutorial-assess-hyper-v/assessment-create.png)

6. 평가가 만들어지면 **서버** > **Azure Migrate: 서버 평가**에서 해당 평가를 확인합니다.
7. **평가 내보내기**를 클릭하고, Excel 파일로 다운로드합니다.


## <a name="review-an-assessment"></a>평가 검토

평가에서 설명하는 항목은 다음과 같습니다.

- **Azure 준비 상태**: VM이 Azure로 마이그레이션하는 데 적합한지 여부입니다.
- **월간 예상 비용**: Azure에서 VM을 실행하는 데 들어가는 월간 예상 컴퓨팅 및 스토리지 비용입니다.
- **월간 예상 스토리지 비용**: 마이그레이션 후 디스크 스토리지에 대한 예상 비용입니다.


### <a name="view-an-assessment"></a>평가 보기

1. **마이그레이션 목표** >  **서버** > **Azure Migrate: 서버 평가**에서 **평가**를 클릭합니다.
2. **평가**에서 해당 평가를 클릭하여 엽니다.

    ![평가 요약](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Azure 준비 상태 검토

1. **Azure 준비 상태**에서 VM이 Azure로 마이그레이션할 준비가 되었는지 확인합니다.
2. VM 상태를 검토합니다.
    - **Azure 준비 완료**: Azure Migrate는 평가에서 VM의 크기 및 예상 비용을 추천합니다.
    - **조건과 함께 준비 완료**: 문제 및 제안된 수정 사항을 보여 줍니다.
    - **Azure를 사용할 준비 안 됨**: 문제 및 제안된 수정 사항을 보여 줍니다.
    - **알 수 없는 준비**: 데이터 가용성 문제로 인해 Azure Migrate에서 준비 상태를 평가할 수 없는 경우에 사용됩니다.

2. **Azure 준비 상태** 상태를 클릭합니다. VM 준비 상태 세부 정보를 살펴보고, 드릴다운하여 컴퓨팅, 스토리지 및 네트워크 설정을 포함한 VM 세부 정보를 확인할 수 있습니다.

### <a name="review-cost-details"></a>비용 세부 정보 검토

이 보기에서는 Azure에서 VM을 실행하는 데 들어가는 예상 컴퓨팅 및 스토리지 비용을 표시합니다.

1. 월간 컴퓨팅 및 스토리지 비용을 검토합니다. 비용은 평가된 그룹의 모든 VM에 대해 집계됩니다.

    - 예상 비용은 머신의 크기 추천 사항과 해당 디스크 및 속성을 기반으로 합니다.
    - 컴퓨팅 및 스토리지의 월간 예상 비용이 표시됩니다.
    - 예상 비용은 온-프레미스 VM을 IaaS VM으로 실행하는 데 들어가는 비용입니다. Azure Migrate 서버 평가에서는 PaaS 또는 SaaS 비용을 고려하지 않습니다.

2. 월간 예상 스토리지 비용을 검토할 수 있습니다. 이 보기에는 평가된 그룹에 대해 집계된 스토리지 비용이 표시되며, 해당 그룹은 여러 유형의 스토리지 디스크로 분할되어 있습니다.
3. 드릴다운하여 특정 VM에 대한 세부 정보를 확인할 수 있습니다.


### <a name="review-confidence-rating"></a>신뢰 등급 검토

성능 기반 평가를 실행하면 신뢰 등급이 평가에 할당됩니다.

![신뢰 등급](./media/tutorial-assess-hyper-v/confidence-rating.png)

- 별 1개(최저)에서 별 5개(최고)까지의 등급입니다.
- 신뢰 등급을 통해 평가에서 제공하는 크기 추천 사항의 안정성을 예측할 수 있습니다.
- 신뢰 등급은 평가를 계산하는 데 필요한 데이터 요소의 가용성을 기반으로 합니다.

평가에 대한 신뢰 등급은 다음과 같습니다.

**데이터 요소 가용성** | **신뢰 등급**
--- | ---
0%-20% | 별 1개
21%-40% | 별 2개
41%-60% | 별 3개
61%-80% | 별 4개
81%-100% | 별 5개

신뢰 등급의 모범 사례에 대해 [자세히 알아보세요](best-practices-assessment.md#best-practices-for-confidence-ratings).





## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure Migrate 어플라이언스 설정
> * 평가 만들기 및 검토

Azure Migrate 서버 마이그레이션을 사용하여 Hyper-V VM을 Azure로 마이그레이션하는 방법을 알아보려면 이 시리즈의 세 번째 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Hyper-V VM 마이그레이션](./tutorial-migrate-hyper-v.md)
