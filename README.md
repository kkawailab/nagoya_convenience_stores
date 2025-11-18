# 名古屋市コンビニエンスストア立地分析

名古屋市内のコンビニエンスストアの分布を可視化・分析するインタラクティブな地図アプリケーションです。GeoJSON形式のデータを使用して、店舗の立地パターン、ブランド分布、密集度などを多角的に分析できます。

![Map Preview](https://img.shields.io/badge/Leaflet-Interactive%20Map-green)
![License](https://img.shields.io/badge/license-MIT-blue)

## 🌟 主な機能

### 📍 インタラクティブマップ
- **Leaflet.js**を使用した高性能な地図表示
- OpenStreetMapタイルレイヤーによる詳細な背景地図
- ブランドごとに色分けされたマーカー表示
- クリックでポップアップ表示（店舗名、営業時間、駐車場情報など）

### 🔍 フィルタリング機能
- **ブランドフィルター**: セブン-イレブン、ファミリーマート、ローソン、ミニストップ
- **エリアフィルター**: 名古屋市16区（千種区、東区、北区、西区、中村区、中区、昭和区、瑞穂区、熱田区、中川区、港区、南区、守山区、緑区、名東区、天白区）

### 📊 視覚化機能
- **ヒートマップ表示**: `leaflet.heat`プラグインを使用した店舗密度の可視化
  - 青→緑→黄→赤のグラデーション表示
  - 密集度が一目でわかる
- **クラスター分析**: `leaflet.markercluster`プラグインを使用したグルーピング
  - 近接店舗の自動クラスタリング
  - ズームレベルに応じた動的な表示切替
  - クリックで詳細表示

### 📈 統計ダッシュボード
- 総店舗数
- 平均店舗間距離
- 最密集エリア
- 現在表示中の店舗数

## 🚀 デモ

GitHub Pagesでホスティング中：
**https://kkawailab.github.io/nagoya_convenience_stores/**

## 📦 技術スタック

| 技術 | 用途 | バージョン |
|------|------|-----------|
| [Leaflet.js](https://leafletjs.com/) | 地図表示ライブラリ | 1.9.4 |
| [Leaflet.heat](https://github.com/Leaflet/Leaflet.heat) | ヒートマップ表示 | 0.2.0 |
| [Leaflet.markercluster](https://github.com/Leaflet/Leaflet.markercluster) | マーカークラスタリング | 1.5.3 |
| [OpenStreetMap](https://www.openstreetmap.org/) | 地図タイル | - |
| GeoJSON | データ形式 | - |

## 📂 プロジェクト構成

```
nagoya_convenience_stores/
├── index.html          # メインアプリケーションファイル（スタンドアロン）
└── README.md          # このファイル
```

## 🔧 インストール方法

### 方法1: ローカルで実行（推奨）

1. **リポジトリをクローン**
   ```bash
   git clone https://github.com/kkawailab/nagoya_convenience_stores.git
   cd nagoya_convenience_stores
   ```

2. **ブラウザで開く**
   ```bash
   # macOS
   open index.html

   # Linux
   xdg-open index.html

   # Windows
   start index.html
   ```

   または、ファイルマネージャーから`index.html`をダブルクリック

### 方法2: ローカルサーバーで実行

CORS制限を回避するため、ローカルサーバーを使用することを推奨：

#### Python 3を使用
```bash
python -m http.server 8000
```
ブラウザで `http://localhost:8000` にアクセス

#### Node.jsを使用
```bash
npx http-server -p 8000
```
ブラウザで `http://localhost:8000` にアクセス

#### VS Code Live Server拡張機能
1. [Live Server拡張機能](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)をインストール
2. `index.html`を右クリック → "Open with Live Server"

### 方法3: GitHub Pagesで公開

1. GitHubリポジトリのSettings → Pages
2. Sourceで`main`ブランチを選択
3. Saveをクリック
4. 数分後に`https://<username>.github.io/nagoya_convenience_stores/`でアクセス可能

## 📊 データについて

### 現在のデータ
このアプリケーションは**デモ用の合成データ**を使用しています。
- JavaScriptの`generateStoreData()`関数で動的生成
- 名古屋市16区にランダムに配置された架空の店舗データ

### 実データの入手方法

実際のコンビニデータを使用する場合、以下の方法でデータを取得できます：

#### 1. Overpass API（OpenStreetMapデータ）

**無料・オープンソース**のOSMデータを使用：

```bash
# Overpass Turbo (https://overpass-turbo.eu/) で以下のクエリを実行
[out:json][timeout:25];
area["name"="名古屋市"]->.searchArea;
(
  node["shop"="convenience"](area.searchArea);
  way["shop"="convenience"](area.searchArea);
  rel["shop"="convenience"](area.searchArea);
);
out body;
>;
out skel qt;
```

#### 2. 国土地理院 地理院地図

- [地理院地図](https://maps.gsi.go.jp/)から施設情報をダウンロード
- KML/GeoJSON形式で出力可能

#### 3. オープンデータポータル

- [名古屋市オープンデータ](https://www.city.nagoya.jp/shicho/page/0000132694.html)
- [政府統計の総合窓口（e-Stat）](https://www.e-stat.go.jp/)

#### 4. Google Places API（有料）

```javascript
// Google Places APIを使用する例
const service = new google.maps.places.PlacesService(map);
service.nearbySearch({
  location: { lat: 35.1815, lng: 136.9066 },
  radius: 10000,
  type: 'convenience_store'
}, callback);
```

### データ形式

このアプリケーションはGeoJSON形式を使用します：

```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [136.9066, 35.1815]
      },
      "properties": {
        "id": 1,
        "name": "セブン-イレブン 名古屋中区1号店",
        "brand": "セブン-イレブン",
        "ward": "中区",
        "営業時間": "24時間",
        "駐車場": "あり"
      }
    }
  ]
}
```

### データの置き換え方法

実データを使用する場合は、`index.html`の以下の部分を修正：

```javascript
// 現在（合成データ）
storeData = generateStoreData();

// 実データを使用する場合
fetch('data/stores.geojson')
  .then(response => response.json())
  .then(data => {
    storeData = data;
    currentLayer = createMarkerLayer(storeData);
    currentLayer.addTo(map);
    updateStats(storeData);
  });
```

## 🎯 使い方

1. **基本操作**
   - マウスドラッグで地図移動
   - スクロールホイールでズーム
   - マーカークリックで詳細情報表示

2. **フィルタリング**
   - ブランドドロップダウンで特定ブランドのみ表示
   - 区ドロップダウンで特定エリアのみ表示

3. **可視化切替**
   - 「ヒートマップ表示」ボタン: 密度分布を表示
   - 「クラスター分析」ボタン: グループ化表示
   - 「リセット」ボタン: 初期表示に戻る

## 🛠️ カスタマイズ

### ブランドカラーの変更

```javascript
const BRANDS = {
    'セブン-イレブン': '#00AB4E',  // 緑
    'ファミリーマート': '#00A0DC', // 青
    'ローソン': '#0066B3',         // 濃青
    'ミニストップ': '#F39800'      // オレンジ
};
```

### ヒートマップのグラデーション調整

```javascript
gradient: {
    0.0: 'blue',   // 低密度
    0.5: 'lime',   // 中密度
    0.7: 'yellow', // 高密度
    1.0: 'red'     // 最高密度
}
```

### クラスターの半径調整

```javascript
currentLayer = L.markerClusterGroup({
    maxClusterRadius: 50,  // クラスター半径（px）
    spiderfyOnMaxZoom: true,
    showCoverageOnHover: true,
    zoomToBoundsOnClick: true
});
```

## 📱 ブラウザ対応

- ✅ Chrome 90+
- ✅ Firefox 88+
- ✅ Safari 14+
- ✅ Edge 90+
- ✅ モバイルブラウザ（iOS Safari, Chrome Mobile）

## 🤝 貢献

プルリクエストを歓迎します！大きな変更の場合は、まずissueで議論してください。

### 開発の流れ

1. このリポジトリをフォーク
2. フィーチャーブランチを作成 (`git checkout -b feature/AmazingFeature`)
3. 変更をコミット (`git commit -m 'Add some AmazingFeature'`)
4. ブランチにプッシュ (`git push origin feature/AmazingFeature`)
5. プルリクエストを作成

## 📄 ライセンス

このプロジェクトはMITライセンスの下で公開されています。

## 👤 作者

- GitHub: [@kkawailab](https://github.com/kkawailab)

## 🙏 謝辞

- [Leaflet.js](https://leafletjs.com/) - オープンソース地図ライブラリ
- [OpenStreetMap](https://www.openstreetmap.org/) - オープンソース地図データ
- [Leaflet.heat](https://github.com/Leaflet/Leaflet.heat) - ヒートマッププラグイン
- [Leaflet.markercluster](https://github.com/Leaflet/Leaflet.markercluster) - クラスタープラグイン

## 📮 お問い合わせ

質問や提案がある場合は、[Issues](https://github.com/kkawailab/nagoya_convenience_stores/issues)で報告してください。

---

## 📝 更新履歴

### [1.0.0] - 2025-11-19

#### 追加
- 初回リリース
- Leaflet.jsベースのインタラクティブマップ機能
- GeoJSON形式のデータ構造実装
- 名古屋市16区のエリア分け
- 4大コンビニブランド（セブン-イレブン、ファミリーマート、ローソン、ミニストップ）のサポート
- ブランドおよびエリア別フィルタリング機能
- leaflet.heatプラグインによるヒートマップ表示
- leaflet.markerclusterプラグインによるクラスター分析
- 統計ダッシュボード（総店舗数、平均距離、最密集エリア、表示中店舗数）
- レスポンシブデザイン対応
- GitHub Pagesでのホスティング対応

#### 技術詳細
- Leaflet 1.9.4
- Leaflet.heat 0.2.0
- Leaflet.markercluster 1.5.3
- デモ用合成データ生成機能

---

**Made with ❤️ for 名古屋**
