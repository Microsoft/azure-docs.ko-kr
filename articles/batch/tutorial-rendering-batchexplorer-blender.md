---
title: Azure Batch 및 Batch 탐색기를 사용하여 Blender 장면 렌더링
description: 자습서 - Azure Batch 및 Batch 탐색기 클라이언트 애플리케이션을 사용하여 Blender 장면에서 여러 프레임을 렌더링하는 방법
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: tutorial
ms.openlocfilehash: 65baff827417cebe2289cc821df94acd38a1ae61
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85964807"
---
# <a name="tutorial-render-a-blender-scene-using-batch-explorer"></a>자습서: Batch 탐색기를 사용하여 Blender 장면 렌더링

이 자습서에서는 Blender 데모 장면의 여러 프레임을 렌더링하는 방법을 보여줍니다. Blender는 클라이언트 및 렌더링 중인 VM 모두에 대한 요금이 청구되지 않으므로 이 자습서에 사용됩니다. 하지만 Maya 또는 3D Max와 같은 다른 애플리케이션을 사용한 경우 프로세스가 매우 유사합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Azure Storage에 Blender 장면 업로드
> * 렌더링을 수행하기 위해 여러 노드를 포함한 Batch 풀 만들기
> * 여러 프레임 렌더링
> * 렌더링된 프레임 파일 보기 및 다운로드

## <a name="prerequisites"></a>사전 요구 사항

사용량 기준 과금 단위로 일괄 처리에서 렌더링 애플리케이션을 사용하는 데 종량제 구독 또는 다른 Azure 구입 옵션이 필요합니다. 사용량 기준 과금 라이선스는 금액 크레딧을 제공하는 무료 Azure 제품을 사용하는 경우 지원되지 않습니다.

스토리지 계정이 연결된 Azure Batch 계정이 필요합니다.  [CLI 아티클](./quick-create-cli.md)과 같은 Batch 빠른 시작 아티클 중 하나를 참조하여 Batch 계정을 만듭니다.

이 자습서에 지정된 VM 크기 및 VM 수에 대해 우선 순위가 낮은 코어 할당량으로 최소 50개 코어가 필요합니다. 기본 할당량을 사용할 수 있지만 작은 VM 크기를 사용해야 합니다. 그러면 이미지를 렌더링하는 데 더 많은 시간이 걸립니다. 코어 할당량을 증가하도록 요청하는 프로세스는 [이 아티클](./batch-quota-limit.md)에 자세히 설명됩니다.

마지막으로, [Batch 탐색기](https://azure.github.io/BatchExplorer/)가 설치되어 있어야 합니다. 이 기능은 Windows, OSX 및 Linux에서 사용할 수 있습니다. 선택 사항이지만 [Blender](https://www.blender.org/download/)가 설치된 경우 샘플 모델 파일을 볼 수 있습니다.

## <a name="download-the-demo-scene"></a>데모 장면 다운로드

[Blender 데모 파일 웹 페이지](https://www.blender.org/download/demo-files/)에서 Blender에 대한 "클래스룸" 데모 ZIP 파일을 다운로드하고 로컬 드라이브의 압축을 풉니다.

## <a name="upload-a-scene-to-azure-storage"></a>Azure Storage에 장면 업로드

데모 장면 파일의 스토리지 계정 컨테이너를 만듭니다.

* Batch 탐색기 시작
* 왼쪽 기본 메뉴에서 '데이터' 메뉴 항목을 선택합니다.
* 풀다운에서 '파일 그룹'을 선택했는지 확인합니다.
* '+' 단추를 선택하고 'blender-classroom'이라는 새로운 '빈 파일 그룹'을 만듭니다.
  * 파일 그룹은 단순히 'fgrp-' 접두사가 있는 Azure Storage Blob 컨테이너입니다. 이것은 스토리지 계정에서 다른 컨테이너를 필터링하는 데 사용되는 규칙입니다.

![장면 파일의 파일 그룹](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup.png)

장면 파일을 업로드합니다.

* 새 컨테이너를 선택하고, 'classroom' 폴더의 콘텐츠를 Batch 탐색기의 컨테이너에 끌어서 놓습니다.

![장면 파일 업로드됨](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup_uploaded.png)

## <a name="create-azure-storage-container-for-output-images"></a>출력 이미지의 Azure Storage 컨테이너 만들기

데모 장면 출력 파일의 스토리지 계정 컨테이너를 만듭니다.

* 왼쪽 기본 메뉴에서 '데이터' 메뉴 항목을 선택합니다.
* '+' 단추를 선택하고 'render-output'이라는 새로운 '빈 파일 그룹'을 만듭니다.

![출력 파일의 파일 그룹](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_filegroup.png)

## <a name="create-a-pool-of-vms-for-rendering"></a>렌더링할 VM의 풀 만들기

Blender 애플리케이션이 포함된 Azure Marketplace VM 이미지를 렌더링하여 Batch 풀을 만듭니다.

* 왼쪽 기본 메뉴에서 '갤러리' 메뉴 항목을 선택합니다.
* 애플리케이션 항목의 목록에 대해 'Blender' 항목을 선택합니다.
* Windows Server에서 프레임을 렌더링하기 위한 항목을 선택합니다.
* 필요에 따라 항목의 오른쪽에 있는 링크 아이콘을 선택하여 풀 및 작업을 만드는 데 사용할 템플릿 파일을 볼 수 있습니다.

![Blender 갤러리 항목](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_gallery_item.png)

* '나중에 사용할 풀 만들기' 단추 선택
  * 풀 이름을 "blender-windows" 그대로 유지
  * '전용 VM 수'를 "0"으로 설정
  * '우선 순위가 낮은 VM 수'를 "3"으로 설정
  * '노드 크기'를 "Standard_F16"으로 설정 – 다른 VM 크기를 선택할 수 있지만 프레임을 렌더링하는 시간은 주로 코어 수에 따라 달라집니다.
* 녹색 단추를 선택하여 풀 만들기
  * 풀이 거의 즉시 생성되지만 VM이 할당되고 시작하기까지 몇 분 정도가 걸립니다.

![Blender의 풀 템플릿](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_template.png)

> [!WARNING]
> VM이 풀에 있는 경우 해당 VM의 비용이 Azure 구독에 청구됩니다. 요금이 청구되지 않도록 중지하기 위해 풀을 삭제하거나 VM을 삭제해야 합니다. 발생 중인 요금을 방지하기 위해 이 자습서의 끝에서 풀을 삭제합니다.

'풀' 보기에서 풀 및 VM의 상태를 모니터링할 수 있습니다. 다음 예제에서는 세 대의 모든 VM이 할당되고, 두 대는 시작되어 유휴 상태이며, 한 대는 시작하고 있음을 보여줍니다. ![풀 열 지도](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap.png)

## <a name="create-a-rendering-job"></a>렌더링 작업 만들기

생성된 풀을 사용하여 일부 프레임을 렌더링하는 렌더링 작업을 만듭니다.
* 왼쪽 기본 메뉴에서 '갤러리' 메뉴 항목을 선택합니다.
* 애플리케이션 항목의 목록에 대해 'Blender' 항목을 선택합니다.
* Windows Server에서 프레임을 렌더링하기 위한 항목을 선택합니다.
* '기존 풀을 사용하여 작업 실행' 단추를 선택합니다.
* 'blender-windows' 풀을 선택합니다.
* '작업 이름'을 'blender-render-tutorial1'로 설정합니다.
* '입력 데이터'에서 'fgrp-blender-classroom'을 선택합니다.
* 'Blend 파일'에서 파일 아이콘을 선택하고 'classroom.blend'를 선택합니다.
* '프레임 시작'을 '1'로 유지하고 '프레임 종료'를 '5'로 설정합니다.
* '출력'을 'fgrp-render-output'으로 설정합니다.
* 녹색 단추를 선택하여 작업을 만듭니다. 각 프레임에 대해 하나씩 5개의 태스크를 포함한 작업이 만들어집니다.

![Blender의 작업 템플릿](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_template.png)

작업 및 모든 태스크를 만들면 작업이 작업 태스크와 함께 표시됩니다. ![작업 태스크 목록](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_task_list.png)

태스크가 풀 VM에서 처음으로 실행되기 시작하면 Blender에서 액세스할 수 있도록 스토리지 파일 그룹에서 VM에 장면 파일을 복사하는 Batch 작업 준비 태스크가 실행됩니다.
Blender에서 생성된 stdout.txt 로그 파일을 확인하여 렌더링 상태를 확인할 수 있습니다.  작업을 선택하면 '태스크 출력'이 기본적으로 표시되고, 'stdout.txt' 파일을 선택하고 볼 수 있습니다.
![stdout 파일](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_stdout.png)

'blender-windows' 풀을 선택하면 풀 VM이 실행 중 상태로 표시됩니다. ![노드를 실행하는 풀 열 지도](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap_running.png)

선택한 VM 크기에 따라 렌더링된 이미지가 생성되는 데 몇 분 정도가 걸립니다.  이전에 지정된 F16 VM을 사용하여 프레임을 렌더링하는 데 약 16분이 걸렸습니다.

## <a name="view-the-rendering-output"></a>렌더링 출력 보기

프레임 렌더링을 완료하면 해당 태스크가 완료됨으로 표시됩니다. ![완료 중인 작업](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_tasks_complete.png)

렌더링된 이미지를 VM에 먼저 작성하고 'wd' 폴더를 선택하여 볼 수 있습니다. ![풀 노드에서 렌더링된 이미지](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image.png)

또한 이 작업 템플릿은 작업이 만들어질 때 지정된 Azure Storage 계정 파일 그룹에 출력 프레임 및 로그 파일을 다시 기록하도록 지정합니다.  '데이터' UI는 출력 파일 및 로그를 보는 데 사용하고 파일을 다운로드하는 데 사용할 수도 있습니다. ![스토리지 파일 그룹에서 렌더링된 이미지](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image_storage.png)

모든 태스크를 완료하면 작업이 완료된 것으로 표시됩니다. ![완료된 모든 작업 및 태스크](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_alltasks_complete.png)

## <a name="clean-up-resources"></a>리소스 정리

> [!WARNING]
> 풀을 삭제(0개 노드 아래로 크기를 조정할 수 있음)하여 Azure 구독에 발생한 VM에 대한 요금을 중지해야 합니다.

* '풀'을 선택합니다.
* 'blender-windows' 풀을 선택합니다.
* '삭제'를 마우스 오른쪽 단추로 클릭하거나 풀 위에서 휴지통 아이콘을 선택합니다.

## <a name="next-steps"></a>다음 단계
* '갤러리' 섹션에서 Batch 탐색기를 통해 사용 가능한 렌더링 애플리케이션을 탐색합니다.
* 각 애플리케이션에는 시간이 지남에 따라 확장되는 사용 가능한 여러 템플릿이 있습니다.  예를 들어 Blender의 경우 이미지의 일부를 병렬적으로 렌더링할 수 있도록 단일 이미지를 타일로 분할하는 템플릿이 있습니다.
* 렌더링 기능에 대한 포괄적인 설명은 [여기](./batch-rendering-service.md)에 있는 아티클 집합을 참조하세요.
