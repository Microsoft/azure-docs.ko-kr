---
title: StorSimple Virtual Array를 파일 서버로 설정 | Microsoft Docs
description: StorSimple 가상 배열 배포의 세 번째 자습서는 가상 디바이스를 파일 서버로 설정하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a931b303e40e41bc23e8b586e1d37e600625b1a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61415095"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>StorSimple 가상 배열 배포 - Azure Portal을 통해 파일 서버로 설정
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>소개
초기 설정을 수행하고, StorSimple 파일 서버를 등록하고, 디바이스 설정을 완료하고, SMB 공유를 만들고 연결하는 방법을 설명합니다. 가상 배열을 파일 서버 또는 iSCSI 서버로 완전히 배포하는 데 필요한 배포 자습서 시리즈의 마지막 문서입니다.

설정 및 구성 프로세스를 완료하는 데 10분 정도가 소요됩니다. 이 문서의 정보는 StorSimple 가상 배열의 배포에만 적용됩니다. StorSimple 8000 시리즈 디바이스 배포의 경우 [업데이트 2를 실행하는 StorSimple 8000 시리즈 디바이스 배포](storsimple-deployment-walkthrough-u2.md)로 이동합니다.

## <a name="setup-prerequisites"></a>설정 필수 조건
StorSimple 가상 배열을 구성하고 설정하기 전에 다음 사항을 확인합니다.

* [Hyper-V에서 StorSimple 가상 배열 프로비전](storsimple-virtual-array-deploy2-provision-hyperv.md) 또는 [VMware에서 StorSimple 가상 배열 프로비전](storsimple-virtual-array-deploy2-provision-vmware.md)에 설명된 대로 가상 배열을 프로비전하고 연결했습니다.
* StorSimple 가상 배열을 관리하려고 만든 StorSimple 디바이스 관리자 서비스의 서비스 등록 키가 있습니다. 자세한 내용은 StorSimple 가상 배열의 [2단계: 서비스 등록 키 받기](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)를 참조하세요.
* 기존 StorSimple 디바이스 관리자 서비스에 가상 배열을 두 번째 또는 후속으로 등록하는 경우에는 서비스 데이터 암호화 키가 있어야 합니다. 이 키는 이 서비스에 첫 번째 디바이스가 등록될 때 생성되었습니다. 이 키를 잃어버린 경우에는 StorSimple 가상 배열의 [서비스 데이터 암호화 키 가져오기](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) 를 참조하세요.

## <a name="step-by-step-setup"></a>단계별 설정
다음 단계별 지침을 사용하여 StorSimple 가상 배열을 설정 및 구성합니다.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>1단계: 로컬 웹 UI 설정을 완료하고 디바이스 등록
#### <a name="to-complete-the-setup-and-register-the-device"></a>설정을 완료하고 디바이스를 등록하려면
1. 브라우저 창을 열고 로컬 웹 UI에 연결합니다. 형식:
   
   `https://<ip-address of network interface>`
   
   이전 단계에서 언급한 연결 URL을 사용합니다. 웹 사이트의 보안 인증서에 문제가 있음을 나타내는 오류가 표시됩니다. **이 웹 페이지에서 계속 진행**을 클릭합니다.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. 가상 배열의 웹 UI에 **StorSimpleAdmin**으로 로그인합니다. 그런 후 [Hyper-V에서 StorSimple 가상 배열 프로비전](storsimple-virtual-array-deploy2-provision-hyperv.md) 또는 [VMware에서 StorSimple 가상 배열 프로비전](storsimple-virtual-array-deploy2-provision-vmware.md)의 3단계: 가상 배열 시작에서 변경한 디바이스 관리자 암호를 입력합니다.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. **홈** 페이지로 이동합니다. 이 페이지는 StorSimple 디바이스 관리자 서비스에 가상 배열을 구성하고 등록하는 데 필요한 다양한 설정을 설명합니다. **네트워크 설정**, **웹 프록시 설정**, **시간 설정**은 선택 사항입니다. 필요한 설정은 **디바이스 설정** 및 **클라우드 설정**입니다.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. **네트워크 설정** 페이지의 **네트워크 인터페이스**에서 DATA 0이 자동으로 구성됩니다. 각 네트워크 인터페이스는 IP 주소를 자동으로 가져오도록(DHCP) 기본 설정됩니다. 따라서 IP 주소, 서브넷 및 게이트웨이가 자동으로 할당됩니다(IPv4 및 IPv6 모두에 대해).
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   디바이스를 프로비전하는 동안 네트워크 인터페이스를 둘 이상 추가한 경우에는 여기에서 구성할 수 있습니다. 네트워크 인터페이스를 IPv4로만 구성하거나 IPv4와 IPv6 둘 다로 구성할 수 있습니다. IPv6 전용 구성은 지원되지 않습니다.
5. 디바이스가 클라우드 저장소 서비스 공급자와 통신하려고 시도하거나 파일 서버로 구성된 경우 이름으로 디바이스를 확인하려고 시도하는 경우에 DNS 서버가 사용되기 때문에 DNS 서버가 필요합니다. **네트워크 설정** 페이지의 **DNS 서버** 아래에:
   
   1. 기본 및 보조 DNS 서버는 자동으로 구성됩니다. 고정 IP 주소를 구성하도록 선택하면 DNS 서버를 지정할 수 있습니다. 고가용성을 위해 기본 및 보조 DNS 서버를 구성하는 것이 좋습니다.
   2. **적용**을 클릭하면 네트워크 설정이 적용되고 유효성 검사가 수행됩니다.
6. **디바이스 설정** 페이지에서:
   
   1. 디바이스에 고유한 **이름** 을 할당합니다. 이름에는 1-15자를 사용할 수 있으며 문자, 숫자 및 하이픈을 포함할 수 있습니다.
   2. 만드는 디바이스의 **유형**에 대해 **파일 서버** 아이콘 ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png)을 클릭합니다. 파일 서버를 사용하여 공유 폴더를 만들 수 있습니다.
   3. 디바이스가 파일 서버이므로 디바이스를 도메인에 가입해야 합니다. **도메인 이름**을 입력합니다.
   4. **적용**을 클릭합니다.
7. 대화 상자가 표시됩니다. 지정된 형식으로 도메인 자격 증명을 입력합니다. 확인 아이콘을 클릭합니다. 도메인 자격 증명이 확인됩니다. 자격 증명이 올바르지 않으면 오류 메시지가 표시됩니다.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. **적용**을 클릭합니다. 디바이스 설정이 적용되고 유효성 검사가 수행됩니다.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > 가상 배열이 Active Directory용 자체 OU(조직 구성 단위)에 있으며 GPO(그룹 정책 개체)가 적용되었거나 상속되지 않았는지 확인합니다. 그룹 정책은 StorSimple 가상 배열에 바이러스 백신 소프트웨어와 같은 애플리케이션을 설치할 수 있습니다. 추가 소프트웨어를 설치하는 것은 지원되지 않으며 데이터 손상을 초래할 수 있습니다. 
   > 
   > 
9. 선택적으로 웹 프록시 서버를 구성합니다. 웹 프록시 구성은 선택 사항이지만 웹 프록시를 사용하면 여기서만 구성할 수 있습니다.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   **웹 프록시** 페이지에서:
   
   1. **웹 프록시 URL**을 *http://&lt;호스트 IP 주소 또는 FQDN&gt;:포트 번호* 형식으로 입력합니다. HTTPS URL은 지원되지 않습니다.
   2. **인증**은 **기본** 또는 **없음**으로 지정합니다.
   3. 인증을 사용하는 경우 **사용자 이름** 및 **암호**를 입력해야 합니다.
   4. **적용**을 클릭합니다. 구성된 웹 프록시 설정의 유효성을 검사하고 적용합니다.
10. (선택 사항) 디바이스에 대한 시간 설정(예: 표준 시간대 및 기본 및 보조 NTP 서버)을 구성합니다. 클라우드 서비스 공급자와 인증할 수 있도록 디바이스 시간을 동기화해야 하기 때문에 NTP 서버가 필요합니다.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    **시간 설정** 페이지에서:
    
    1. 드롭다운 목록에서 디바이스가 배포되는 지리적 위치를 기반으로 **표준 시간대** 를 설정합니다. 디바이스의 기본 표준 시간대는 PST입니다. 디바이스는 모든 예약된 작업에 대해 이 표준 시간대를 사용합니다.
    2. 디바이스에 **기본 NTP 서버** 를 지정하거나 time.windows.com의 기본값을 적용합니다. 네트워크에서 NTP 트래픽이 데이터 센터에서 인터넷으로 전달되도록 허용하는지 확인합니다.
    3. 선택적으로 디바이스에 대한 **보조 NTP 서버**를 지정합니다.
    4. **Apply**를 클릭합니다. 구성된 시간 설정의 유효성을 검사하고 적용합니다.
11. 디바이스에 대한 클라우드 설정을 구성합니다. 이 단계에서는 로컬 디바이스 구성을 완료한 다음, StorSimple 디바이스 관리자 서비스에 디바이스를 등록합니다.
    
    1. **서비스 등록 키**(StorSimple 가상 배열의 [2단계: 서비스 등록 키 받기](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)에서 가져옴)를 입력합니다.
    2. 서비스에 디바이스를 처음으로 등록하는 경우 **서비스 데이터 암호화 키**가 표시됩니다. 이 키를 복사하고 안전한 위치에 저장합니다. 이 키는 StorSimple 디바이스 관리자 서비스에 추가 디바이스를 등록하기 위한 서비스 등록 키에 필요합니다. 
       
       서비스에 디바이스를 처음으로 등록하는 경우가 아니라면 서비스 데이터 암호화 키를 제공해야 합니다. 자세한 내용은 로컬 웹 UI의 [서비스 데이터 암호화 키 받기](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) 를 참조하세요.
    3. **등록**을 클릭합니다. 디바이스가 다시 시작됩니다. 디바이스 등록이 완료되기까지 2-3분 정도 기다려야 할 수 있습니다. 디바이스가 다시 시작된 후 로그인 페이지가 열립니다.
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Azure Portal로 돌아갑니다. **모든 리소스**로 이동하여 StorSimple 디바이스 관리자 서비스를 검색합니다.
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. 필터링된 목록에서 StorSimple 디바이스 관리자 서비스를 선택한 다음 **관리 &gt; 디바이스**로 이동합니다. **디바이스** 블레이드에서 디바이스가 서비스에 성공적으로 연결되었는지와 **설정할 준비 완료** 상태인지 확인합니다.
    
    ![파일 서버 구성](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>2단계: 디바이스를파일 서버로 구성
필요한 디바이스 설정을 완료하려면 [Azure Portal](https://portal.azure.com/)에서 다음 단계를 수행합니다.

#### <a name="to-configure-the-device-as-file-server"></a>파일 서버로 디바이스를 구성하려면
1. StorSimple 디바이스 관리자 서비스로 이동한 다음 **관리 &gt; 디바이스**로 이동합니다. **디바이스** 블레이드에서 방금 만든 디바이스를 선택합니다. 이 디바이스는 **설정할 준비 완료**로 표시됩니다.
   
   ![파일 서버 구성](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. 디바이스를 클릭하고 디바이스를 설치할 준비가 되었음을 나타내는 배너 메시지가 표시됩니다.
   
    ![파일 서버 구성](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. 명령 모음에서 **구성**을 클릭합니다. 그러면 **구성** 블레이드가 열립니다. **구성** 블레이드에서 다음을 수행합니다.
   
   1. 파일 서버 이름은 자동으로 채워집니다.
    
   2. 클라우드 저장소 암호화가 **사용**으로 설정되었는지 확인합니다. 이 클라우드로 전송된 모든 데이터를 암호화합니다. 
    
   3. 암호화를 위한 사용자 정의 키에 256비트 AES 키가 사용됩니다. 32개의 문자 키를 지정하고 확인을 위해 키를 다시 입력합니다. 나중에 참조할 수 있도록 키 관리 앱에서 키를 기록합니다.
    
   4. **필수 설정 구성**을 클릭하여 디바이스와 함께 사용할 저장소 계정 자격 증명을 지정합니다. 구성된 저장소 계정 자격 증명이 없는 경우 **새로 추가**를 클릭합니다. **사용하는 저장소 계정이 블록 Blob을 지원하는지 확인합니다. 페이지 Blob은 지원되지 않습니다.** [블록 Blob 및 페이지 Blob에 대한](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 자세한 내용입니다.
   
      ![파일 서버 구성](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. **저장소 계정 자격 증명 추가** 블레이드에서 다음을 수행합니다. 

    1. 저장소 계정이 서비스와 동일한 구독에 있는 경우 현재 구독을 선택합니다. 서비스 구독 외부에서 기타 저장소 계정을 지정합니다. 
    
    2. 드롭다운 목록에서 기존 저장소 계정을 선택합니다. 
    
    3. 위치는 지정된 저장소 계정에 따라 자동으로 채워지게 됩니다. 
    
    4. 디바이스와 클라우드 간에 보안 네트워크 통신 채널을 확보하려면 SSL을 사용하도록 설정합니다.
    
    5. **추가**를 클릭하여 이 저장소 계정 자격 증명을 추가합니다. 
   
        ![파일 서버 구성](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. 저장소 계정 자격 증명이 성공적으로 만들어지면 **구성** 블레이드가 업데이트되어 지정된 저장소 계정 자격 증명을 표시합니다. **구성**을 클릭합니다.
   
   ![파일 서버 구성](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   만들어진 파일 서버가 표시됩니다. 서비스가 성공적으로 만들어지면 알림이 표시됩니다.
   
   ![파일 서버 구성](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   디바이스 상태도 **온라인**으로 변경됩니다.
   
   ![파일 서버 구성](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   공유를 추가하는 작업을 진행할 수 있습니다.

## <a name="step-3-add-a-share"></a>3단계: 공유 추가
공유를 만들려면 [Azure Portal](https://portal.azure.com/)에서 다음 단계를 수행합니다.

#### <a name="to-create-a-share"></a>공유를 만들려면
1. 이전 단계에서 구성한 파일 서버 디바이스를 선택하고 **...** 를 클릭(하거나 마우스 오른쪽 단추로 클릭)합니다. 상황에 맞는 메뉴에서 **공유 추가**를 선택합니다. 또는 디바이스 명령 모음에서 **+공유 추가**를 클릭할 수 있습니다.
   
   ![공유 추가](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. 다음 공유 설정을 지정합니다.

   1. 공유에 대한 고유 이름입니다. 이름은 3~127개의 문자를 포함하는 문자열이어야 합니다.
    
   2. 공유에 대한 선택적 **설명**입니다. 설명은 공유 소유자를 식별하는 데 도움이 됩니다.
    
   3. 공유의 **유형**입니다. 유형에는 **계층화됨** 또는 **로컬로 고정**을 지정할 수 있으며 계층화됨이 기본값입니다. 로컬 보증, 낮은 대기 시간 및 높은 성능이 필요한 워크로드의 경우 **로컬로 고정** 공유를 선택합니다. 다른 모든 데이터에 대해서는 **계층화됨** 공유를 선택합니다.
      로컬로 고정된 공유는 씩 프로비전되며, 공유의 기본 데이터가 디바이스에 로컬로 유지되고 클라우드로 유출되지 않습니다. 반면에 계층화된 공유는 씬 프로비전됩니다. 계층화된 공유를 만들 때 공간의 10%는 로컬 계층에 프로비전되고 공간의 90%는 클라우드에 프로비전됩니다. 예를 들어, 1TB 볼륨을 프로비전하는 경우 100GB는 로컬 공간에 상주하고 900GB는 데이터가 계층화될 때 클라우드에서 사용됩니다. 이것은 디바이스의 로컬 공간이 부족하면 계층화된 공유를 프로비전할 수 없다는 것을 의미합니다.
   
   4. **전체 기본 사용 권한 설정** 필드에서 공유에 액세스할 사용자 또는 그룹에 권한을 할당합니다. 사용자 또는 사용자 그룹의 이름을 지정 *john\@contoso.com* 형식입니다. 이 공유에 액세스하는 관리자 권한은 사용자 그룹(단일 사용자 대신)을 사용하여 허용하는 것이 좋습니다. 여기에서 권한을 할당한 후에 파일 탐색기를 사용하여 해당 권한을 수정할 수 있습니다.
   
   5. **추가**를 클릭하여 공유를 만듭니다. 
    
       ![공유 추가](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       공유 만들기가 진행 중이라는 알림이 표시됩니다.
   
       ![공유 추가](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      지정된 설정으로 공유를 만든 후에 **공유** 블레이드가 업데이트되어 새 공유를 반영합니다. 기본적으로 공유에 대한 모니터링 및 백업을 사용하도록 설정됩니다.
   
      ![공유 추가](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>4단계: 공유에 연결
이전 단계에서 만든 하나 이상의 공유에 연결해야 합니다. StorSimple 가상 배열에 연결된 Windows Server 호스트에서 이러한 단계를 수행합니다.

#### <a name="to-connect-to-the-share"></a>공유에 연결하려면
1. ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R 키를 누릅니다. 실행 창에서 *&amp;#92;&amp;#92;&lt;파일 서버 이름&gt;* 으로 경로를 지정하고, *파일 서버 이름*을 파일 서버에 할당한 디바이스 이름으로 변경합니다. **확인**을 클릭합니다.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. 그러면 파일 탐색기가 열립니다. 폴더로 만든 공유가 표시됩니다. 공유(폴더)를 선택하고 두 번 클릭하여 콘텐츠를 봅니다.
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. 이제 공유에 파일을 추가하고 백업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계
로컬 웹 UI를 사용하여 [StorSimple 가상 배열을 관리](storsimple-ova-web-ui-admin.md)하는 방법을 알아봅니다.

