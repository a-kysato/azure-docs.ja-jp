---
title: "Azure Log Analytics の IT Service Management Connector | Microsoft Docs"
description: "この記事では、IT Service Management Connector (ITSMC) の概要を説明し、このソリューションを使用して ITSM の作業項目を OMS Log Analytics で一元的に監視および管理して、問題を迅速に解決する方法に関する情報を示します。"
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: v-jysur
ms.openlocfilehash: bd384255b3c46b3ae88b1269ab26e0ddaa6f6e77
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2017
---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>IT Service Management Connector を使用した ITSM 作業項目の一元管理 (プレビュー)

![IT Service Management Connector シンボル](./media/log-analytics-itsmc/itsmc-symbol.png)

IT Service Management Connector (ITSMC) では、サポートされている IT Service Management (ITSM) 製品/サービスと Log Analytics 間の双方向の統合が提供されます。  この接続を介して、Log Analytics のアラート、ログ レコード、または Azure のアラートに基づいて ITSM 製品でインシデント、アラート、またはイベントを作成できます。 また、このコネクタは、インシデントなどのデータや ITSM 製品からの変更要求を OMS Log Analytics にインポートします。

ITSMC を使用すると、次のことができます。

  - 任意の ITSM ツールで操作アラートとインシデント管理プラクティスを統合する。
    - OMS アラートとログ検索から、ITSM で作業項目 (アラート、イベント、インシデントなど) を作成する。
    - アクション グループの ITSM アクションを使用して、Azure アクティビティ ログ アラートに基づいて作業項目を作成する。

  - 組織全体で使用される監視、ログ、およびサービス管理データを統合する。
    - ITSM ツールのインシデントと変更要求データを、Log Analytics ワークスペースの関連するログ データに関連付ける。   
    - インシデント、変更要求、影響を受けるシステムの概要を示すトップレベルのダッシュボードを表示する。
    - サービス管理データを洞察するための Log Analytics クエリを記述する。

## <a name="adding-the-it-service-management-connector-solution"></a>IT Service Management Connector ソリューションの追加

[ソリューション ギャラリーからの Log Analytics ソリューションの追加](log-analytics-add-solutions.md)に関するページで説明されているプロセスを使用して、IT Service Management Connector ソリューションを Log Analytics ワーク スペースに追加します。

ソリューション ギャラリーに次の ITSMC タイルが表示されます。

![コネクタ タイル](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

追加が正常に行われると、**[OMS]** > **[設定]** > **[接続されたソース]** で IT Service Management Connector が確認できます。

![ITSMC の接続](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

> [!NOTE]

> 既定では、ITSMC は、接続のデータを 24 時間に 1 回更新します。 編集内容やテンプレートの更新を反映するために接続のデータをすぐに更新するには、接続の横に表示される [更新] ボタンをクリックします。

 ![ITSMC の更新](./media/log-analytics-itsmc/itsmc-connection-refresh.png)


## <a name="configuring-the-itsmc-connection-with-your-itsm-productsservices"></a>ITSM 製品/サービスとの ITSMC 接続を構成する

ITSMC では、**System Center Service Manager**、**ServiceNow**、**Provance**、および **Cherwell** への接続がサポートされます。

状況に合わせて次の手順を使用します。

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>ソリューションの使用

コネクタを構成したら、接続されている ITSM 製品/サービスのデータの収集が開始されます。 ITSM 製品/サービスのインシデントや変更要求の数によっては、初期同期が完了するまで数分かかります。

> [!NOTE]
> - ITSMC ソリューションによって ITSM 製品からインポートされたデータは、**ServiceDesk_CL** タイプのログ レコードとして Log Analytics に表示されます。
> - ログ レコードには、**ServiceDeskWorkItemType_s** という名前のフィールドが含まれ、インシデントまたは変更要求 (ITSM 製品からインポートされた 2 種類のデータ) が示されます。

## <a name="data-synced-from-itsm-product"></a>ITSM 製品から同期されるデータ
インシデントと変更要求は、ITSM 製品から Log Analytics ワークスペースに同期されます。
ITSMC によって収集されるデータの例を以下に示します。

> [!NOTE]

> Log Analytics にインポートされる作業項目の種類によっては、**ServiceDesk_CL** には以下のフィールドが含まれます。

**作業項目:** **インシデント**  
ServiceDeskWorkItemType_s="Incident"

**フィールド**

- サービス デスク接続名
- サービス デスク ID
- 状態
- 緊急度
- 影響
- 優先順位
- エスカレーション
- 作成者
- 解決者
- 終了者
- から
- 割当先
- カテゴリ
- タイトル
- 説明
- 作成日
- 終了日
- 解決日
- 最終更新日
- コンピューター


**作業項目:** **変更要求**

ServiceDeskWorkItemType_s="ChangeRequest"

**フィールド**
- サービス デスク接続名
- サービス デスク ID
- 作成者
- 終了者
- から
- 割当先
- タイトル
- 型
- カテゴリ
- 状態
- エスカレーション
- 競合状態
- 緊急度
- 優先順位
- リスク
- 影響
- 割当先
- 作成日
- 終了日
- 最終更新日
- 要求日
- 開始予定日
- 終了予定日
- 作業開始日
- 作業終了日
- 説明
- コンピューター

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow インシデントの出力データ

| OMS のフィールド | ITSM のフィールド |
|:--- |:--- |
| ServiceDeskId_s| 番号 |
| IncidentState_s | 状態 |
| Urgency_s |緊急度 |
| Impact_s |影響|
| Priority_s | 優先順位 |
| CreatedBy_s | 開始者 |
| ResolvedBy_s | 解決者|
| ClosedBy_s  | 終了者 |
| Source_s| 連絡先の種類 |
| AssignedTo_s | 割り当て先  |
| Category_s | カテゴリ |
| Title_s|  簡単な説明 |
| Description_s|  メモ |
| CreatedDate_t|  開始済み |
| ClosedDate_t| 終了済み|
| ResolvedDate_t|解決済み|
| Computer  | 構成項目 |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 変更要求の出力データ

| OMS のフィールド | ITSM のフィールド |
|:--- |:--- |
| ServiceDeskId_s| 番号 |
| CreatedBy_s | 要求者 |
| ClosedBy_s | 終了者 |
| AssignedTo_s | 割り当て先  |
| Title_s|  簡単な説明 |
| Type_s|  型 |
| Category_s|  カテゴリ |
| CRState_s|  状態|
| Urgency_s|  緊急度 |
| Priority_s| 優先順位|
| Risk_s| リスク|
| Impact_s| 影響|
| RequestedDate_t  | Requested by date\(要求日\) |
| ClosedDate_t | 終了日 |
| PlannedStartDate_t  |     開始予定日 |
| PlannedEndDate_t  |   終了予定日 |
| WorkStartDate_t  | 実際の開始日 |
| WorkEndDate_t | 実際の終了日|
| Description_s | 説明 |
| Computer  | 構成項目 |

**ITSM データに関する Log Analytics 画面 (サンプル):**

![Log Analytics 画面](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="itsmc-integration-with-other-oms-solutions"></a>その他の OMS ソリューションとの ITSMC 統合

現在、ITSM Connector では Service Map ソリューションとの統合がサポートされています。

サービス マップは、Windows と Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 これを使用すると、サーバーを重要なサービスを提供する相互接続されたシステムとして表示することができます。 サービス マップは、TCP 接続アーキテクチャ全体のサーバー、プロセス、ポート間の接続を表示します。エージェントのインストール以外の構成は必要ありません。

詳細: [サービス マップ](../operations-management-suite/operations-management-suite-service-map.md)。

Service Map ソリューションも使用する場合は、ITSM ソリューションで作成されたサービス デスク項目が次の例のように表示されます。

![Service Map の統合](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>OMS アラートの ITSM 作業項目を作成する

ITSMC ソリューションを適用して、接続されている ITSM ツールで作業項目の作成をトリガーするように OMS アラートを構成することができます。 次の手順に従います。

1. **[ログ検索]** ウィンドウで、ログ検索クエリを実行しデータを表示します。 クエリ結果は、作業項目のソースです。
2. **[ログ検索]** ウィンドウで、**[アラート]** をクリックし、**[アラート ルールの追加]** ページを開きます。

    ![Log Analytics 画面](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. **[アラート ルールの追加]** ウィンドウで、**[名前]**、**[重大度]**、**[検索クエリ]**、**[アラートの条件]** ([時間枠] や [メトリック測定]) などの必要な情報を確認できます。
4. **[ITSM 操作]** で **[はい]** を選択します。
5. **[接続を選択]** リストで、お使いの ITSM 接続を選択します。
6. 必要に応じてその他の情報も入力します。
7. このアラートについて各ログ エントリに個別の作業項目を作成するには、**[各ログ エントリに対して、個々の作業項目を作成します]** チェックボックスをオンします。

    または

    このアラートについてログ エントリの数に関係なく 1 つの作業項目を作成するには、このチェックボックスをオフのままにしておきます。

7. **[保存]**をクリックします。

作成した OMS アラートは、**[設定]**>**[アラート]** で確認できます。 指定したアラートの条件が満たされた場合は、それに対応する ITSM 接続の作業項目が作成されます。

## <a name="create-itsm-work-items-from-oms-logs"></a>OMS ログから ITSM 作業項目を作成する

ログ レコードから直接接続された ITSM ソースで作業項目を作成することもできます。 次の手順に従います。

1. **[ログ検索]** で、必要なデータを検索し、詳細を選択して **[作業項目の作成]** をクリックします。

    **[ITSM 作業項目の作成]** ウィンドウが表示されます。

    ![Log Analytics 画面](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   次の情報を追加します。

  - **[作業項目のタイトル]**: 作業項目のタイトル。
  - **[作業項目の説明]**: 新しい作業項目についての説明。
  - **[影響を受けるコンピューター]**: このログ データが見つかったコンピューターの名前。
  - **[接続を選択]**: この作業項目を作成する ITSM 接続。
  - **[作業項目]**: 作業項目の種類。

3. インシデントの既存の作業項目テンプレートを使用するには、**[テンプレートに基づいて作業項目を生成する]** オプションで **[はい]** を選択し、**[作成]** をクリックします。

    または、

    カスタムした値を入力する場合は **[いいえ]** をクリックします。

4. **[連絡先の種類]**、**[影響]**、**[緊急度]**、**[カテゴリ]**、**[サブ カテゴリ]** のテキスト ボックスに適切な値を入力し、**[作成]** をクリックします。

## <a name="create-itsm-work-items-from-azure-alerts"></a>Azure アラートから ITSM 作業項目を作成する

ITSMC はアクション グループと統合されています。

[アクション グループ](../monitoring-and-diagnostics/monitoring-action-groups.md)では、Azure アラートのアクションをトリガーする再利用可能なモジュール化された方法が提供されます。 アクション グループの ITSM アクションを使用して、ITSM Connector ソリューションへの接続が既に確保されている ITSM 製品で作業項目を作成できます。

次の手順に従います。

1. Azure Portal で、**[監視]** をクリックします。
2. 左側のウィンドウで、**[アクション グループ]** をクリックします。 **[アクション グループの追加]** ウィンドウが表示されます。

    ![アクション グループ](media/log-analytics-itsmc/action-groups.png)

3. アクション グループの**名前**と**短い名前**を入力します。 アクション グループを作成する**リソース グループ**と**サブスクリプション**を選択します。

    ![アクション グループの詳細](media/log-analytics-itsmc/action-groups-details.png)

4. アクション リストで、**[アクションの種類]** のドロップダウン メニューから **[ITSM]** を選択します。 アクションの**名前**を入力し、**[詳細の編集]** をクリックします。
5. Log Analytics ワークスペースが配置される**サブスクリプション**を選択します。 ご使用のワークスペース名が後に続く**接続**名 (ITSM Connector の名前) を選択します。 たとえば、"MyITSMMConnector(MyWorkspace)" などです。

    ![ITSM アクションの詳細](./media/log-analytics-itsmc/itsm-action-details.png)

6. ドロップダウン メニューから **[作業項目]** の種類を選択します。
   既存のテンプレートを使用するか、ITSM 製品に必要なフィールドに入力します。
7. **[OK]**をクリックします。

Azure のアラート ルールを作成/編集する場合は、ITSM アクションを含むアクション グループを使用します。 アラートがトリガーされると、ITSM ツールで作業項目が作成されます。

>[!NOTE]

> 現時点では、ITSM アクションをサポートしているのは Activity Log Alerts だけです。その他の Azure アラートはこれをサポートしていません。


## <a name="troubleshoot-itsm-connections-in-oms"></a>OMS の ITSM 接続のトラブルシューティング
1.  接続されているソースの UI からの接続が失敗し、"**接続の保存中にエラーが発生しました**" というメッセージが表示された場合は、次の手順を実行してください。
 - ServiceNow、Cherwell、Provance の接続の場合 - 各接続のユーザー名、パスワード、クライアント ID、およびクライアント シークレットが正しく入力されていることを確認します。
        - 対応する ITSM 製品で接続を作成するための十分な特権があるかどうかを確認します。
 - Service Manager の接続の場合 - Web アプリが正常にデプロイされ、ハイブリッド接続が作成されていることを確認します。 オンプレミスの Service Manager コンピューターとの接続が正常に確立されていることを確認するには、[ハイブリッド接続](log-analytics-itsmc-connections.md#configure-the-hybrid-connection)の作成に関するドキュメントで詳述するように、Web アプリの URL にアクセスします。

2.  ServiceNow のデータが Log Analytics に同期されていない場合は、ServiceNow インスタンスがスリープ状態でないことを確認します。 ServiceNow Dev インスタンスは、長時間アイドル状態になっている場合、スリープ状態になることがあります。 それ以外の場合は、問題を報告してください。
3.  OMS アラートが発生しても、ITSM 製品で作業項目が作成されない場合や、構成項目が作成されない/作業項目にリンクされない場合、または一般情報が必要な場合は、以下を確認してください。
 -  ITSMC: このソリューションでは、接続/作業項目/コンピューターなどの概要が表示されます。**接続の状態**を示すタイルをクリックします。これにより、関連するクエリを使用する **[ログ検索]** が表示されます。 詳細については、LogType_S が ERROR のログ レコードを確認してください。
 - **[ログ検索]** ページ: *Type=ServiceDeskLog_CL* というクエリを使用して直接エラー/関連情報を表示します。

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager Web アプリのデプロイのトラブルシューティング
1.  Web アプリのデプロイに関する問題が発生した場合は、該当するサブスクリプションでリソースを作成/デプロイするための十分なアクセス許可があることを確認します。
2.  [スクリプト](log-analytics-itsmc-service-manager-script.md)の実行時に "**オブジェクト参照がオブジェクトのインスタンスに設定されていません**" というエラーが発生した場合は、**[ユーザー構成]** セクションに有効な値が入力されていることを確認します。
3.  Service Bus Relay 名前空間を作成できない場合は、必要なリソース プロバイダーがサブスクリプションに登録されていることを確認します。 登録されていない場合は、Azure Portal で Service Bus Relay 名前空間を手動で作成します。 リソース プロバイダーは、Azure Portal で[ハイブリッド接続を作成](log-analytics-itsmc-connections.md#configure-the-hybrid-connection)するときに作成することもできます。


## <a name="contact-us"></a>お問い合わせ

IT Service Management Connector に関するご質問やフィードバックは、[omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) にお寄せください。

## <a name="next-steps"></a>次のステップ
[ITSM 製品/サービスを IT Service Management Connector に追加する](log-analytics-itsmc-connections.md)。
