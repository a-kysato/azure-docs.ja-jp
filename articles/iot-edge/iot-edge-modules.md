---
title: "Azure IoT Edge モジュールについて | Microsoft Docs"
description: "Azure IoT Edge モジュールと、その構成方法について説明します"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 726bbafa9e4ba35cfa4a9cbf4d89056d52fe7963
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="understand-azure-iot-edge-modules---preview"></a>Azure IoT Edge モジュールについて - プレビュー

Azure IoT Edge では、ビジネス ロジックを*モジュール*形式でエッジに展開および管理できます。 Azure IoT Edge モジュールは、IoT Edge によって管理される計算の最小単位であり、Azure Stream Analytics などの Azure サービスまたは独自ソリューション固有のコードを含めることができます。 モジュールを開発、展開、および管理する方法を理解するには、モジュールを構成する次の 4 つの概念を考えると理解が深まります。

* **モジュール イメージ**。モジュールを定義するソフトウェアを含むパッケージです。
* **モジュール インスタンス**。IoT Edge デバイスでモジュール イメージを実行している計算の特定の単位です。 モジュール インスタンスは、IoT Edge ランタイムによって開始されます。
* **モジュール ID**。IoT Hub に格納されている情報 (セキュリティ資格情報を含む) の一部で、各モジュール インスタンスに関連付けられています。
* **モジュール ツイン**。IoT Hub に格納されている JSON ドキュメントで、メタデータ、構成、条件などのモジュール インスタンスの状態情報が含まれています。 

## <a name="module-images-and-instances"></a>モジュール イメージとインスタンス

IoT Edge モジュール イメージには、IoT Edge ランタイムの管理、セキュリティ、および通信機能を活用するアプリケーションが含まれています。 独自のモジュール イメージを開発したり、Azure Stream Analytics などのサポートされている Azure サービスからモジュール イメージをエクスポートしたりすることもできます。
モジュール イメージはクラウドに存在し、さまざまなソリューションで更新、変更、展開できます。 たとえば、生産ラインの出力を予測する機械学習を使用するモジュールは、コンピューター ビジョンを使用してドローンを制御するモジュールとは別のイメージとして存在します。 

モジュール イメージがデバイスに展開され、IoT Edge ランタイムによって開始されるたびに、該当モジュールの新しいインスタンスが作成されます。 世界のクラウドの異なる部分にある 2 つのデバイスが同じモジュール イメージを使用することができますが、モジュールがデバイスで開始されたときに、各デバイスは独自のモジュール インスタンスを持つことになります。 

![クラウド内のモジュール イメージ - デバイス上のモジュール インスタンス][1]

実装では、モジュール イメージはリポジトリ内のコンテナー イメージとして存在し、モジュール インスタンスはデバイス上のコンテナーです。 Azure IoT Edge のユース ケースが増えると、新しい種類のモジュール イメージとインスタンスが作成されます。 たとえば、リソースの制約があるデバイスはコンテナーを実行できないため、ダイナミック リンク ライブラリとして存在するモジュール イメージと実行可能ファイルであるインスタンスが必要になることがあります。 

## <a name="module-identities"></a>モジュール ID

新しいモジュール インスタンスが IoT Edge ランタイムによって作成されると、作成されたインスタンスには対応するモジュール ID が関連付けられます。 モジュール ID は IoT Hub に格納され、その特定のモジュール インスタンスのすべてのローカルおよびクラウド通信のアドレス指定とセキュリティ スコープとして採用されます。
モジュール インスタンスに関連付けられる ID は、インスタンスが実行されているデバイスの ID と、ソリューション内のモジュールに付けた名前によって決まります。 たとえば、Azure Stream Analytics を使用するモジュール `insight` を呼び出し、`Hannover01` というデバイスに展開した場合、IoT Edge ランタイムは `/devices/Hannover01/modules/insight` という対応するモジュール ID を作成します。

明らかに、1 つのモジュール イメージを同じデバイスに複数回展開する必要があるシナリオで、同じイメージを異なる名前で複数回展開することができます。

![モジュールの ID は一意][2]

## <a name="module-twins"></a>モジュール ツイン

各モジュール インスタンスは、モジュール インスタンスの構成に使用できる、対応するモジュール ツインも持ちます。 インスタンスとツインは、モジュール ID によってお互いに関連付けられます。 

モジュール ツインは、モジュールの情報と構成プロパティを格納する JSON ドキュメントです。 この概念は、IoT Hub の[デバイス ツイン][lnk-device-twin]の概念に似ています。 モジュール ツインの構造は、デバイス ツインとまったく同じです。 両方の種類のツインと対話するために使用する API も同じです。 2 つの唯一の違いは、クライアント SDK をインスタンス化するために使用する ID です。 

```
// Create a DeviceClient object. This DeviceClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
await client.OpenAsync(); 
 
// Get the model twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="next-steps"></a>次のステップ
 - [Understand the Azure IoT Edge runtime and its architecture (Azure IoT Edge ランタイムとそのアーキテクチャについて)][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md