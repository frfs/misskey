---
name: updating-frsky-v13
description: "Use whenever the user asks to update, upgrade, or rebase the frsky-v13 branch onto the latest Misskey release tag. Exclude alpha and beta tags by default; resolve the root package version as the release version suffixed with -frsky."
---

# Updating frsky-v13

このスキルは、Misskey の `frsky-v13` ブランチを最新リリースへ更新する手順を定める。更新指示があったときに実作業を行う。ユーザーが特定のタグやプレリリースを指定した場合は、その指示を優先する。

## 手順

1. リポジトリの状態と remotes を確認する。未コミット変更がある場合は、変更を保持したまま作業を止め、退避やコミットなど次の対応を相談する。変更を stash したり破棄したりしない。
2. 更新元の公式リモートを特定する。`origin` と `upstream` のどちらが公式か明確でない場合はユーザーに確認する。公式リモートからタグを fetch する。ブランチを自動で pull したり、リモートブランチへ push したりしない。
3. タグをバージョン順に調べ、最新の安定版タグを選ぶ。α版・β版は、ユーザーから特別な指示がない限り除外する。タグ名の文字列順ではなくバージョン順で比較する。命名形式が不揃いで最新安定版を確定できない場合は、候補を示して確認する。
4. `frsky-v13` のローカルブランチが存在することを確認し、それを checkout して選んだタグのコミットへ rebase する。ブランチが存在しない、または現在の状態から安全に開始できない場合は、作業を進めず相談する。
5. rebase 中にルート `package.json` の `version` が競合した場合は、選んだタグのコミットにある `package.json` の `version` の末尾へ `-frsky` を付けた値で解決する。タグ名の `v` などを版数として推測せず、必ずファイル内の値を使う。競合 hunk に version 以外の変更も含まれる場合、その部分は自動で決めず、次の競合対応に従う。
6. その他の競合が発生したら、競合ごとに base・更新元タグ・`frsky-v13` 側の変更を調べ、影響と対応案をユーザーへ提示する。確認を得てから解決を続ける。提案や確認を待つ間、競合状態のまま停止してよい。競合内容を推測で上書きしない。
7. rebase 完了後、ブランチ名、rebase 先のタグとコミット、ルート `package.json` の版数、変更内容、競合解決内容を確認する。JSON の妥当性と Git の状態を検査し、変更範囲に応じた近接テスト・lint を実行する。テスト不能や失敗は隠さず報告する。

## 安全上の制約

- ユーザーの明示指示なしに force push、push、commit、reset、競合ファイルの一括置換をしない。
- rebase の中断・巻き戻しが必要な場合は、その状態と影響を説明してユーザーに確認する。
- `pnpm-lock.yaml` など他のファイルに版数変更が必要かを確認し、必要性が明確でない変更は加えず提案する。
- リベース先、版数、競合の判断根拠を最終報告に含める。
