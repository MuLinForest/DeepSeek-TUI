# 繁體中文 zh-Hant 維護指引

## 現狀

- 繁體中文來自分支 `feat/zh-hant-locale`（基於上游 PR #781 by vincentredrock）
- 版本：v0.8.15 + 7 commits（最新 main + zh-Hant 改動）
- 翻譯品質：台灣繁體中文，人工手寫
- 改動範圍：8 個檔案，約 +300/-15 行

## 拉最新版 + 保留繁體

當上游（Hmbown/DeepSeek-TUI）出新版本時：

```bash
# 1. 拉上游最新
git fetch upstream

# 2. 把繁體改動重新套到最新版上
git checkout feat/zh-hant-locale
git rebase upstream/main

# 3. 推上去
git push myfork feat/zh-hant-locale --force-with-lease
```

推完 GitHub Actions 會自動出 binary（`Build zh-Hant` workflow）。

如果 rebase 中途有衝突（上游改到同一個檔案）：
- `git status` 看哪些檔案衝突
- 解完後 `git add` + `git rebase --continue`
- 通常衝突很少，因為 zh-Hant 改動多為「新增」而非「修改」既有程式碼

## GitHub Actions 自動 build

你的 fork（MuLinForest/DeepSeek-TUI）有自訂 workflow：

- **觸發時機**：push 到 `feat/zh-hant-locale` 分支，或手動觸發
- **產出**：`deepseek-linux-x64` + `deepseek-tui-linux-x64`（Release binary）
- **手動觸發**：https://github.com/MuLinForest/DeepSeek-TUI/actions/workflows/build-zh-hant.yml → Run workflow

## 環境準備（給新 session / 新機器）

```bash
# 裝系統依賴
sudo apt install -y libdbus-1-dev pkg-config

# Clone（如果還沒有）
git clone https://github.com/MuLinForest/DeepSeek-TUI.git
cd DeepSeek-TUI

# 設 Git 身份
git config user.name "MuLinForest"
git config user.email "Millenary.soul@gmail.com"

# 加上游 remote
git remote add upstream https://github.com/Hmbown/DeepSeek-TUI.git

# 切到繁體分支
git checkout feat/zh-hant-locale
```

## 本地 build

```bash
# 快速語法檢查
cargo check

# 直接跑（debug 模式）
cargo run --bin deepseek

# 出 release binary（兩個都要編）
cargo build --release --bin deepseek --bin deepseek-tui

# 安裝到 ~/.local/bin
cp target/release/deepseek ~/.local/bin/
cp target/release/deepseek-tui ~/.local/bin/
```

## GitHub Actions 下載 binary

```bash
# 下載最新成功的 build artifact
gh run download -R MuLinForest/DeepSeek-TUI \
  -n deepseek-linux-x64 -n deepseek-tui-linux-x64
```

## 設定繁體中文

編輯 `~/.config/deepseek/settings.toml`：

```toml
locale = "zh-Hant"
```

或透過環境變數：`LANG=zh_TW.UTF-8`

## Co-Author 規則（源自 /mnt/aivault/AGENTS.md）

所有 commit 都要加 co-author：

```
Co-Authored-By: DeepSeek (V4 Pro) <deepseek-tui@mulin.date>
```

## 上游 PR 追蹤

- #781（vincentredrock）：繁體中文原始 PR — https://github.com/Hmbown/DeepSeek-TUI/pull/781
- #790（vincentredrock）：繁體後續 i18n 覆蓋追蹤 — https://github.com/Hmbown/DeepSeek-TUI/issues/790
- #812（gordonlu）：更多 i18n 字串（依賴 #781）— https://github.com/Hmbown/DeepSeek-TUI/pull/812

一旦上游合了 #781，繁體就變內建，不需要這個分支了。直接 `git checkout main && git pull` 即可。
