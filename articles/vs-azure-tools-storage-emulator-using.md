---
title: Visual Studio에서 저장소 에뮬레이터 구성 및 사용 | Microsoft Docs
description: Visual Studio에서 저장소 에뮬레이터 구성 및 사용
services: visual-studio-online
author: ghogen
manager: douge
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: 39e2071a62d6a1f6ee050f862856815048e50430
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128299"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Visual Studio에서 저장소 에뮬레이터 구성 및 사용
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>개요
Azure SDK 개발 환경은 로컬 개발 컴퓨터의 Azure에서 사용할 수 있는 Blob, 큐 및 Table Storage를 시뮬레이션하는 유틸리티인 스토리지 에뮬레이터를 포함합니다. Azure 스토리지 서비스를 사용하는 클라우드 서비스를 구축하거나 스토리지 서비스를 호출하는 외부 애플리케이션을 작성하는 경우, 스토리지 에뮬레이터에 대해 로컬로 코드를 테스트할 수 있습니다. Microsoft Visual Studio용 Azure Tools는 저장소 에뮬레이터의 관리를 Visual Studio로 통합합니다. 처음으로 Azure Tools가 스토리지 에뮬레이터 데이터베이스를 초기화하고, Visual Studio에서 코드를 실행하거나 디버깅할 때 스토리지 에뮬레이터 서비스를 시작하고, Azure Storage 탐색기를 통해 스토리지 에뮬레이터 데이터에 대한 읽기 전용 액세스를 제공합니다.

시스템 요구 사항 및 사용자 지정 구성 지침을 포함한 스토리지 에뮬레이터에 대한 자세한 정보는 [개발 및 테스트용으로 Azure Storage 에뮬레이터 사용](storage/common/storage-use-emulator.md)을 참조하세요.

> [!NOTE]
> 저장소 에뮬레이터 시뮬레이션과 Azure 저장소 서비스 간 기능에 몇 가지 차이점이 있습니다. 특정 차이점에 대한 정보는 Azure SDK 설명서에서 [스토리지 에뮬레이터와 Azure Storage 서비스 간 차이점](storage/common/storage-use-emulator.md)을 참조하세요.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>저장소 에뮬레이터에 대한 연결 문자열 구성
역할 내 코드에서 저장소 에뮬레이터에 액세스하려면, 저장소 에뮬레이터를 가리키고 이후 Azure 저장소 계정을 가리키도록 변경될 수 있는 연결 문자열을 구성합니다. 연결 문자열은 사용자 역할이 저장소 계정에 연결하도록 런타임에 읽을 수 있는 구성 설정입니다. 연결 문자열을 만드는 방법에 대한 내용은 [Azure Storage 연결 문자열 구성](/azure/storage/common/storage-configure-connection-string)을 참조하세요.

> [!NOTE]
> **DevelopmentStorageAccount** 속성을 사용하여 사용자 코드에서 스토리지 에뮬레이터 계정에 대한 참조를 반환할 수 있습니다. 사용자 코드에서 스토리지 에뮬레이터에 액세스하려고 하지만 Azure에 애플리케이션을 게시하려는 경우 이 방법이 제대로 작동하며, Azure 스토리지 계정에 액세스하는 연결 문자열을 만들고 게시하기 전에 해당 연결 문자열을 사용하도록 코드를 수정해야 합니다. 저장소 에뮬레이터 계정과 Azure 저장소 계정 사이를 자주 전환하는 경우, 연결 문자열은 이 프로세스를 단순화합니다.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>저장소 에뮬레이터 초기화 및 실행
Visual Studio에서 서비스를 실행하거나 디버깅하는 경우 Visual Studio가 자동으로 저장소 에뮬레이터를 시작하도록 지정할 수 있습니다. 솔루션 탐색기에서 **Azure** 프로젝트에 대한 바로 가기 메뉴를 열고 **속성**을 선택합니다. **개발** 탭의 **Azure Storage 에뮬레이터 시작** 목록에서 **True**를 선택합니다(이미 해당 값으로 설정되지 않은 경우).

처음으로 Visual Studio에서 서비스를 실행하거나 디버그한 경우, 저장소 에뮬레이터는 초기화 프로세스를 시작합니다. 이 프로세스는 저장소 에뮬레이터에 대한 로컬 포트를 예약하고 저장소 에뮬레이터 데이터베이스를 만듭니다. 완료되면 저장소 에뮬레이터 데이터베이스를 삭제하지 않는 한 이 프로세스를 다시 실행할 필요가 없습니다.

> [!NOTE]
> Azure Tools의 2012년 6월 릴리스로 시작해서 저장소 에뮬레이터는 SQL Express LocalDB에서 기본적으로 실행됩니다. Azure Tools의 이전 릴리스에서 저장소 에뮬레이터는 SQL Express 2005 또는 2008의 기본 인스턴스에 대해 실행되며, Azure SDK를 설치하기 전에 설치되어야 합니다. 명명된 SQL Express의 인스턴스 또는 Microsoft SQL Server의 명명된 또는 기본 인스턴스에 대해 저장소 에뮬레이터를 실행할 수도 있습니다. 기본 인스턴스가 아닌 인스턴스에 대해 실행하도록 구성해야 하는 경우, [개발 및 테스트용으로 Azure Storage 에뮬레이터 사용](storage/common/storage-use-emulator.md)을 참조하세요.
> 
> 

저장소 에뮬레이터는 로컬 저장소 서비스의 상태를 보고 시작, 중지 및 다시 설정하는 사용자 인터페이스를 제공합니다. 저장소 에뮬레이터 서비스가 시작된 후 사용자 인터페이스를 표시하거나, Windows 작업 표시줄에서 Microsoft Azure 에뮬레이터에 대한 알림 영역 아이콘을 마우스 오른쪽 단추로 클릭하여 서비스를 시작하거나 중지할 수 있습니다.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>서버 탐색기에서 저장소 에뮬레이터 데이터 보기
서버 탐색기에서 Azure Storage 노드를 사용하면 데이터를 보고 스토리지 에뮬레이터를 포함한 Storage 계정의 blob 및 테이블 데이터에 대한 설정을 변경할 수 있습니다. 자세한 내용은 [Storage 탐색기를 사용하여 Azure Blob Storage 리소스 관리](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)를 참조하세요.

