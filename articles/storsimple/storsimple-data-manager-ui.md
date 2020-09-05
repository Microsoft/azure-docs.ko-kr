---
title: Microsoft Azure StorSimple Data Manager UI
description: StorSimple Data Manager UI를 사용 하 여 StorSimple 8000 시리즈 장치에 있는 데이터를 변환 하는 방법을 알아봅니다.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 5d6273cbef1664984d466fb15c7f02ca505b18e2
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88184542"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Azure Portal에서 StorSimple 데이터 관리자 서비스 관리

이 문서에서는 StorSimple 데이터 관리자 UI를 사용하여 StorSimple 8000 시리즈 디바이스에 있는 데이터를 변환하는 방법을 설명합니다. 변환 된 데이터는 Azure Media Services, Azure HDInsight, Azure Machine Learning, Azure Cognitive Search 등의 다른 Azure 서비스에서 사용 될 수 있습니다.


## <a name="use-storsimple-data-transformation"></a>StorSimple 데이터 변환 사용

StorSimple 데이터 관리자는 데이터 변환이 인스턴스화되는 리소스입니다. 데이터 변환 서비스를 사용하여 StorSimple 형식에서 Blob 또는 Azure Files의 네이티브 형식으로 데이터를 변환할 수 있습니다. StorSimple 네이티브 형식 데이터를 변환하려면 StorSimple 8000 시리즈 디바이스에 대한 세부 정보와 변환하려는 관심 있는 데이터에 대한 세부 정보를 지정해야 합니다.

### <a name="create-a-storsimple-data-manager-service"></a>StorSimple 데이터 관리자 서비스 만들기

StorSimple 데이터 관리자 서비스를 만들려면 다음 단계를 수행합니다.

1. Microsoft 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그온합니다.

2. **+ 리소스 만들기**를 클릭하고 StorSimple 데이터 관리자를 검색합니다.

    ![StorSimple 데이터 관리자 서비스 만들기 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. StorSimple 데이터 관리자를 클릭하고 **만들기**를 클릭합니다.
    
    ![StorSimple 데이터 관리자 서비스 만들기 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. 새 서비스의 경우 다음을 지정합니다.

   1. StorSimple 데이터 관리자에 대한 고유한 **서비스 이름**을 제공합니다. 이 이름은 서비스를 식별하는 데 사용할 수 있는 친숙한 이름입니다. 이 이름은 2~24자 사이의 문자, 숫자 및 하이픈일 수 있습니다. 이름은 문자 또는 숫자로 시작하고 끝나야 합니다.

   2. 드롭다운 목록에서 **구독** 을 선택 합니다. 구독은 대금 청구 계정에 연결됩니다. 구독이 하나만 있는 경우 이 필드가 자동으로 채워집니다(선택할 수 없음).

   3. 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다. 자세한 내용은 [Azure 리소스 그룹](/azure/azure-resource-manager/management/manage-resource-groups-portal)을 참조하세요.

   4. 스토리지 계정 및 StorSimple 데이터 관리자 서비스를 저장하는 서비스의 **위치**를 지정합니다. StorSimple 디바이스 관리자 서비스, 데이터 관리자 서비스 및 연결된 스토리지 계정은 모두 지원되는 지역에 있어야 합니다.
    
   5. 이 서비스에 대한 링크를 대시보드로 가져오려면 **대시보드에 고정**을 선택합니다.
    
   6. **만들기**를 클릭합니다.

      ![StorSimple 데이터 관리자 서비스 만들기 3](./media/storsimple-data-manager-ui/create-service-4.png)

서비스 생성은 몇 분 정도가 소요됩니다. 서비스가 성공적으로 만들어지면 알림이 표시되고 새 서비스가 표시됩니다.

### <a name="create-a-data-transformation-job-definition"></a>데이터 변환 작업 정의 만들기

StorSimple 데이터 관리자 서비스 내에서 데이터 변환 작업 정의를 만들어야 합니다. 작업 정의는 스토리지 계정에 이동하는 데 관심이 있는 StorSimple 데이터의 세부 정보를 네이티브 형식으로 지정합니다. 작업 정의를 만든 후에 다른 런타임 설정을 사용하여 이 작업을 다시 실행할 수 있습니다.

다음 단계를 수행하여 작업 정의를 만듭니다.

1. 만든 서비스로 이동합니다. **관리 > 작업 정의**로 이동합니다.

2. **+ 작업 정의**를 클릭 합니다.

    ![+작업 정의 클릭](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. 작업 정의의 이름을 제공합니다. 이름은 3자에서 63자 사이일 수 있습니다. 이름은 대/소문자, 숫자 및 하이픈을 포함할 수 있습니다.

4. 작업이 실행되는 위치를 지정합니다. 이 위치는 서비스가 배포되는 위치와 다를 수 있습니다.

5. **원본**을 클릭하여 원본 데이터 저장소를 지정합니다.

    ![원본 데이터 리포지토리 구성](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. 새 데이터 관리자 서비스이기 때문에 데이터 리포지토리가 구성되지 않습니다. **데이터 원본 구성**에서 StorSimple 8000 시리즈 디바이스 및 관심 있는 데이터에 대한 세부 정보를 지정합니다.

   StorSimple 디바이스 관리자를 데이터 리포지토리로 추가하려면 데이터 리포지토리 드롭다운에서 **새로 추가**를 클릭한 다음에 **데이터 리포지토리 추가**를 클릭합니다.

    ![새 데이터 리포지토리 추가](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. **StorSimple 8000 시리즈 관리자**를 데이터 저장소 형식으로 선택합니다.
    
   2. 원본 데이터 리포지토리에 대한 이름을 입력합니다.
    
   3. 드롭다운 목록에서 StorSimple 디바이스 관리자 서비스와 연결된 구독을 선택합니다.
    
   4. **리소스**에 대한 StorSimple 디바이스 관리자의 이름을 제공합니다.

   5. StorSimple 디바이스 관리자 서비스에 대한 **서비스 데이터 암호화** 키를 입력합니다. 

      ![원본 데이터 리포지토리 구성 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      완료되면 **확인**을 클릭합니다. 데이터 저장소가 저장됩니다. 이러한 매개 변수를 다시 입력하지 않고도 다른 작업 정의에서 이 StorSimple 디바이스 관리자를 다시 사용합니다. **확인**을 클릭한 후에 새로 만든 원본 데이터 리포지토리가 드롭다운에 표시되는 데 몇 초 정도가 걸립니다.

7. **데이터 리포지토리**에 대한 드롭다운 목록에서 만든 데이터 리포지토리를 선택합니다. 

   1. 관심 있는 데이터를 포함하는 StorSimple 8000 시리즈 디바이스 이름을 입력합니다.

   2. 관심 있는 데이터가 있는 StorSimple 디바이스의 볼륨 이름을 지정합니다.

   3. **필터** 하위 섹션에서 관심 있는 데이터를 포함하는 루트 디렉터리를 _\MyRootDirectory\Data_ 형식으로 입력합니다. _\C:\Data_와 같은 드라이브 문자는 지원되지 않습니다. 여기에서 파일 필터를 추가할 수 있습니다.

   4. 데이터 변환 서비스는 Azure에 푸시되는 데이터의 최신 스냅숏에 대해서만 작동 합니다.

   5. **확인**을 클릭합니다.

      ![원본 데이터 리포지토리 구성 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. 다음으로, 대상 데이터 리포지토리를 구성해야 합니다. 스토리지 계정을 선택하여 해당 계정에서 Blob에 파일을 배치합니다. 드롭다운 목록에서 **새로 추가** 및 **설정 구성** 차례로 선택합니다.

9. 추가하려는 대상 리포지토리의 유형 및 리포지토리와 연결된 다른 매개 변수를 선택합니다.

    스토리지 계정 유형 대상을 선택하는 경우 이름, 구독(해당 서비스 또는 다른 서비스와 동일한 구독 선택) 및 스토리지 계정을 지정할 수 있습니다.
        ![대상 데이터 리포지토리 구성 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    스토리지 큐는 작업이 실행될 때 만들어집니다. 이 큐는 준비가 완료된 변환 Blob에 대한 메시지로 채워집니다. 이 큐의 이름은 작업 정의의 이름과 같습니다.
    
10. 데이터 리포지토리를 추가한 후 몇 분 정도 기다립니다.
    
    1. 만든 리포지토리를 **대상 계정 이름**의 드롭다운 목록에서 대상으로 선택합니다.

    2. 스토리지 유형을 Blob 또는 파일로 선택합니다. 변환된 데이터가 있는 스토리지 컨테이너의 이름을 지정합니다. **확인**을 클릭합니다.

        ![대상 데이터 리포지토리 스토리지 계정 구성](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. 또한 작업을 실행하기 전에 예상 작업 기간을 나타내는 옵션을 선택할 수도 있습니다. **확인**을 클릭하여 작업 정의를 만듭니다. 이제 작업 정의가 완료되었습니다. 다른 런타임 설정을 지정해서 UI를 통해 이 작업 정의를 여러 번 사용할 수 있습니다.

    ![작업 정의 완료](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    새로 만든 작업 정의가 이 서비스에 대한 작업 정의 목록에 추가됩니다.

### <a name="run-the-job-definition"></a>작업 정의 실행

StorSimple에서 작업 정의에 지정된 스토리지 계정으로 데이터를 이동할 때마다 작업 정의를 실행해야 합니다. 런타임 시 일부 매개 변수를 다르게 지정할 수 있습니다. 단계는 다음과 같습니다.

1. StorSimple 데이터 관리자 서비스를 선택하고 **관리 > 작업 정의**로 이동합니다. 실행하려는 작업 정의를 선택한 후 클릭합니다.
     
     ![작업 실행 시작 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. **지금 실행**을 클릭 합니다.
     
     ![작업 실행 시작 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. **실행 설정**을 클릭하여 이 작업 실행에 대해 변경하려는 모든 설정을 수정할 수 있습니다. **확인**을 클릭한 다음 **실행**을 클릭하여 작업을 시작합니다.

    ![작업 실행 시작 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. 이 작업을 모니터링하려면 StorSimple 데이터 관리자에서 **작업**으로 이동합니다. 또한 **작업** 블레이드의 모니터링 외에도 StorSimple에서 스토리지 계정으로 파일을 이동할 때마다 메시지가 추가되는 스토리지 큐에 대기할 수 있습니다.

    ![작업 실행 시작 4](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>작업 완료 후 로그 보기

작업을 완료 한 후 작업의 상태를 볼 수 있습니다. 작업 상태는 **성공**, **부분적으로 성공** 및 **실패**일 수 있습니다. 성공적으로 복사 된 파일 목록과 복사 하지 못한 파일을 볼 수 있습니다. 이러한 목록은 대상 저장소 계정 내의 **"storsimple-데이터 관리자-joblogs"** 라는 컨테이너에서 사용할 수 있습니다. 이 컨테이너 내에서 작업 정의와 이름이 같은 폴더를 찾을 수 있습니다. 이 내에서 목록을 포함 하는 모든 작업 실행에 대해 폴더가 생성 됩니다. 이 폴더의 이름은 작업 세부 정보 페이지에서 가져올 수 있는 작업의 GUID가 됩니다. 또는 대부분의 경우 작업 페이지 내에서 복사 로그에 대 한 링크가 표시 됩니다.
이 폴더에는 두 개의 csv 파일 집합이 표시 됩니다. 모든 파일에는 성공적으로 **복사 된 파일 목록이 포함 됩니다.** **Failedfilelist** 로 시작 하는 모든 파일에는 오류 메시지와 함께 복사할 수 없는 파일이 포함 되어 있습니다.


## <a name="next-steps"></a>다음 단계

[.NET SDK를 사용하여 StorSimple 데이터 관리자 작업을 시작합니다](storsimple-data-manager-dotnet-jobs.md).
