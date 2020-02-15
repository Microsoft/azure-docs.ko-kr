---
title: '빠른 시작: 사용자 지정 음성 도우미, Java(Android) - Speech Service'
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 Android에서 Java로 음성 도우미 애플리케이션을 만드는 방법 알아보기
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: d4f84bad6139210dcff3d3f3726d8b7a17d92a5b
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119737"
---
# <a name="quickstart-create-a-voice-assistant-in-java-on-android-by-using-the-speech-sdk"></a>빠른 시작: Speech SDK를 사용하여 Android에서 Java로 음성 도우미 만들기

빠른 시작은 [음성 텍스트 변환](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android) 및 [텍스트 음성 변환](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=android)에도 사용할 수 있습니다.

이 문서에서는 [Speech SDK](speech-sdk.md)를 사용하여 Android용 Java로 음성 도우미를 빌드합니다. 이 애플리케이션은 이미 작성하고 [Direct Line Speech 채널](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)로 구성한 봇에 연결합니다. 그런 다음, 봇에 음성 요청을 보내고 음성 지원 응답 작업을 제공합니다.

이 애플리케이션은 Speech SDK Maven 패키지와 Android Studio 3.3으로 작성되었습니다. Speech SDK는 현재 32/64비트 ARM 및 Intel x86/x64 호환 프로세서를 탑재한 Android 디바이스와 호환됩니다.

> [!NOTE]
> Speech Devices SDK 및 Roobo 디바이스에 대한 내용은 [Speech Devices SDK](speech-devices-sdk.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Speech Service에 대한 Azure 구독 키입니다. [Azure Portal](https://portal.azure.com)에서 [무료로 얻거나](get-started.md) 새로 만듭니다.
- 이전에 [Direct Line Speech 채널](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)을 사용하여 만들고 구성한 봇
- [Android Studio](https://developer.android.com/studio/) v3.3 이상

    > [!NOTE]
    > [음성 도우미에 대한 지원되는 지역 목록](regions.md#voice-assistants)을 참조하고 리소스가 해당 지역 중 하나에 배포되었는지 확인하세요.

## <a name="create-and-configure-a-project"></a>프로젝트 만들기 및 구성

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>사용자 인터페이스 만들기

이 섹션에서는 애플리케이션의 기본 UI(사용자 인터페이스)를 만듭니다. 기본 작업 `activity_main.xml`을 여는 것부터 시작하겠습니다. 기본 템플릿에는 애플리케이션 이름이 있는 제목 표시줄과 "Hello world!" 메시지가 포함된 `TextView`가 있습니다.

다음으로, `activity_main.xml`의 콘텐츠를 다음 코드로 바꿉니다.

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

이 XML은 봇과 상호 작용하는 간단한 UI를 정의합니다.

- `button` 요소를 클릭하면 상호 작용이 시작되고 `onBotButtonClicked` 메서드가 호출됩니다.
- `recoText` 요소는 사람이 봇에 말할 때 음성 텍스트 변환 결과를 표시합니다.
- `activityText` 요소는 봇에서 최신 Bot Framework 작업에 대한 JSON 페이로드를 표시합니다.

이제 UI의 텍스트 및 그래픽 모양이 다음과 유사하게 표시됩니다.

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>샘플 코드 추가

1. `MainActivity.java`를 열고 내용을 다음 코드로 바꿉니다.

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from speech subscription information
            BotFrameworkConfig config = BotFrameworkConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * `onCreate` 메서드에는 마이크 및 인터넷 권한을 요청하는 코드가 포함됩니다.

   * `onBotButtonClicked` 메서드는 앞에서 설명한 것처럼 단추 클릭 처리기입니다. 단추를 누르면 봇과의 단일 상호 작용("turn")이 트리거됩니다.

   * `registerEventListeners` 메서드는 `DialogServiceConnector`에 사용되는 이벤트와 들어오는 작업에 대한 기본 처리를 보여줍니다.

1. 동일한 파일에서 구성 문자열을 리소스에 맞게 바꿉니다.

    * `YourChannelSecret`은 봇의 Direct Line Speech 채널 비밀로 바꿉니다.

    * `YourSpeechSubscriptionKey`를 구독 키로 바꿉니다.

    * `YourServiceRegion`은 구독과 연결된 [지역](regions.md)으로 바꿉니다. 현재는 Speech Service 지역의 하위 집합에만 Direct Line Speech가 지원됩니다. 자세한 내용은 [지역](regions.md#voice-assistants)을 참조하세요.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 개발 PC에 Android 디바이스를 연결합니다. 디바이스에서 [개발 모드 및 USB 디버깅](https://developer.android.com/studio/debug/dev-options)이 사용하도록 설정되어 있는지 확인합니다.

1. 애플리케이션을 빌드하려면 Ctrl + F9를 누르거나 메뉴 모음에서 **빌드** > **프로젝트 만들기**를 선택합니다.

1. 애플리케이션을 시작하려면 Shift+F10을 누르거나 **실행** >  **'앱' 실행**을 선택합니다.

1. 나타나는 배포 대상 창에서 Android 디바이스를 선택합니다.

   ![배포 대상 선택 창 스크린샷](media/sdk/qs-java-android-12-deploy.png)

애플리케이션과 작업이 시작되면 단추를 클릭하여 봇에 말하기 시작합니다. 말하는 동안 문자화된 텍스트가 표시되고 봇에서 받은 최신 작업이 수신되면 나타납니다. 봇이 음성 응답을 제공하도록 구성된 경우에는 음성 텍스트 변환이 자동으로 재생됩니다.

![Android 애플리케이션 스크린샷](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기본 봇 생성 및 배포](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>참고 항목
- [음성 도우미 정보](voice-assistants.md)
- [평가판 Speech Service 구독 키 받기](get-started.md)
- [사용자 지정 키워드](speech-devices-sdk-create-kws.md)
- [봇에 Direct Line Speech 연결](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [GitHub에서 Java 샘플 살펴보기](https://aka.ms/csspeech/samples)
