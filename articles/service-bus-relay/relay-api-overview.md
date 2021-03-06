---
title: "Azure リレー API の概要 | Microsoft Docs"
description: "使用可能な Azure リレー API の概要"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 60413bd110fd4021b070f94d6338a50e1b321dea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="available-relay-apis"></a>使用可能なリレー API

## <a name="runtime-apis"></a>ランタイム API

現在使用できるリレー ランタイム クライアントの一覧を次の表に示します。

各ランタイム ライブラリの詳細については、「[追加情報](#additional-information)」セクションをご覧ください。

| 言語/プラットフォーム | 使用可能な機能 | クライアント パッケージ | リポジトリ |
| --- | --- | --- | --- |
| .NET Standard | Hybrid Connections | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF リレー | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | 該当なし |
| ノード | Hybrid Connections | [`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>追加情報

#### <a name="net"></a>.NET
.NET エコシステムには複数のランタイムがあるため、Event Hubs に対して複数の .NET ライブラリがあります。 .NET Standard ライブラリは、.NET Core または .NET Framework のいずれかを使用して実行できますが、.NET Framework ライブラリについては、.NET Framework 環境でのみ実行できます。 .NET Framework の詳細については、「[フレームワークのバージョン](/dotnet/articles/standard/frameworks#framework-versions)」を参照してください。

## <a name="next-steps"></a>次のステップ
Azure リレーの詳細については、次のリンク先を参照してください。
* [What is Azure Relay? (Azure Relay とは)](relay-what-is-it.md)
* [Relay に関する FAQ](relay-faq.md)