---
title: Azure Analysis Services 데이터베이스 백업 및 복원 | Microsoft Docs
description: Azure Analysis Services 데이터베이스를 백업하고 복원하는 방법에 대해 설명합니다.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 31e8e65b382a3a6bcad2998a0babdf9605dc4968
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023910"
---
# <a name="backup-and-restore"></a>Backup 및 복원

Azure Analysis Services에서 테이블 형식 모델 데이터베이스를 백업하는 것은 온-프레미스 Analysis Services의 경우와 매우 유사합니다. 주요 차이점은 백업 파일을 저장하는 위치입니다. Backup 파일은 [Azure Storage 계정](../storage/common/storage-create-storage-account.md)의 컨테이너에 저장해야 합니다. 이미 있는 저장소 계정과 컨테이너를 사용하거나 서버에 대한 저장소 설정을 구성할 때 만들 수 있습니다.

> [!NOTE]
> 저장소 계정을 만들면 새로운 유료 서비스가 발생할 수 있습니다. 자세한 내용은 [Azure Storage 가격](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요.
> 
> 

백업을은.abf 확장명이 저장 됩니다. 메모리 내 테이블 형식 모델의 경우 모델 데이터와 메타데이터가 모두 저장됩니다. DirectQuery 테이블 형식 모델의 경우 모델 메타데이터만 저장됩니다. Backup은 선택한 옵션에 따라 압축하고 암호화할 수 있습니다.


## <a name="configure-storage-settings"></a>저장소 설정 구성
백업하기 전에 서버에 대해 저장소 설정을 구성해야 합니다.


### <a name="to-configure-storage-settings"></a>저장소 설정을 구성하려면
1.  Azure Portal > **설정**에서 **Backup**을 클릭합니다.

    ![설정의 Backup](./media/analysis-services-backup/aas-backup-backups.png)

2.  **사용**을 클릭한 다음 **저장소 설정**을 클릭합니다.

    ![사용](./media/analysis-services-backup/aas-backup-enable.png)

3. 저장소 계정을 선택하거나 새로 만듭니다.

4. 컨테이너를 선택하거나 새로 만듭니다.

    ![컨테이너 선택](./media/analysis-services-backup/aas-backup-container.png)

5. 백업 설정을 저장합니다.

    ![백업 설정 저장](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>SSMS를 사용하여 백업하려면

1. SSMS에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **백업**을 클릭합니다.

2. **데이터베이스 Backup** > **Backup 파일**에서 **찾아보기**를 클릭합니다.

3. **다른 이름으로 파일 저장** 대화 상자에서 폴더 경로 확인한 다음 백업 파일의 이름을 입력합니다. 

4. **데이터베이스 Backup** 대화 상자에서 옵션을 선택 합니다.

    **파일 덮어쓰기 허용** - 이름이 같은 백업 파일을 덮어쓰려면 이 옵션을 선택합니다. 이 옵션을 선택하지 않으면 저장하려는 파일에 동일한 위치에 이미 있는 파일과 같은 이름을 사용할 수 없습니다.

    **압축 적용** - 백업 파일을 압축하려면 이 옵션을 선택합니다. 백업 파일을 압축하면 디스크 공간은 절약되지만 CPU 사용률이 약간 더 높아집니다. 

    **백업 파일 암호화** - 백업 파일을 암호화하려면 이 옵션을 선택합니다. 이 옵션에서 백업 파일을 보호하려면 사용자가 제공한 암호가 필요합니다. 암호는 복원 작업 이외의 다른 방법으로 백업 데이터를 읽을 수 없도록 합니다. 백업을 암호화하도록 선택한 경우 암호를 안전한 위치에 저장합니다.

5. **확인**을 클릭하여 백업 파일을 만들고 저장합니다.


### <a name="powershell"></a>PowerShell
[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) cmdlet을 사용합니다.

## <a name="restore"></a>복원
복원할 때 백업 파일은 서버용으로 구성한 저장소 계정에 있어야 합니다. 온-프레미스 위치에서 스토리지 계정으로 백업 파일을 이동해야 하는 경우 [Microsoft Azure Storage 탐색기](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) 또는 [AzCopy](../storage/common/storage-use-azcopy.md) 명령줄 유틸리티를 사용합니다. 



> [!NOTE]
> 온-프레미스 서버에서 저장하는 경우 모델 역할에서 모든 도메인 사용자를 제거한 후 Azure Active Directory 사용자로 역할에 다시 추가해야 합니다.
> 
> 

### <a name="to-restore-by-using-ssms"></a>SSMS를 사용하여 복원하려면

1. SSMS에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **복원**을 클릭합니다.

2. **데이터베이스 Backup** 대화 상자의 **Backup 파일**에서 **찾아보기**를 클릭합니다.

3. **데이터베이스 파일 찾기** 대화 상자에서 복원하려는 파일을 선택합니다.

4. **데이터베이스 복원**에서 데이터베이스를 선택합니다.

5. 옵션을 지정합니다. 보안 옵션은 백업할 때 사용한 백업 옵션과 일치해야 합니다.


### <a name="powershell"></a>PowerShell

[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) cmdlet을 사용합니다.


## <a name="related-information"></a>관련 정보

[Azure Storage 계정](../storage/common/storage-create-storage-account.md)  
[고가용성](analysis-services-bcdr.md)     
[Azure Analysis Services 관리](analysis-services-manage.md)
