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

### 呼び出し例

```javascript
// 単発 / QRコード（デフォルト）
window.open('https://example.github.io/qr-scanner/index.html?is_continuous=false');

// 連続 / バーコード
window.open('https://example.github.io/qr-scanner/index.html?is_continuous=true&scan_type=barcode');
レスポンス
読み取り完了時・キャンセル時に window.opener.postMessage() で結果を返します。

受信側
Copywindow.addEventListener('message', function(e) {
  if (!e.data || e.data.type !== 'qr-scan-result') return;
  console.log(e.data);
});
レスポンス形式
Copy{
  "type": "qr-scan-result",
  "status": "ok",
  "is_continuous": true,
  "scan_type": "qr",
  "items": [
    {
      "data": "読み取ったデータ文字列",
      "format": "QR_CODE",
      "scanned_at": "2026-03-04T14:30:00.000Z"
    }
  ]
}
フィールド	型	説明
type	string	固定値 qr-scan-result
status	string	ok: 正常完了、cancelled: キャンセル
is_continuous	boolean	呼び出し時のモード
scan_type	string	qr または barcode
items	array	読み取り結果の配列。キャンセル時は空配列
items[].data	string	読み取ったデータ
items[].format	string	フォーマット名（QR_CODE, EAN_13, CODE_128 等）
items[].scanned_at	string	読み取り時刻（ISO 8601）
バリデーション
同一セッション内で同じ data の重複読み取りは無視されます。 重複時はエラー音 + Toastで通知し、itemsには追加されません。

対応フォーマット
scan_type	対応フォーマット
qr	QR_CODE
barcode	EAN_13, EAN_8, UPC_A, UPC_E, CODE_128, CODE_39, CODE_93, CODABAR, ITF, RSS_14, RSS_EXPANDED
動作要件
HTTPS環境（GitHub Pages等）
カメラ搭載デバイス（スマートフォン / タブレット）
window.open() で開くこと（自動タブクローズに必要）
