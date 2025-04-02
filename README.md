# 🌌 Astralane.io ドキュメント (Japanese)

## アストラレーンへようこそ  
### スピード時代に向けてブロックチェーン性能を革新する

アストラレーンは、Solanaインフラの複雑な作業を引き受け、あなたがイノベーションに集中できる環境を提供します。  
グローバルRPCとインデックス化されたデータ、超高速なトランザクションスピード、そしてお客様のニーズに応じたカスタムソリューションにより、あなたのプロジェクトを強力にサポートします。

> **インフラ構築は私たちに任せて、あなたは世界を変えることに集中しましょう！**

---

## 🚅 低レイテンシー — クイックスタート

最小限のワイヤ遅延を求めるHFT（高頻度取引）事業者から、ピーク時にも確実な約定を求めるマーケットメーカーまで、  
Solana上で最も要求の厳しいオペレーター向けに、スロット以下の速度でトランザクションを伝播する低レイテンシーなインフラを提供します。

リーダーパスの最適化に加え、バイナリ最適化されたメッシュネットワークにより、  
スロット未満の精密なデータストリームを実現します。

さらに、動的な優先順位システムが高優先度のオペレーションにインテリジェントにリソースを割り当て、  
エンタープライズグレードの信頼性と24時間365日のサポート体制でバックアップします。

---

## 🚅 エンドポイントと設定 — 稼働に必要なすべてがここに

### 🔗 **エンドポイント: Iris（アイリス）**
トランザクション送信用エンドポイント：

- フランクフルト: `http://fr.gateway.astralane.io/iris?api-key=xxxx`
- 東京: `http://jp.gateway.astralane.io/iris?api-key=xxxx`
- ニューヨーク: `http://ny.gateway.astralane.io/iris?api-key=xxxx`

---

### 🔭 **Paladin（パラディン）リーダートラッキング**
- エンドポイント: `http://fr.gateway.astralane.io/api/paladin`

#### 利用可能なAPIコール:
- `sendTransaction`  
- `sendBundle`  
- `sendIdeal`  
- `getNonce`  
- `sendPaladin`

---

## 💸 チップ用アドレス

astrazznxsGUhWShqgNtAdfrzP2G83DzcWVJDxwV9bF
astra4uejePWneqNaJKuFFA8oonqCE1sqF6b45kDMZm
astra9xWY93QyfG6yM8zwsKsRodscjQ2uU2HKNL5prk
astraRVUuTHjpwEVvNBeQEgwYx9w9CFyfxjYoobCZhL


---

## 📊 レート制限

| エンドポイント | デフォルトTPS |
|----------------|----------------|
| Iris           | 5 TPS          |
| Paladin        | 1 TPS          |

> 💬 追加TPSやカスタムリベートプランが必要な場合は、**Telegramにてお問い合わせください。**

---

## 💰 最小チップ額

- **Iris エンドポイント**  
  各トランザクションに最低 `0.0001 SOL` のチップを設定し、  
  チップアドレスへのシステム送金命令を含める必要があります。

- **Paladin エンドポイント**  
  各トランザクションに最低 `0.00XX SOL` のチップを設定する必要があります。

✅ 高度なトランザクション送信者向けには、**カスタム価格設定プラン**により、  
最小チップ額の引き下げや免除が可能な柔軟なプランも提供しています。

---

## 🛠️ 効率的な活用方法

Paladin Leader API エンドポイントを使用して、**アクティブなリーダーとスロットのタイミング**を追跡しましょう。  
これにより、**チップや手数料を適切に調整**し、コストを最適に管理できます。  
必要に応じて、**専用のエンドポイント経由で設定を更新**してください。

---
🚀 トランザクションの送信
トランザクションの送信方法
Astralane は、お客様のニーズに合わせてさまざまなオプションを提供しています。ここでは、トランザクション送信用に提供している RPC メソッドの一部を紹介します。

sendTransaction
この RPC 呼び出しは、すべての Solana ライブラリと完全に互換性があるため、既存のワークフローを簡単に置き換えることができます。Jito や Paladin (より高い最小チップ) などのパートナーの SWQoS クライアントを介してルーティングされるため、トランザクション処理が最適化され、信頼性が最大限に高まります。

RPC URL を配置する場所に URL を挿入し、通常の送信方法でトランザクションを送信するだけです。変更点は、チップに命令を追加することだけです。

const TIP: Pubkey = pubkey!("astra4uejePWneqNaJKuFFA8oonqCE1sqF6b45kDMZm"); // Use tip wallet depending on region of access
const MIN_TIP_AMOUNT: u64 = 100_000; // added for spam prevention

fn send_tx_tipped(
    ixs: &mut Vec<Instruction>, 
    signer: &Keypair, 
    rpc_client: &RpcClient
    ) {
    let tip_ix = system_instruction::transfer(&signer.pubkey(), &TIP, MIN_TIP_AMOUNT);
    ixs.push(tip_ix);

    let blockhash = rpc_client.get_latest_blockhash().unwrap();
    let tx = Transaction::new_signed_with_payer(ixs, Some(&signer.pubkey()), &[signer], blockhash);

    rpc_client.send_transaction(&tx).unwrap();
}

sendBundle
操作がアトミック トランザクション実行に依存している場合は、sendBundle エンドポイントを使用してアトミックに実行されるトランザクションを送信します。

1 つのアトミック バンドルで最大 4 つのトランザクションを送信します。これらのトランザクションは順番に実行され、精度と信頼性が確保されます。いずれかのトランザクションが失敗すると、バンドル全体が元に戻されるため、一貫性が保証され、部分的な失敗が排除されます

//Rust

const TIP: Pubkey = pubkey!("astra4uejePWneqNaJKuFFA8oonqCE1sqF6b45kDMZm"); // Use tip wallet depending on region of access
const MIN_TIP_AMOUNT: u64 = 100_000; // added for spam prevention

async fn send_bundle(
    ixs: &mut Vec<Instruction>,
    signer: &Keypair,
    client: reqwest::Client,
    blockhash: Hash,
    url: String,
) {
    let tip_ix = system_instruction::transfer(&signer.pubkey(), &TIP, MIN_TIP_AMOUNT);
    ixs.push(tip_ix);
    let tx = Transaction::new_signed_with_payer(ixs, Some(&signer.pubkey()), &[signer], blockhash);
    let encoded_tx = base64::prelude::BASE64_STANDARD.encode(&bincode::serialize(tx).unwrap());
    let response = client
        .post(url)
        .header("api_key", "xxx")
        .json(&json! ({
            "jsonrpc": "2.0",
            "id": 1,
            "method": "sendBundle",
            "params": [[encoded_tx]],
        }))
        .send()
        .await;
}

//Golang
package main

import (
	"bytes"
	"context"
	"encoding/base64"
	"encoding/json"
	"fmt"
	"github.com/davecgh/go-spew/spew"
	"github.com/gagliardetto/solana-go/programs/system"
	"log"
	"net/http"
	"time"

	"github.com/gagliardetto/solana-go"
	"github.com/gagliardetto/solana-go/rpc"
)

func main() {
	payerPrivateKeyBase58 := "YOUR_WALLET_PRIVATE_KEY"
	astralaneTipAddressBase58 := "astra4uejePWneqNaJKuFFA8oonqCE1sqF6b45kDMZm"
	rpcEndpoint := "DM_US_FOR_TRIAL_RPC_ENDPOINTS"
	astralaneEndpoint := "http://fr.gateway.astralane.io/iris?api-key=YOUR_ASTRALANE_API_KEY"

	signer, err := solana.PrivateKeyFromBase58(payerPrivateKeyBase58)
	if err != nil {
		log.Fatalf("Failed to load private key: %v", err)
	}

	client := rpc.New(rpcEndpoint)
	recentBlockhash, err := client.GetLatestBlockhash(context.Background(), rpc.CommitmentFinalized)
	if err != nil {
		log.Fatalf("Failed to get recent blockhash: %v", err)
	}

	astralaneTipAddress, err := solana.PublicKeyFromBase58(astralaneTipAddressBase58)
	if err != nil {
		log.Fatalf("Failed to decode astralane tip address: %v", err)
	}
	tx, err := solana.NewTransaction(
		[]solana.Instruction{
			system.NewTransferInstruction(
				200_000,
				signer.PublicKey(),
				astralaneTipAddress,
			).Build(),
		},
		recentBlockhash.Value.Blockhash,
		solana.TransactionPayer(signer.PublicKey()),
	)
	if err != nil {
		log.Fatalf("Failed to create transaction: %v", err)
	}

	if _, err := tx.Sign(func(key solana.PublicKey) *solana.PrivateKey {
		return &signer
	}); err != nil {
		log.Fatalf("Failed to sign transaction: %v", err)
	}

	spew.Dump(tx)

	out, err := tx.MarshalBinary()
	if err != nil {
		log.Fatalf("Marshaling to binary failed: %v", err)
	}
	encodedTx := base64.StdEncoding.EncodeToString(out)

	decoded, err := base64.StdEncoding.DecodeString(encodedTx)
	if err != nil {
		log.Fatalf("Sanity check decode failed: %v", err)
	}
	if !bytes.Equal(decoded, out) {
		log.Fatalf("Sanity check failed: decoded transaction does not match original binary")
	}

	payload := map[string]interface{}{
		"jsonrpc": "2.0",
		"id":      1,
		"method":  "sendBundle",
		"params": []interface{}{
			[]string{encodedTx},
		},
	}

	response, err := sendBundleRequest(astralaneEndpoint, payload)
	if err != nil {
		log.Fatalf("Bundle RPC failed: %v", err)
	}

	log.Printf("Bundle RPC response: %s", response)
}

func sendBundleRequest(endpoint string, payload map[string]interface{}) (string, error) {
	jsonData, err := json.Marshal(payload)
	if err != nil {
		return "", fmt.Errorf("Failed to marshal JSON: %w", err)
	}

	client := &http.Client{Timeout: 15 * time.Second}
	resp, err := client.Post(endpoint, "application/json", bytes.NewBuffer(jsonData))
	if err != nil {
		return "", fmt.Errorf("Request to Bundle RPC failed: %w", err)
	}
	defer resp.Body.Close()

	var result map[string]interface{}
	if err := json.NewDecoder(resp.Body).Decode(&result); err != nil {
		return "", fmt.Errorf("Failed to decode Bundle RPC response: %w", err)
	}

	responseBytes, _ := json.MarshalIndent(result, "", "  ")
	return string(responseBytes), nil
}

//Request
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "sendBundle",
  "params": [
    "base64_encoded_txn1",
    "base64_encoded_txn2"
    "base64_encoded_txn3"
    {
      "encoding": "base64",
      "mevProtect": true
    }
  ]
}

//Response:
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        "37Dxw2nJYw3T8JVqenPQMf39VJ9CNZYCyQm67b6nRj6fa6UjQ1UuLqFvh3wJ2G7LcMuZn4oq5kDt2A2CEXfi8D8"
    ]
}

// 注意: MAX_TRANSACTIONS_IN_BUNDLE = 4
// バンドル内の最大トランザクション数は4です。

//sendIdeal
スナイパーに最適です! JITO バリデータと通常のバリデータに分離されているため、トレーダーは JITO チップに多く費やすか、優先手数料に多く費やすかで葛藤することがよくあります。永続的な nonce は、この問題を軽減する方法を提供します。

当社の sendIdeal RPC メソッドは、2 つのトランザクションを受け入れます:

1 つは優先度の高い手数料 + 最小チップ

もう 1 つは高いチップ + 優先度の低い手数料

これらのトランザクションは、高度な SWQoS およびバンドル パイプラインを介してルーティングされます。永続的な nonce を使用すると、1 つのトランザクションが到着すると、もう 1 つは自動的にキャンセルされるため、最適な効率とコスト削減が保証されます。

永続的な nonce アカウントを自分で管理したくない場合は、そのための管理サービスも提供しています。使用する API キーごとに nonce アカウントを作成し、getNonce rpc 呼び出しを使用してクエリできます。この機能を最大限に活用するには、以下の統合手順に従ってください。

Step 1 : Generate Nonce instruction

//Rust 
use solana_sdk::hash::Hash;
use solana_sdk::pubkey::Pubkey;
use solana_sdk::signature::{EncodableKey, Keypair, Signer};

async fn get_nonce(
    client: reqwest::Client,
    url: String,
    auth_key: String,
) {
    let response = client
        .post(url)
        .header("api_key", "xxx")
        .json(&json! ({
            "jsonrpc": "2.0",
            "id": 1,
            "method": "getNonce",
            "params": [api_key], // provided during onboarding
        }))
        .send()
        .await;
    let result = response["result"].clone();
    let nonce = result["nonce"].as_str().unwrap();
    let nonce_account = Pubkey::from_str(result["nonceAccount"].as_str().unwrap()).unwrap();
    let nonce_authority =
        Pubkey::from_str(result["nonceAuthority"].as_str().unwrap()).unwrap();
    let nonce_as_hash = Hash::from_str(nonce).unwrap();
}

次に！以下に「sendIdeal」エンドポイントのTypeScriptコード例を日本語翻訳しました。
import {
  Connection,
  Keypair,
  PublicKey,
  SystemProgram,
  Transaction,
  LAMPORTS_PER_SOL,
  ComputeBudgetProgram,
} from '@solana/web3.js';
import * as bs58 from 'bs58';


 async sendTransTest() {
    try {
      const result = await fetch(
        `http://fr.gateway.astralane.io/iris?api-key=<api-key>`,
        {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({
            jsonrpc: '2.0',
            id: 1,
            method: 'getNonce',
            params: [
              'api-key',
            ],
          }),
        },
      );
      const res = await result.json();
      const data = res as any;
      const nonceAccount = new PublicKey(data.result.nonceAccount);
      const nonceAuthority = new PublicKey(data.result.nonceAuthority);
      const nonce = data.result.nonce;
      const advanceNonce = SystemProgram.nonceAdvance({
        noncePubkey: nonceAccount,
        authorizedPubkey: nonceAuthority,
      });

      const secretKey = Keypair.fromSecretKey(
        bs58.default.decode(
          'wallet-private-key',
        ),
      );

      const wallet = secretKey;
      //Tip account
      const toPublicKey = new PublicKey(
        'astra4uejePWneqNaJKuFFA8oonqCE1sqF6b45kDMZm',
      );
      const MIN_TIP_AMOUNT = 100000;

      const lowTipHighFeeIx = [
        advanceNonce,
        ComputeBudgetProgram.setComputeUnitPrice({
          microLamports: 1 * 100000,
        }),
        SystemProgram.transfer({
          fromPubkey: wallet.publicKey,
          toPubkey: toPublicKey,
          lamports: MIN_TIP_AMOUNT,
        }),
      ];

      const HighTipLowFeeIx = [
        advanceNonce,
        ComputeBudgetProgram.setComputeUnitPrice({
          microLamports: 1 * 100,
        }),
        SystemProgram.transfer({
          fromPubkey: wallet.publicKey,
          toPubkey: toPublicKey,
          lamports: 100 * MIN_TIP_AMOUNT,
        }),
      ];

      const htlfTransaction = new Transaction().add(...HighTipLowFeeIx);
      htlfTransaction.recentBlockhash = nonce;
      htlfTransaction.feePayer = wallet.publicKey;
      htlfTransaction.partialSign(wallet);
      const highTipLowFeeEncoded = Buffer.from(
        htlfTransaction.serialize({
          requireAllSignatures: false,
          verifySignatures: false,
        }),
      ).toString('base64');

      const lthfTransaction = new Transaction().add(...lowTipHighFeeIx);
      lthfTransaction.recentBlockhash = nonce;
      lthfTransaction.feePayer = wallet.publicKey;
      lthfTransaction.partialSign(wallet);
      const lowTipHighFeeEncoded = Buffer.from(
        lthfTransaction.serialize({
          requireAllSignatures: false,
          verifySignatures: false,
        }),
      ).toString('base64');

      const final = await fetch(
        `http://fr.gateway.astralane.io/iris?api-key=<api-key>`,
        {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({
            jsonrpc: '2.0',
            id: 1,
            method: 'sendIdeal',
            params: [[highTipLowFeeEncoded, lowTipHighFeeEncoded]],
          }),
        },
      );
      const finalResult = await final.json();
      console.log(finalResult);

      return lowTipHighFeeEncoded;
    } catch (error) {
      console.log(error);
    }
  }

  //Request
  {
  "id": 1,
  "jsonrpc": "2.0",
  "method": "sendIdeal",
  "params": [
    "transction_with_large_tip_low_priority_fee",
    "transaction_with_large_priority_fee_low_tip"
    {
      "encoding": "base64"
    }
  ]
}


//Response
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        "<signature A>",
        "<signature B>"
    ]
}

//sendPaladin (ベータ版)
Paladin は、トランザクションをリーダーに直接送信するためのより効率的な方法を提供するカスタム TPU ポート実装です。Paladin クライアントは現在、Solana ネットワークの 10% で実行されています (2025 年 3 月 10 日現在)

ステップ 1: パラディン リーダーの追跡
パラディン バリデーターはすべてのスロットに存在するわけではないため、パラディン リーダーがいるスロットを確認するために使用できるパラディン リーダー トラッカー エンドポイントを用意しました。リーダー情報に基づいてトランザクションを動的に送信するためのトラッカー統合に関する一般的なガイドラインを以下に示します。

現在のエポックのすべての Palidator 公開鍵を取得します
⚔️ GET /api/palidators
[
    "Ss...Z77",
    "ACv...mi",
    "7Z...Z84",
]

次のパリデーターリーダースロットを獲得
⚔️ GET /api/next_palidator
{
  "pubkey": "Csd...def",
  "leader_slot": 42424242,
  "context_slot": 42424242
}

指定されたスロット以降に次のリーダー Palidator を獲得する
⚔️ GET /api/next_palidator/{slot}
{
  "pubkey": "Csd...def",
  "leader_slot": 42424242,
  "context_slot": 42424242
}

注意: 悪意のあるオペレーターの中には、Paladin を使用しているふりをする人もいます。そのため、このようなシナリオで悪用されないように、悪意のあるオペレーターに対するプロアクティブなブロックリストを実装することをお勧めします。これを実現する方法がわからない場合は、Discord でお問い合わせください。

Paladin バリデーターに送信されるトランザクションには、10 ランポート/CU という非常に小さな最小制限があります。

ステップ 2: パラディン トランザクションの作成
これらのスロット中にトランザクションを送信するには、send_palladin メソッドを使用します。このエンドポイントでトランザクションを送信する際は、最小チップ要件と最小優先手数料要件を必ず遵守してください。

トランザクションの送信中は、2 つのモードがサポートされています。Fail on revert: True / False

このエンドポイントに送信されたすべてのトランザクションは、小規模なオークションを通過し、順序は次の基準に基づいて決定されます。

優先手数料

タイブレーカーは、Astralane チップ アドレスへのチップによって決定されます

勝者のトランザクションは、パラディン ポート経由で送信されます。failOnRevert: false の場合、トランザクションがオークションに勝てなかった場合、SwQOS / JITO 経由で送信されます。

failOnRevert true の場合、これらのトランザクションは Palidators にのみ送信され、スロットがパラディン リーダーの近くにない場合は破棄されます。

{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "sendPaladin",
  "params": [
    "<base 64 tx>",
    {
      "failOnRevert": "false"
    }
  ]
}

{
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        "<signature A>",
    ]
}

これを効率的に使用するには、Paladin Leader API エンドポイントを使用してアクティブなリーダーとスロットのタイミングを追跡し、それに応じてチップ/料金を調整してください。最適なコスト管理のために、専用のエンドポイントを介して構成を更新してください。

---
## 🆘 お困りですか？

技術ガイド、統合サポート、最新情報の受け取りには、  
👉 [**Discordにご参加ください。**](https://discord.com/invite/2UfWGtUDtN)

---

> **Astralane — Solanaインフラの最前線で、あなたのビルドを加速する。**
