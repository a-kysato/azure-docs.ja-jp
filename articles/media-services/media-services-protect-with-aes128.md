---
title: "AES-128 動的暗号化とキー配信サービスの使用 | Microsoft Docs"
description: "Microsoft Azure Media Services では、AES 128 ビット暗号化キーを使用して暗号化したコンテンツを配信できます。 Media Services には、権限のあるユーザーに暗号化キーを配信する、キー配信サービスも用意されています。 このトピックでは、AES-128 を使用して動的に暗号化する方法とキー配信サービスを使用する方法について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 04c015a6fb6f9398e83b8717e869ba1d8e32a702
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2017
---
# <a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>AES-128 動的暗号化とキー配信サービスの使用
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

## <a name="overview"></a>概要
> [!NOTE]
> **Safari on macOS** への配信用に AES でコンテンツを暗号化する場合は、こちらの[ブログ投稿](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)をご覧ください。
> AES 暗号化でメディア コンテンツを保護する方法の概要については、[この](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption)ビデオをご覧ください。
> 
> 

Microsoft Azure Media Services では、Advanced Encryption Standard (AES) (128 ビット暗号化キーを使用) を使用して暗号化された、HTTP ライブ ストリーミング (HLS) とスムーズ ストリームを配信できます。 Media Services には、権限のあるユーザーに暗号化キーを配信する、キー配信サービスも用意されています。 Media Services で資産を暗号化する場合は、暗号化キーを資産に関連付ける必要があります。また、キーの承認ポリシーを構成する必要があります。 プレーヤーがストリームを要求すると、Media Services は指定されたキーを使用して、AES でコンテンツを動的に暗号化します。 ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。 ユーザーのキーの取得が承認されているかどうかを判断するために、サービスはキーに指定した承認ポリシーを評価します。

Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (オープンまたはトークン制限) を指定できます。 トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。 Media Services では、[Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) 形式と [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) 形式のトークンがサポートされます。 詳細については、「 [コンテンツ キー承認ポリシーを構成する](media-services-protect-with-aes128.md#configure_key_auth_policy)」を参照してください。

動的暗号化を使用するには、一連のマルチビットレート MP4 ファイルまたはマルチビットレート Smooth Streaming ソース ファイルを含む資産が必要です。 また、資産の配信ポリシーを構成する必要があります (この記事で後述します)。 次に、ストリーミング URL で指定された形式に基づいて、オンデマンド ストリーミング サーバーは、選択されたプロトコルでストリームを配信できるようにします。 その結果、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて、適切な応答を構築して返します。

この記事には、保護されたメディアを配信するアプリケーションの開発に取り組む開発者にとって有用な情報が含まれています。 この記事では、承認ポリシーを使用するキー配信サービスの構成方法を説明します。これにより、許可されたクライアントのみが暗号化キーを受け取ることができるようになります。 また、動的暗号化の使用方法についても説明します。


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128 動的暗号化とキー配信サービスのワークフロー

以下では、Media Services キー配信サービスと動的暗号化を使用して、AES で資産を暗号化する際に実行する必要のある一般的な手順について説明します。

1. [資産を作成し、その資産にファイルをアップロードする](media-services-protect-with-aes128.md#create_asset)
2. [ファイルが含まれる資産をアダプティブ ビットレート MP4 セットにエンコードする](media-services-protect-with-aes128.md#encode_asset)
3. [コンテンツ キーを作成し、それをエンコードした資産に関連付ける](media-services-protect-with-aes128.md#create_contentkey)」を参照してください。 Media Services では、コンテンツ キーに資産の暗号化キーが含まれています。
4. [コンテンツ キー承認ポリシーの構成](media-services-protect-with-aes128.md#configure_key_auth_policy)コンテンツ キー承認ポリシーを構成する必要があります。 コンテンツ キーがクライアントに配信されるには、クライアントがこのコンテンツ キー承認ポリシーを満たしている必要があります。
5. [資産の配信ポリシーを構成します](media-services-protect-with-aes128.md#configure_asset_delivery_policy)。 配信ポリシーの構成には、次の内容が含まれます。キー取得 URL と初期化ベクトル (IV) (AES 128 では、暗号化と暗号化解除の際に同じ IV を指定する必要があります)、配信プロトコル (たとえば、MPEG DASH、HLS、Smooth Streaming、またはすべて)、動的暗号化の種類 (たとえば、エンベロープ暗号化、または動的暗号化を行わない)。

    同じ資産の各プロトコルに異なるポリシーを適用できます。 たとえば、PlayReady 暗号化を Smooth/DASH に適用し、AES Envelope を HLS に適用できます。 配信ポリシーで定義されていないプロトコル (たとえば、プロトコルとして HLS のみを指定する 1 つのポリシーを追加した場合) は、ストリーミングからブロックされます。 ただし、資産配信ポリシーをまったく定義していない場合は例外となります。 この場合、すべてのプロトコルが平文で許可されます。

6. [OnDemand ロケーターを作成](media-services-protect-with-aes128.md#create_locator) します。

この記事では、[クライアント アプリケーションがキー配信サービスにキーを要求する方法](media-services-protect-with-aes128.md#client_request)についても説明します。

この記事の最後に、詳細な .NET の[例](media-services-protect-with-aes128.md#example)があります。

前述したワークフローを図にすると、次のようになります。 ここでは、認証のためにトークンが使用されています。

![AES-128 での保護](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

この記事の残りの部分では、詳細な説明、コード例、および前述したタスクの実行方法を説明するトピックへのリンクを紹介します。

## <a name="current-limitations"></a>現時点での制限事項
資産の配信ポリシーを追加または更新する場合は、既存のロケーターを削除し (存在する場合)、新しいロケーターを作成する必要があります。

## <a id="create_asset"></a>資産を作成し、その資産にファイルをアップロードする
ビデオの管理、エンコード、およびストリーミングを行うには、最初にコンテンツを Microsoft Azure Media Services にアップロードする必要があります。 コンテンツをアップロードすると、クラウドにコンテンツが安全に保存され、処理したりストリーミングしたりできるようになります。 

詳細については、「 [Media Services アカウントへのファイルのアップロード](media-services-dotnet-upload-files.md)」を参照してください。

## <a id="encode_asset"></a>ファイルが含まれる資産をアダプティブ ビットレート MP4 セットにエンコードする
動的暗号化を使用する場合に必要となるのは、一連のマルチビットレート MP4 ファイルまたはマルチビットレート Smooth Streaming ソース ファイルを含む資産の作成のみです。 そうすれば、マニフェストまたはフラグメント要求で指定された形式に基づき、オンデマンド ストリーミング サーバーによって、ユーザーが選択したプロトコルでストリームを受信するようになります。 その結果、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて、適切な応答を構築して返します。 詳しくは、[ダイナミック パッケージの概要](media-services-dynamic-packaging-overview.md)に関する記事をご覧ください。

>[!NOTE]
>AMS アカウントの作成時に、**既定**のストリーミング エンドポイントが自分のアカウントに追加され、**停止**状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態である必要があります。 
>
>また、ダイナミック パッケージと動的暗号化を使用するには、資産に一連のアダプティブ ビットレート MP4、またはアダプティブ ビットレート Smooth Streaming ファイルが含まれている必要があります。

エンコード手順については、「 [Media Encoder Standard を使用して資産をエンコードする方法](media-services-dotnet-encode-with-media-encoder-standard.md)」を参照してください。

## <a id="create_contentkey"></a>コンテンツ キーを作成し、それをエンコードした資産に関連付ける
Media Services では、コンテンツ キーに、資産を暗号化するときに使用するキーが含まれています。

詳細については、 [コンテンツ キーの作成](media-services-dotnet-create-contentkey.md)に関するページを参照してください。

## <a id="configure_key_auth_policy"></a>コンテンツ キー承認ポリシーの構成
Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー承認ポリシーを構成する必要があります。キーがクライアント (プレーヤー) に配信されるには、クライアントがこのコンテンツ キー承認ポリシーを満たしている必要があります。 コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (オープン、トークン制限、IP 制限) を指定できます。

詳細については、「 [コンテンツ キー承認ポリシーを構成する](media-services-dotnet-configure-content-key-auth-policy.md)」を参照してください。

## <a id="configure_asset_delivery_policy"></a>資産の配信ポリシーを構成する
資産の配信ポリシーを構成します。 資産の配信ポリシーの構成には、次の内容が含まれます。

* キー取得 URL。 
* エンベロープ暗号化に使用する初期化ベクトル (IV)。 AES 128 では、暗号化と暗号化解除に同一の IV が必要です。 
* 資産配信プロトコル (たとえば、MPEG DASH、HLS、Smooth Streaming、またはすべて)。
* 動的暗号化の種類 (AES エンベロープなど) または動的暗号化なし。 

詳しくは、[資産の配信ポリシーの構成](media-services-dotnet-configure-asset-delivery-policy.md)に関するページをご覧ください。

## <a id="create_locator"></a>ストリーミング URL を取得するために OnDemand ロケーターを作成する
Smooth、DASH、HLS のストリーミング URL をユーザーに提供する必要があります。

> [!NOTE]
> 資産の配信ポリシーを追加または更新する場合は、既存のロケーターを削除し (存在する場合)、新しいロケーターを作成する必要があります。
> 
> 

資産を発行し、ストリーミング URL を構築する手順については、「 [Build a streaming URL (ストリーミング URL の構築)](media-services-deliver-streaming-content.md)」をご覧ください。

## <a name="get-a-test-token"></a>テスト トークンを取得する
キー認証ポリシーに使用されたトークンによる制限に基づいて、テスト トークンを取得します。

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

[AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) を使用して、ストリームをテストできます。

## <a id="client_request"></a>クライアントが配信サービスにキーを要求する方法
以前の手順で、マニフェスト ファイルを参照する URL を構成しました。 クライアントは、キー配信サービスへの要求を実行するために、ストリーミング マニフェスト ファイルから必要な情報を抽出する必要があります。

### <a name="manifest-files"></a>マニフェスト ファイル
クライアントはマニフェスト ファイルから URL 値 (コンテンツ キー ID (kid) も含まれる) を抽出する必要があります。 その後、クライアントは、キー配信サービスからの暗号化キーの取得を試みます。 また、クライアントは、IV 値を抽出し、それを使ってストリームの暗号化を解除する必要もあります。 次のスニペットでは、Smooth Streaming マニフェストの <Protection> 要素を示します。

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

HLS の場合、ルート マニフェストはセグメント ファイルに分割されます。 

たとえば、ルート マニフェストは http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) で、そこにはセグメント ファイル名の一覧が含まれています。

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

セグメント ファイルのいずれかをテキスト エディターで開くと (たとえば、http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl)、それには #EXT-X-KEY キーが含まれ、ファイルが暗号化されていることを示します。

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>Safari で AES 暗号化された HLS を再生することを計画している場合には、[このブログ](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)をご覧ください。

### <a name="request-the-key-from-the-key-delivery-service"></a>キー配信サービスにキーを要求する

次のコードは、キー配信 URI (マニフェストから抽出) とトークンを使用して、Media Services のキー配信サービスに要求を送信する方法を示しています (Secure Token Service から Simple Web Token を取得する方法は、この記事では扱いません)。

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }

## <a name="protect-your-content-with-aes-128-using-net"></a>AES 128 によるコンテンツの保護 (.NET 使用)

### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio プロジェクトの作成と構成

1. 「[.NET を使用した Media Services 開発](media-services-dotnet-how-to-use.md)」の説明に従って、開発環境をセットアップし、app.config ファイルに接続情報を指定します。 
2. app.config ファイルで定義されている **appSettings** に次の要素を追加します。

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

### <a id="example"></a>例

Program.cs ファイルのコードを、このセクションで示されているコードで上書きします。
 
>[!NOTE]
>さまざまな AMS ポリシー (ロケーター ポリシーや ContentKeyAuthorizationPolicy など) に 1,000,000 ポリシーの制限があります。 常に同じ日数、アクセス許可などを使う場合は、同じポリシー ID を使う必要があります (たとえば、長期間存在するように意図されたロケーターのポリシー (非アップロード ポリシー))。 詳細については、[こちらの記事](media-services-dotnet-manage-entities.md#limit-access-policies)を参照してください。

必ず変数を更新して、入力ファイルが置かれているフォルダーをポイントするようにしてください。

[!code-csharp[Main](../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

