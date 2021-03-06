---
title: "Azure File Sync (プレビュー) サーバー エンドポイントの追加/削除 | Microsoft Docs"
description: "Azure Files のデプロイを計画するときの考慮事項について説明します。"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 2ab14183a0ca4ade7873dbdece407937a746b663
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2017
---
# <a name="addremove-an-azure-file-sync-preview-server-endpoint"></a>Azure File Sync (プレビュー) サーバー エンドポイントの追加/削除
Azure ファイル同期 (プレビュー) を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を損なわずに Azure Files で組織のファイル共有を一元化できます。 これは、Windows Server を Azure ファイル共有のクイック キャッシュに変換することで行います。 Windows Server で使用可能な任意のプロトコル (SMB、NFS、FTPS など) を使用してデータにローカル アクセスすることができ、世界中に必要な数だけキャッシュを持つことができます。

"*サーバー エンドポイント*" は、"*登録済みサーバー*" 上の特定の場所 (サーバー ボリュームのフォルダーやボリュームのルートなど) を表します。 名前空間が重複していなければ、同じボリューム上に複数のサーバー エンドポイントが存在する可能性があります (たとえば、F:\sync1 と F:\sync2 など)。 サーバー エンドポイントごとに、クラウドの階層化ポリシーを個別に構成できます。 既存の一連のファイルを含むサーバーの場所をサーバー エンドポイントとして同期グループに追加すると、それらのファイルは、同期グループの他のエンドポイント上に既にある他のファイルと統合されます。

Azure ファイル同期をエンドツーエンドでデプロイする方法の詳細については、「[How to deploy Azure File Sync (preview)](storage-sync-files-deployment-guide.md)」(Azure ファイル同期 (プレビュー) をデプロイする方法) を参照してください。

## <a name="prerequisites"></a>前提条件
サーバー エンドポイントを作成するには、まず次の条件を満たしていることを確認する必要があります。 
- サーバーに Azure ファイル同期エージェントがインストールされ、登録されていること。 Azure ファイル同期エージェントをインストールする手順については、「[Register/unregister a server with Azure File Sync (preview)](storage-sync-files-server-registration.md)」(Azure ファイル同期 (プレビュー) へのサーバーの登録/登録解除) の記事を参照してください。 
- ストレージ同期サービスがデプロイされていること。 ストレージ同期サービスのデプロイ方法の詳細については、「[How to deploy Azure File Sync (preview)](storage-sync-files-deployment-guide.md)」(Azure ファイル同期 (プレビュー) をデプロイする方法) を参照してください。 
- 同期グループがデプロイされていること。 [同期グループの作成方法](storage-sync-files-deployment-guide.md#create-a-sync-group)に関するセクションをご覧ください。
- サーバーがインターネットに接続され、Azure にアクセスできること。

## <a name="add-a-server-endpoint"></a>サーバー エンドポイントを追加する
サーバー エンドポイントを追加するには、目的の同期グループに移動し、[サーバー エンドポイントの追加] を選択します。

![[同期グループ] ウィンドウで新しいサーバー エンドポイントを追加する](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

**[サーバー エンドポイントの追加]** では、次の情報を指定する必要があります。

- **登録済みサーバー**: サーバー エンドポイントを作成するサーバーまたはクラスターの名前。
- **パス**: 同期グループの一部として同期する Windows Server 上のパス。
- **クラウドの階層化**: クラウドの階層化の有効または無効を切り替えるスイッチです。頻繁に使用またはアクセスするファイルを Azure Files に階層化することができます。
- **ボリュームの空き領域**: サーバー エンドポイントが配置されているボリュームに確保する空き領域のサイズ。 たとえば、単一のサーバー エンドポイントで [ボリュームの空き領域] をボリュームの 50% に設定すると、データの約半量が Azure Files に階層化されます。 クラウドの階層化が有効かどうかにかかわらず、Azure ファイル共有は、データの完全なコピーを常に同期グループ内に保持します。

**[作成]** を選択してサーバー エンドポイントを追加します。 これで、同期グループの名前空間内のファイルは同期状態が維持されるようになります。 

## <a name="remove-a-server-endpoint"></a>サーバー エンドポイントを削除する
サーバー エンドポイントでクラウドの階層化を有効にすると、ファイルが Azure ファイル共有に "*階層化*" されます。 その結果、オンプレミスのファイル共有は、データセットの完全なコピーでなくキャッシュとして動作するので、ファイル サーバー上の領域を効率的に使用できるようになります。 ただし、階層化されたファイルがサーバーのローカルにまだある状態でサーバー エンドポイントを削除すると、それらのファイルにアクセスできなくなります。 そのため、ファイルのアクセスを継続するには、Azure Files から階層化されたファイルをすべて回収してから、登録解除を進める必要があります。 

この処理は、次のように PowerShell コマンドレットを使用して実行できます。

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> サーバーをホストするローカル ボリュームに、階層化されたデータをすべて回収できる空き領域がない場合、`Invoke-StorageSyncFileRecall` コマンドレットは失敗します。  

サーバー エンドポイントを削除するには、次の手順に従います。

1. サーバーが登録されているストレージ同期サービスに移動します。
2. 目的の同期グループに移動します。
3. ストレージ同期サービスの同期グループ内にある目的のサーバー エンドポイントを削除します。 これは、[同期グループ] ウィンドウの関連するサーバー エンドポイントを右クリックして実行できます。

    ![同期グループからサーバー エンドポイントを削除する](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>次のステップ
- [Azure ファイル同期 (プレビュー) へのサーバーの登録/登録解除](storage-sync-files-server-registration.md)
- [Azure ファイル同期のデプロイの計画](storage-sync-files-planning.md)