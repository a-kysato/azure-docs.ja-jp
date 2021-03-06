---
title: "Azure Event Hubs Dedicated 容量の概要 | Microsoft Docs"
description: "Microsoft Azure Event Hubs Dedicated 容量の概要。"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: sethm;babanisa
ms.openlocfilehash: 613ea691e38b6f0bcd8873fc2ec6bcafb3cc6c78
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2017
---
# <a name="overview-of-event-hubs-dedicated"></a>Event Hubs Dedicated の概要

*Event Hubs Dedicated* 容量は、最も厳しい要件を持つお客様にシングル テナント デプロイを提供します。 フルスケールの Azure Event Hubs では、十分な耐久性を備えたストレージと 1 秒未満の待機時間により、1 秒当たり 200 万件以上のイベントまたは 1 秒当たり最大 2 GB のテレメトリを受信できます。 また、同じシステム上でのリアルタイム処理とバッチ処理により、統合されたソリューションを実現できます。 このプランに含まれる [Event Hubs Capture](event-hubs-capture-overview.md) では、1 つのストリームでリアルタイムのパイプラインとバッチ ベースのパイプラインの両方をサポートすることで、ソリューションの複雑さが軽減できます。

次の表では、Event Hubs で提供されるサービス レベルを比較しています。 Standard のほとんどの機能が従量課金であるのに対し、Event Hubs Dedicated プランは月額固定価格となっています。 Dedicated レベルの機能は Standard プランと同じですが、要求の厳しいワークロードを実行するお客様向けにエンタープライズ スケールの容量で提供されます。 

| 機能 | 標準 | 専用 |
| --- |:---:|:---:|:---:|
| イングレス イベント | 100 万イベントごとの課金 | あり |
| スループット単位 (1 MB/秒イングレス、2 MB/秒エグレス) | 1 時間ごとの課金 | あり |
| メッセージ サイズ | 256 KB | 1 MB |
| パブリッシャー ポリシー | あり | はい |   
| コンシューマー グループ | 20 | 20 |
| メッセージ リプレイ | はい | はい |
| 最大スループット ユニット | 20 (～ 100)   | 1 CU は約 50 |
| 仲介型接続 | 1,000 (付属) | 100,000 (付属) |
| 追加の仲介型接続 | あり | はい |
| メッセージのリテンション期間 | 1 日分が含まれます | 最大 7 日分が含まれます |
| キャプチャ | 1 時間ごとの課金 | あり |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Event Hubs Dedicated 容量の利点

Event Hubs Dedicated を使用することで実現する利点を次に示します。

* 他のテナントからのノイズがないシングル テナント ホスティング。
* メッセージ サイズを 1 MB に拡大 (Standard では 256 KB)。
* パフォーマンスを常に再現可能。
* ニーズの急増に対応するための容量を保証。
* マイクロバッチおよび長期リテンション期間と統合できるように、Azure Event Hubs の[キャプチャ](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)機能が含まれます。
* メンテナンス不要: 負荷分散、OS の更新、セキュリティ パッチ、パーティション分割は Microsoft が管理。
* 固定の時間単位料金。
* 追加料金なしで最大 7 日間のメッセージ リテンション期間。

Event Hubs Dedicated では、Standard プランのスループットの制限もいくつか削除されます。 Standard レベルのスループット単位では、1 秒あたり 1000 件のイベント、または TU あたり 1 Mbps の受信とその倍量の送信が提供されます。 Dedicated スケール プランには、イングレス イベントとエグレス イベントの数に制限はありません。 これらの制限は、購入したイベント ハブの処理容量によってのみ決定されます。

この予約された専用の環境では、次のようなこのレベル固有の他の機能が提供されます。

* クラスター内の名前空間の数の制御
* 各名前空間に対するスループット制限の指定
* 各名前空間に含まれる Event Hubs の数の構成
* パーティションの数に対する制限の決定

このサービスは最大規模のテレメトリ ユーザーを対象としており、エンタープライズ契約で提供されます。

## <a name="how-to-onboard"></a>利用を開始する方法

CU を追加または削除することによって、1 か月の間にニーズに合わせて容量をスケールアップまたはスケールダウンできます。 Dedicated プランは、お客様に適した柔軟なデプロイを実現するために、Event Hubs 製品チームからより実践的な利用方法が提供されるという点でほかにはないサービスとなっています。 この SKU の利用を始めるには、(課金サポート)[https://ms.portal.azure.com/#create/Microsoft.Support]または Microsoft の担当者にお問い合わせください。

## <a name="next-steps"></a>次のステップ
Event Hubs Dedicated 容量の詳細については、Microsoft の営業担当者または Microsoft サポートにお問い合わせください。 次のリンク先で、Event Hubs の詳細を確認することもできます。

価格については、次のリンクを参照してください。

- [Event Hubs Dedicated の価格](https://azure.microsoft.com/pricing/details/event-hubs/)。 Microsoft の営業担当者または Microsoft サポートに連絡して、Event Hubs Dedicated 容量の詳細を確認することもできます。
- 「[Event Hubs の FAQ](event-hubs-faq.md)」では、Event Hubs の価格の情報について説明し、よく寄せられる質問のいくつかに回答します。 
