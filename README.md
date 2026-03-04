# rs-simple-scanner
QRおよびバーコードの単発・連続読み取りが可能。
主にGAS等での簡易フロントエンド実装用。

## 呼び出し方
https://rsfact.github.io/rs-simple-scanner/index.html?is_continuous={boolean}&scan_type={string}&origin={string}

### パラメータ

| パラメータ | 必須 | 型 | 値 | デフォルト | 説明 |
|---|---|---|---|---|---|
| `is_continuous` | Yes | string | `true` / `false` | - | `true`: 連続読み取り、`false`: 単発読み取り |
| `scan_type` | No | string | `qr` / `barcode` | `qr` | 読み取り対象 |
| `origin` | No | string | URL origin | `*` | `postMessage`の送信先origin |

### レスポンス例
単発読み取り
{
  "scan_type": "qr",
  "is_continuous": false,
  "is_success": true,
  "is_cancel": false,
  "items": [
    {
      "value": "https://example.com/item/12345",
      "scanned_at": "2026-03-04T14:30:00.000Z"
    }
  ]
}

連続
{
  "scan_type": "barcode",
  "is_continuous": true,
  "is_success": true,
  "is_cancel": false,
  "items": [
    {
      "value": "4901234567890",
      "scanned_at": "2026-03-04T14:30:00.000Z"
    },
    {
      "value": "4901234567906",
      "scanned_at": "2026-03-04T14:30:05.000Z"
    }
  ]
}

キャンセル時
{
  "scan_type": "qr",
  "is_continuous": false,
  "is_success": false,
  "is_cancel": true,
  "items": []
}

### フィールド
フィールド	型	説明
scan_type	string	qr または barcode
is_continuous	boolean	呼び出し時のモード
is_success	boolean	1件以上読み取って正常完了した場合 true
is_cancel	boolean	キャンセルされた場合 true
items	array	読み取り結果の配列。キャンセル時は空配列
items[].value	string	読み取ったデータ
items[].scanned_at	string	読み取り時刻（ISO 8601）

### バリデーション
同一セッション内で同じ data の重複読み取りは無視されます。 重複時はエラー音 + Toastで通知し、itemsには追加されません。

### 対応フォーマット
scan_type	対応フォーマット
qr	QR_CODE
barcode	EAN_13, EAN_8, UPC_A, UPC_E, CODE_128, CODE_39, CODE_93, CODABAR, ITF, RSS_14, RSS_EXPANDED

### 動作要件
HTTPS環境（GitHub Pages等）
カメラ搭載デバイス（スマートフォン / タブレット）
window.open() で開くこと（自動タブクローズに必要）
