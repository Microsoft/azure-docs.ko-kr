---
title: Java SDK를 사용하여 Azure Media Services 시작 | Microsoft Docs
description: 이 자습서에서는 Java를 사용하는 AMS(Azure Media Services) 애플리케이션으로 기본 VoD(주문형 비디오) 콘텐츠 배달 서비스를 구현하는 단계를 안내합니다.
services: media-services
documentationcenter: java
author: juliako
manager: femila
editor: johndeu
ms.assetid: b884bd61-dbdb-42ea-b170-8fb02e7fded7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a12d0e7d4382d1f8feac721c103d9231e54ad249
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463867"
---
# <a name="get-started-with-the-java-client-sdk-for-azure-media-services"></a>Java 클라이언트 SDK를 사용하여 Azure Media Services 시작  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

이 자습서에서는 Java 클라이언트 SDK를 사용하여 Azure Media Services에서 기본 비디오 콘텐츠 배달 서비스를 구현하는 단계를 안내합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하는 데 필요한 조건은 다음과 같습니다.

* Azure 계정. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* Media Services 계정. Media Services 계정을 만들려면 [Media Services 계정을 만드는 방법](media-services-portal-create-account.md)을 참조하세요.
* 최신 [Azure Media Services Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-media/latest)

## <a name="how-to-import-the-azure-media-services-java-client-sdk-package"></a>방법: Azure Media Services Java 클라이언트 SDK 패키지 가져오기

Java용 Media Services SDK를 사용하려면 [Azure Media Services Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-media/latest)의 `azure-media` 패키지 현재 버전(0.9.8)에 대한 참조를 추가합니다.

예를 들어 빌드 도구가 `gradle`이면 `build.gradle` 파일에 다음 종속성을 추가합니다.

    compile group: 'com.microsoft.azure', name: 'azure-media', version: '0.9.8'

>[!IMPORTANT]
>`azure-media` 패키지 버전 `0.9.8`부터 SDK에서 AAD(Azure Active Directory) 인증을 지원하고 Azure ACS(Access Control Service) 인증 지원을 제거했습니다. 가능한 빨리 Azure AD 인증 모델로 마이그레이션하는 것이 좋습니다. 마이그레이션에 대한 자세한 내용은 [Azure AD 인증을 사용하여 Azure Media Services API 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조하세요.

>[!NOTE]
>Azure Media Services Java SDK의 소스 코드는 [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-java/tree/0.9/services/azure-media)에서 찾을 수 있습니다. 마스터 분기가 아니라 0.9 분기로 전환해야 합니다. 

## <a name="how-to-use-azure-media-services-with-java"></a>방법: Java로 Azure Media Services 사용

>[!NOTE]
>Azure Media Services 계정이 만들어지면 **기본** 스트리밍 엔드포인트가 **중지됨** 상태에 있는 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 **실행** 상태에 있어야 합니다.

다음 코드에서는 자산을 만들고, 미디어 파일을 자산에 업로드하고, 자산 변환 태스크를 포함하는 작업을 실행하고, 동영상을 스트리밍하기 위해 로케이터를 만드는 방법을 보여 줍니다.

이 코드를 사용하려면 먼저 Media Services 계정을 설정해야 합니다. 계정 설정에 대한 자세한 내용은 [Media Services 계정을 만드는 방법](media-services-portal-create-account.md)(영문)을 참조하세요.

이 코드는 Azure AD 서비스 주체 인증을 사용하여 Azure Media Services API에 연결됩니다. Azure AD 애플리케이션을 만들고 코드에서 다음 변수의 값을 지정합니다.
* `tenant`: Azure AD 애플리케이션이 있는 Azure AD 테넌트 도메인
* `clientId`: Azure AD 애플리케이션의 클라이언트 ID
* `clientKey`: Azure AD 애플리케이션의 클라이언트 키
* `restApiEndpoint`: Azure Media Services 계정의 REST API 엔드포인트

Azure AD 애플리케이션을 만들고 Azure Portal에서 위의 구성 값을 얻을 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 Azure AD 인증 시작](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad)의 **서비스 주체 인증** 섹션을 참조하세요.

또한 이 코드는 로컬에 저장된 비디오 파일을 사용합니다. 업로드할 사용자 고유의 로컬 파일을 제공하도록 코드를 편집해야 합니다.

```java
    import java.io.*;
    import java.net.URI;
    import java.security.NoSuchAlgorithmException;
    import java.util.EnumSet;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.media.MediaConfiguration;
    import com.microsoft.windowsazure.services.media.MediaContract;
    import com.microsoft.windowsazure.services.media.MediaService;
    import com.microsoft.windowsazure.services.media.WritableBlobContainerContract;
    import com.microsoft.windowsazure.services.media.authentication.AzureAdClientSymmetricKey;
    import com.microsoft.windowsazure.services.media.authentication.AzureAdTokenCredentials;
    import com.microsoft.windowsazure.services.media.authentication.AzureAdTokenProvider;
    import com.microsoft.windowsazure.services.media.authentication.AzureEnvironments;
    import com.microsoft.windowsazure.services.media.models.AccessPolicy;
    import com.microsoft.windowsazure.services.media.models.AccessPolicyInfo;
    import com.microsoft.windowsazure.services.media.models.AccessPolicyPermission;
    import com.microsoft.windowsazure.services.media.models.Asset;
    import com.microsoft.windowsazure.services.media.models.AssetFile;
    import com.microsoft.windowsazure.services.media.models.AssetFileInfo;
    import com.microsoft.windowsazure.services.media.models.AssetInfo;
    import com.microsoft.windowsazure.services.media.models.Job;
    import com.microsoft.windowsazure.services.media.models.JobInfo;
    import com.microsoft.windowsazure.services.media.models.JobState;
    import com.microsoft.windowsazure.services.media.models.ListResult;
    import com.microsoft.windowsazure.services.media.models.Locator;
    import com.microsoft.windowsazure.services.media.models.LocatorInfo;
    import com.microsoft.windowsazure.services.media.models.LocatorType;
    import com.microsoft.windowsazure.services.media.models.MediaProcessor;
    import com.microsoft.windowsazure.services.media.models.MediaProcessorInfo;
    import com.microsoft.windowsazure.services.media.models.Task;

    public class Program
    {
        // Media Services account credentials configuration
        private static String tenant = "tenant.domain.com";
        private static String clientId = "<client id>";
        private static String clientKey = "<client key>";
        private static String restApiEndpoint = "https://account_name.restv2.region_name.media.azure.net/api/";

        // Media Services API
        private static MediaContract mediaService;

        // Encoder configuration
        // This is using the default Adaptive Streaming encoding preset. 
        // You can choose to use a custom preset, or any other sample defined preset. 
        // In addition you can use other processors, like Speech Analyzer, or Redactor if desired.
        private static String preferredEncoder = "Media Encoder Standard";
        private static String encodingPreset = "Adaptive Streaming";

        public static void main(String[] args)
        {
            ExecutorService executorService = Executors.newFixedThreadPool(1);

            try {
                // Setup Azure AD Service Principal Symmetric Key Credentials
                AzureAdTokenCredentials credentials = new AzureAdTokenCredentials(
                        tenant,
                        new AzureAdClientSymmetricKey(clientId, clientKey),
                        AzureEnvironments.AZURE_CLOUD_ENVIRONMENT);

                AzureAdTokenProvider provider = new AzureAdTokenProvider(credentials, executorService);

                // Create a new configuration with the credentials
                Configuration configuration = MediaConfiguration.configureWithAzureAdTokenProvider(
                        new URI(restApiEndpoint),
                        provider);

                // Create the media service provisioned with the new configuration
                mediaService = MediaService.create(configuration);

                // Upload a local file to an Asset
                AssetInfo uploadAsset = uploadFileAndCreateAsset("Video Name", "C:/path/to/video.mp4");
                System.out.println("Uploaded Asset Id: " + uploadAsset.getId());

                // Transform the Asset
                AssetInfo encodedAsset = encode(uploadAsset);
                System.out.println("Encoded Asset Id: " + encodedAsset.getId());

                // Create the Streaming Origin Locator
                String url = getStreamingOriginLocator(encodedAsset);

                System.out.println("Origin Locator URL: " + url);
                System.out.println("Sample completed!");

            } catch (ServiceException se) {
                System.out.println("ServiceException encountered.");
                System.out.println(se.toString());
            } catch (Exception e) {
                System.out.println("Exception encountered.");
                System.out.println(e.toString());
            } finally {
                executorService.shutdown();
            }
        }

        private static AssetInfo uploadFileAndCreateAsset(String assetName, String fileName)
            throws ServiceException, FileNotFoundException, NoSuchAlgorithmException {

            WritableBlobContainerContract uploader;
            AssetInfo resultAsset;
            AccessPolicyInfo uploadAccessPolicy;
            LocatorInfo uploadLocator = null;

            // Create an Asset
            resultAsset = mediaService.create(Asset.create().setName(assetName).setAlternateId("altId"));
            System.out.println("Created Asset " + fileName);

            // Create an AccessPolicy that provides Write access for 15 minutes
            uploadAccessPolicy = mediaService
                .create(AccessPolicy.create("uploadAccessPolicy", 15.0, EnumSet.of(AccessPolicyPermission.WRITE)));

            // Create a Locator using the AccessPolicy and Asset
            uploadLocator = mediaService
                .create(Locator.create(uploadAccessPolicy.getId(), resultAsset.getId(), LocatorType.SAS));

            // Create the Blob Writer using the Locator
            uploader = mediaService.createBlobWriter(uploadLocator);

            File file = new File(fileName);

            // The local file that will be uploaded to your Media Services account
            InputStream input = new FileInputStream(file);

            System.out.println("Uploading " + fileName);

            // Upload the local file to the media asset
            uploader.createBlockBlob(file.getName(), input);

            // Inform Media Services about the uploaded files
            mediaService.action(AssetFile.createFileInfos(resultAsset.getId()));
            System.out.println("Uploaded Asset File " + fileName);

            mediaService.delete(Locator.delete(uploadLocator.getId()));
            mediaService.delete(AccessPolicy.delete(uploadAccessPolicy.getId()));

            return resultAsset;
        }

        // Create a Job that contains a Task to transform the Asset
        private static AssetInfo encode(AssetInfo assetToEncode)
            throws ServiceException, InterruptedException {

            // Retrieve the list of Media Processors that match the name
            ListResult<MediaProcessorInfo> mediaProcessors = mediaService
                            .list(MediaProcessor.list().set("$filter", String.format("Name eq '%s'", preferredEncoder)));

            // Use the latest version of the Media Processor
            MediaProcessorInfo mediaProcessor = null;
            for (MediaProcessorInfo info : mediaProcessors) {
                if (null == mediaProcessor || info.getVersion().compareTo(mediaProcessor.getVersion()) > 0) {
                    mediaProcessor = info;
                }
            }

            System.out.println("Using Media Processor: " + mediaProcessor.getName() + " " + mediaProcessor.getVersion());

            // Create a task with the specified Media Processor
            String outputAssetName = String.format("%s as %s", assetToEncode.getName(), encodingPreset);
            String taskXml = "<taskBody><inputAsset>JobInputAsset(0)</inputAsset>"
                    + "<outputAsset assetCreationOptions=\"0\"" // AssetCreationOptions.None
                    + " assetName=\"" + outputAssetName + "\">JobOutputAsset(0)</outputAsset></taskBody>";

            Task.CreateBatchOperation task = Task.create(mediaProcessor.getId(), taskXml)
                    .setConfiguration(encodingPreset).setName("Encoding");

            // Create the Job; this automatically schedules and runs it.
            Job.Creator jobCreator = Job.create()
                    .setName(String.format("Encoding %s to %s", assetToEncode.getName(), encodingPreset))
                    .addInputMediaAsset(assetToEncode.getId()).setPriority(2).addTaskCreator(task);
            JobInfo job = mediaService.create(jobCreator);

            String jobId = job.getId();
            System.out.println("Created Job with Id: " + jobId);

            // Check to see if the Job has completed
            checkJobStatus(jobId);
            // Done with the Job

            // Retrieve the output Asset
            ListResult<AssetInfo> outputAssets = mediaService.list(Asset.list(job.getOutputAssetsLink()));
            return outputAssets.get(0);
        }


        public static String getStreamingOriginLocator(AssetInfo asset) throws ServiceException {
            // Get the .ISM AssetFile
            ListResult<AssetFileInfo> assetFiles = mediaService.list(AssetFile.list(asset.getAssetFilesLink()));
            AssetFileInfo streamingAssetFile = null;
            for (AssetFileInfo file : assetFiles) {
                if (file.getName().toLowerCase().endsWith(".ism")) {
                    streamingAssetFile = file;
                    break;
                }
            }

            AccessPolicyInfo originAccessPolicy;
            LocatorInfo originLocator = null;

            // Create a 30-day read only AccessPolicy
            double durationInMinutes = 60 * 24 * 30;
            originAccessPolicy = mediaService.create(
                    AccessPolicy.create("Streaming policy", durationInMinutes, EnumSet.of(AccessPolicyPermission.READ)));

            // Create a Locator using the AccessPolicy and Asset
            originLocator = mediaService
                    .create(Locator.create(originAccessPolicy.getId(), asset.getId(), LocatorType.OnDemandOrigin));

            // Create a Smooth Streaming base URL
            return originLocator.getPath() + streamingAssetFile.getName() + "/manifest";
        }

        private static void checkJobStatus(String jobId) throws InterruptedException, ServiceException {
            boolean done = false;
            JobState jobState = null;
            while (!done) {
                // Sleep for 5 seconds
                Thread.sleep(5000);

                // Query the updated Job state
                jobState = mediaService.get(Job.get(jobId)).getState();
                System.out.println("Job state: " + jobState);

                if (jobState == JobState.Finished || jobState == JobState.Canceled || jobState == JobState.Error) {
                    done = true;
                }
            }
        }
    }
```

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="additional-resources"></a>추가 리소스
Azure에서 Java 응용 프로그램을 개발하는 방법에 대한 자세한 내용은 [Azure Java 개발자 센터][Azure Java Developer Center] 및 [Java 개발자용 Azure 를 참조하세요][Azure for Java developers].


Media Services Javadoc 설명서는 [Java용 Azure 라이브러리 설명서]\(Java용 Azure 라이브러리 설명서)를 참조하세요.

<!-- URLs. -->

[Azure Media Services SDK Maven Package]: https://mvnrepository.com/artifact/com.microsoft.azure/azure-media/latest
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure for Java developers]: https://docs.microsoft.com/java/azure/
[Media Services Client Development]: https://msdn.microsoft.com/library/windowsazure/dn223283.aspx

