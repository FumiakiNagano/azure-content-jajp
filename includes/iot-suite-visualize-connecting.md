## デバイスのテレメトリをダッシュボードに表示する

リモート監視ソリューションのダッシュボードでは、デバイスが IoT Hub に送信するテレメトリを表示できます。

1. ブラウザーでリモート監視ソリューションのダッシュボードに戻り、左側のパネルの **[デバイス]** をクリックし、**[デバイスの一覧]** に移動します。

2. **[デバイスの一覧]** で、デバイスの状態が現在 **[実行中]** であることを確認してください。

    ![][18]

3. **[ダッシュボード]** をクリックしてダッシュボードに戻り、**[表示するデバイス]** ドロップダウンでテレメトリを表示するデバイスを選択します。サンプル アプリケーションから送信されるテレメトリは、内部温度では 50 単位、外部温度では 55 単位、湿度では 50 単位です。ダッシュボードに既定で表示されるのは温度と湿度のみであることに注意してください。

    ![][img-telemetry]

## デバイスにコマンドを送信する

リモート監視ソリューションのダッシュボードでは、デバイスにコマンドを送信することを IoT Hub に要求できます。たとえば、リモート監視ソリューションでデバイスの内部温度を設定するコマンドを送信できます。

1. リモート監視ソリューションのダッシュボードで、左側のパネルの **[デバイス]** をクリックし、**[デバイスの一覧]** に移動します。

2. **[デバイスの一覧]**で、デバイスの **[デバイス ID]** をクリックします。

3. **[デバイスの詳細]** パネルで、**[コマンド]** をクリックします。

    ![][13]

4. **[コマンド]** ドロップダウン リストで、**[SetTemperature]** を選択し、**[温度]** に新しい温度の値を入力します。**[コマンドの送信]** をクリックして、コマンドをデバイスに送信します。

    ![][14]

    > [AZURE.NOTE] コマンド履歴では、コマンドのステータスは最初は **[保留中]** と表示されます。デバイスがコマンドを確認すると、ステータスは **[成功]** に変わります。

5. ダッシュボードで、デバイスが新しい温度の値として 75 を送信していることを確認します。

## 次のステップ

記事「[構成済みソリューションのカスタマイズ][lnk-customize]」に、このサンプルを拡張するいくつかの方法の説明があります。可能な拡張には、実際のセンサーの使用やその他のコマンドの実装などがあります。

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-dev-messaging]: ../articles/iot-hub/iot-hub-devguide.md#messaging

<!---HONumber=AcomDC_0211_2016-->