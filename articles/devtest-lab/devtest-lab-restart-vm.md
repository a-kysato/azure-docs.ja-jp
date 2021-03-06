---
title: "ラボ内の VM を Azure DevTest Labs で再起動する | Microsoft Docs"
description: "Azure DevTest Labs で仮想マシンを再起動する方法を説明します"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: tarcher
ms.openlocfilehash: 4e46a7b21c8c9900ba61e9cf41000b341645133c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>ラボ内の VM を Azure DevTest Labs で再起動する
この記事の手順に従うと、DevTest Labs で仮想マシンをすばやく簡単に再起動することができます。 VM を再起動する前に、次をご考慮ください。

- 再起動の機能を有効にするには、VM が実行されている必要があります。
- 再起動の実行時に、実行中の VM に接続していたユーザーは、バックアップを開始した後に VM に再接続する必要があります。
- VM を再起動するときに成果物が適用される場合は、成果物が適用されない可能性があるとの警告が表示されます。 

    ![再起動時に成果物を適用している間に表示される警告](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > 成果物を適用中に VM が停止した場合は、VM の再起動の機能を使用すると、問題が解決されることがあります。
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>ラボ内の VM を Azure DevTest Labs で再起動する手順
1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
1. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で、再起動する VM が含まれているラボを選択します。  
1. 左側のパネルで、**[仮想マシン]** を選択します。 
1. VM の一覧で、実行中の VM を選択します。
1. VM の管理ウィンドウの上部で **[再起動]** を選択します。  

    ![VM の再起動ボタン](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. ウィンドウの右上にある **[通知]** アイコンを選択することで、再起動の状態を監視します。

    ![VM 再起動の状態の表示](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

**[仮想マシン]** の一覧で実行中の VM の省略記号 (...) を選択して、その VM を再起動することもできます。

![省略記号を使用して VM を再起動する](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>次のステップ
* 再起動の後、VM の管理ウィンドウで **[接続]** を選択すれば、VM に再接続できます。
* [DevTest Labs Azure Resource Manager のクイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-devtestlab/tree/master/Samples)を探索します。
