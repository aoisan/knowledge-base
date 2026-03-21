# 開発基盤選定ガイドライン（OS → .NET → C# → UI → DB）

本ドキュメントは、Windows デスクトップアプリケーション開発における  
**OS / .NET / C# / UI / DB / DB Driver / ORM** の選定基準を、  
最小限の比較表で判断しやすい形に整理したガイドライン。

---

# 0. 結論：2026 年時点の推奨構成
※ UI / DB / Driver / ORM は要件に応じて選択
## 新規開発
- **OS**：Windows 10/11  
- **IDE**：Visual Studio 2022  
- **ランタイム**：.NET 8（LTS）  
- **言語**：C# 12
- **UI**：WPF または WinUI 3  

## 既存資産の保守
将来的に .NET 6 / .NET 8 への移行を推奨
- **OS**：Windows 10/11  
- **IDE**：Visual Studio 2019 / 2022  
- **ランタイム**：.NET Framework 4.8  
- **言語**：C# 7.3  

---

# 1. 選定の優先順位

| 優先度 | 項目 | 理由 |
|-------|------|------|
| **1** | **OS（ターゲット環境）** | 対応ランタイム・IDE が決まる |
| **2** | **.NET Framework / .NET** | サポート期限・性能・互換性 |
| **3** | **C# 言語バージョン** | ランタイムに従属 |
| **4** | **UI フレームワーク** | OS/ランタイムの制約を受ける |
| **5** | **DB** | アプリ構造に影響 |
| **6** | **DB Driver** | DB に従属 |
| **7** | **ORM** | 開発スタイルに影響 |

---

# 2. OS の選定
OS が対応していない技術は選択できないため、ここで選択肢が大きく絞られる。
## Windows 7 / 8.1 は完全レガシー  
- .NET Framework は 4.8 まで動作
- .NET Core は 3.1 が最終
- .NET 5 以降は 完全非対応
- C# 7.3 が上限  
- 新規開発は絶対に避けるべき

## Windows 10 は最も広い互換性を持つ
- .NET Framework 〜 .NET 8 まで網羅  
- Visual Studio 2015〜2022 まで動作   
- 企業利用で依然多い

## Windows 11 は新規開発の最適解  
- Visual Studio 2022 + .NET 6/7/8 + C# 10〜12  
- 将来性・保守性が最も高い

---

# 3. .NET ランタイムの選定
ランタイムはアプリの寿命・性能・サポート期限に直結する。
## 新規開発
- **.NET 8（LTS）を基本とし、必要に応じて .NET 6 を選択**

## 既存資産がある場合
- .NET Framework 4.8（最終版で新機能なし） を継続  
- 将来的に .NET 6/8 へ移行を検討

---

# 4. C# 言語バージョン
C# はランタイムと Visual Studio に依存するため、  **ランタイムを決めた時点でほぼ自動的に決まる**。
| ランタイム | C# バージョン |
|------------|----------------|
| .NET Framework | C# 7.3 |
| .NET 6 | C# 10 |
| .NET 7 | C# 11 |
| .NET 8 | C# 12 |
---

# 5. UI フレームワーク選定
上位の OS・ランタイム・C# の制約を受ける。
| UI | 特徴 | 推奨度 |
|----|------|--------|
| **WPF** | MVVM と相性良い、安定、企業向け | ★★★★★ |
| **WinUI 3** | Windows 10/11 専用のモダン UI | ★★★★☆ |
| **WinForms** | レガシー、将来性なし | ★★☆☆☆ |
| **MAUI** | Windows 10/11 前提のクロスプラットフォーム（Windows / Android / iOS） | ★★★☆☆ |

---

# 6. DB 選定
※ 組織標準がある場合はそれを優先する
## 6.1 DB選択の基準（.NET 開発者向け）
- 企業システム・基幹業務 → SQL Server
- OSS / Web 系 → PostgreSQL / MySQL
- デスクトップアプリ → SQLite
- 大規模・ミッションクリティカル → Oracle
- クロスプラットフォーム（Linux 含む） → PostgreSQL / SQL Server on Linux 


## 6.2 主要DB比較
| DB | 種類 | ライセンス | 特徴 | .NET との相性 | 主な用途 |
|----|------|------------|--------|----------------|-----------|
| SQL Server | 商用 | 商用 / Express 無料 | Microsoft 製、機能豊富 | ★★★★★ | 企業システム、金融、基幹 |
| PostgreSQL | OSS | 無料 | 高機能・高性能 | ★★★★☆ | Web、クラウド、分析 |
| MySQL | OSS | 無料 | 世界で最も普及 | ★★★☆☆ | Web、SaaS |
| MariaDB | OSS | 無料 | MySQL 互換、高性能 | ★★★☆☆ | Web、OSS 系 |
| Oracle | 商用 | 高額 | エンタープライズ向け | ★★★☆☆ | 大規模基幹、金融 |
| SQLite | OSS | 無料 | 組み込み型、軽量 | ★★★★☆ | デスクトップ、モバイル |


---

# 7. DB Driver 選定
DB Driver は使用する DB に依存して選択する。
| DB | Driver |
|----|--------|
| SQL Server | Microsoft.Data.SqlClient |
| PostgreSQL | Npgsql |
| MySQL | MySqlConnector |
| Oracle | Oracle.ManagedDataAccess |
| SQLite | Microsoft.Data.Sqlite |

---

# 8. ORM （Object-Relational Mapping）選定
## ORM選択の基準
- シンプルなCRUD中心 → Dapper
- ドメインロジックが複雑 → EF Core
- パフォーマンス重視 → Dapper


## 注意点
- ORMはSQLの理解を前提とする
- パフォーマンス問題はSQLで解決するケースが多い


| ORM | 特徴 | 適した用途 |
|------|--------|--------------|
| **ADO.NET** | 最速・低レベル | レガシー / 高速処理 |
| **Dapper** | SQL 主体・高速 | 既存 SQL / パフォーマンス重視 |
| **EF Core** | LINQ / モデル中心 | 新規開発 / 生産性重視 |

---

# 9. OS × Visual Studio × .NET × C#（総合マトリクス）

| OS | Visual Studio | .NET | C# | 備考 |
|----|----------------|------|-----|------|
| Windows 7 | VS 2015〜2019 | .NET Framework / .NET Core 3.1 | 〜7.3 | 新規不可 |
| Windows 10 | VS 2015〜2022 | .NET Framework / .NET 6/7/8 | 〜12 | 現場で最多 |
| Windows 11 | VS 2022 | .NET 6/7/8 | 10〜12 | 新規開発の最適解 |

---

# 10. まとめ

1. **OS を決める**  
2. **ランタイム（.NET）を決める**  
3. **C# が自動的に決まる**  
4. **UI フレームワークを選ぶ**  
5. **DB を選ぶ**  
6. **DB Driver を選ぶ**  
7. **ORM を選ぶ**

この順序で選定することで、技術的制約と将来の保守性を両立できる。