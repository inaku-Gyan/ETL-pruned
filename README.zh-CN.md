# ETL-pruned

[English Version](./README.md)

`ETL-pruned` 是一个镜像仓库，用于为 [ETLCPP/etl](https://github.com/ETLCPP/etl) 提供**可直接作为 Git Submodule 引入的 release 分支**。

## 仓库作用

有些业务仓库希望通过 Git Submodule 引入 ETL，但只需要精简后的发布内容。

本仓库维护一个 `release` 分支，仅包含：

- `include/etl/` 的内容（平铺到仓库根目录）
- `LICENSE`
- 上游 `README.md`
- `version.txt`

这样可以保证作为子模块接入时更轻量、结构稳定。

## 使用

### 初始化：引入 Submodule

在业务仓库中添加 `release` 分支子模块：

```bash
git submodule add -b release https://github.com/inaku-Gyan/ETL-pruned.git third_party/etl
git submodule update --init --recursive
```

### 更新到最新发布内容

```bash
# 进入 Submodule 目录，更新子仓库
cd third_party/etl
git fetch
git checkout release        # 签出到最新的 release，或手动指定某个具体 tag / commit

# 回到主仓库，提交 Submodule 的变更
cd ../..
git add .
git commit -m "chore: update ETL submodule"
```

## 在本仓库发布：Release 如何生成

工作流文件：[release.yml](.github/workflows/release.yml)

通过手动触发（`workflow_dispatch`）并传入：

- `tag`：上游 ETL 的 `X.Y.Z` 版本号（必填）
- `commit_message`：可选的附加提交信息

工作流会执行：

1. 校验 `tag` 是否符合 `X.Y.Z` 格式。
2. 按该 tag 稀疏检出上游 `ETLCPP/etl`。
3. 检出本仓库 `release` 分支。
4. 清理 `release` 分支旧内容。
5. 复制精简后的发布文件到 `release`。
6. 提交并推送到 `release` 分支。

## 说明

- 不可直接在 `release` 分支开发功能，该分支由工作流自动生成。
- 需要新版本时，触发 release workflow 并指定新的上游 tag 即可。
