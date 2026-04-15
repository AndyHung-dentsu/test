# Github PR 範例

## 使用方式
1. 放置於專案目錄 `.github/PULL_REQUEST_TEMPLATE` 目錄下
2. 於 github 開 PR 時，在 PR 網址後方加上 `?template=<檔名>` (e.g. `?template=feature.md`)
3. 範例即會自動套用

## 檔案說明
|  PR 場景   | 檔案  |
|  ----  | ----  |
| 功能分支 => master  | [feature](templates/feature.md) |
| master => production  | [release](templates/release.md) |
| release => production  | [release](templates/release.md) |
| production => master  | [back-merge](templates/back-merge.md) |
| hotfix => production  | [hotfix](templates/hotfix.md) |
| hotfix => master  | [back-merge](templates/back-merge.md) |
| prd => master  | [prd](templates/prd.md) |
