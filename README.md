# rs-simple--scanner
QRおよびバーコードの単発・連続読み取りが可能。
主にGAS等での簡易フロントエンド実装用。

## 呼び出し方

https:///qr-scanner/index.html?is_continuous={boolean}&scan_type={string}&origin={string}


### パラメータ

| パラメータ | 必須 | 型 | 値 | 説明 |
|---|---|---|---|---|
| `is_continuous` | Yes | string | `true` / `false` | `true`: 連続読み取り（完了ボタンで終了）、`false`: 単発読み取り（1件読み取り後自動終了） |
| `scan_type` | No | string | `qr` / `barcode` / `all` | 読み取り対象。デフォルト: `all` |
| `origin` | No | string | URL origin | `postMessage`の送信先origin。デフォルト: `*` |

### 呼び出し例

```javascript
// 単発 / QRのみ
window.open('https://example.github.io/qr-scanner/index.html?is_continuous=false&scan_type=qr&origin=' + encodeURIComponent(location.origin), '_blank');

// 連続 / バーコードのみ
window.open('https://example.github.io/qr-scanner/index.html?is_continuous=true&scan_type=barcode&origin=' + encodeURIComponent(location.origin), '_blank');

// 連続 / すべて
window.open('https://example.github.io/qr-scanner/index.html?is_continuous=true&scan_type=all&origin=' + encodeURIComponent(location.origin), '_blank');
レスポンス
読み取り完了時またはキャンセル時に window.opener.postMessage() で結果を返します。

受信側
Copywindow.addEventListener('message', function(e) {
  if (!e.data || e.data.type !== 'qr-scan-result') return;
  console.log(e.data);
});
レスポンス形式
Copy{
  "type": "qr-scan-result",
  "status": "ok | cancelled",
  "is_continuous": true,
  "scan_type": "qr | barcode | all",
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
scan_type	string	呼び出し時の読み取り対象
items	array	読み取り結果の配列。キャンセル時は空配列
items[].data	string	読み取ったデータ
items[].format	string	フォーマット名（QR_CODE, EAN_13, CODE_128 等）
items[].scanned_at	string	読み取り時刻（ISO 8601）
重複バリデーション
同一セッション内で同じ data を複数回読み取った場合、itemsには追加されません。 重複時はエラー音とToast通知でユーザーに通知します。

対応フォーマット
scan_type	対応フォーマット
qr	QR_CODE
barcode	EAN_13, EAN_8, UPC_A, UPC_E, CODE_128, CODE_39, CODE_93, CODABAR, ITF, RSS_14, RSS_EXPANDED
all	上記すべて
動作要件
HTTPS環境（GitHub Pages等）
カメラ搭載デバイス（スマートフォン / タブレット推奨）
window.open() で開かれること（window.close() による自動タブクローズのため）
