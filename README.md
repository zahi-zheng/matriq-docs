# MatriQ Cloud SDK 文档

MatriQ Cloud 面向 SDK 集成方的公开接口文档,托管在 GitHub Pages:

**https://zahi-zheng.github.io/matriq-docs/**

## 仓库说明

- `docs/SDK_API.md` 由私有源码仓库 `matriq-cloud` 的 GitHub Actions **自动同步**,请勿在本仓库直接编辑,改动会被下一次同步覆盖。
- `docs/index.md`(落地页)与站点配置在本仓库维护。
- 文档规范位置:接口契约以线上环境的 `/openapi.json` 为准。

## 本地预览

```bash
pip install mkdocs-material
mkdocs serve
# 打开 http://127.0.0.1:8000
```

## 发布

推送到 `main` 后,GitHub Actions 自动构建并发布到 Pages(`.github/workflows/docs.yml`)。
