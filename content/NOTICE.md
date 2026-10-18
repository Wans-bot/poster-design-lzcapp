# 迅排设计 · 懒猫微服移植说明（NOTICE）

本包为开源项目「迅排设计」（Poster Design）在懒猫微服平台上的移植版本，非上游官方发行版。

## 上游信息

- 项目：迅排设计（Poster Design / xunpai-design）
- 仓库：https://github.com/palxiao/poster-design
- 本次移植固定版本：`main` 分支 commit `129c1ad1b9d7345795d4df1583da23cbfb377f2e`（2026-09-10）
- 上游版本号：2.0.0
- 许可：AGPL-3.0-or-later（见随包 `LICENSE`，中文说明见 `LICENSE-ZH`）
- 上游产品页：https://xpai.design

## 本移植的修改

按 AGPL-3.0 第 5 节要求，此处列出相对上游的全部修改。所有修改都以补丁形式保存在
本工程的 `image/` 与 `content/` 目录内，可随包获取。

1. `service/src/configs.ts`：`website` / `filePath` / `executablePath` 改为可被
   `SITE_URL` / `FILE_PATH` / `CHROME_PATH` 环境变量覆盖（原值为构建期硬编码的本机路径）。
2. `service/src/main.ts`：
   - `Content-Type: application/json` 只对 `/api`、`/design` 生效（原来污染所有响应，包括静态资源）；
   - 由同一进程托管前端构建产物并支持 history 回退（上游生产部署依赖 nginx 同源反代）；
   - 增加 `unhandledRejection` 记录，渲染任务异常不再终止整个服务进程。
3. `service/src/utils/download-single.ts`：修正 Chromium 启动参数数组（上游混入了全角破折号，
   会被 Chrome 当作位置参数/多目标，导致启动失败）。
4. `service/src/utils/node-queue.ts`：任务队列补 `catch`，单个渲染任务失败不再变成未处理拒绝。
5. `index.html`：移除不蒜子统计脚本（第三方外部统计，自托管场景不适用）。
6. `service/seed/poster.db`：删除上游自带的示例管理员 `admin / 123456`（改由部署时环境变量创建），
   并把 28 处写成 `http://127.0.0.1:7001/static/...` 的静态资源地址改为相对路径。
7. `lzc-manifest.yml`（本平台新增，不属于上游）：路由、健康检查、持久化挂载、免密注入与网盘文件选择器。

## 懒猫微服平台相关的适配文件

- `content/lazycat-injects/xp-autologin.js`：本地编写的免密登录与「切换账号」注入脚本。
- `content/lazycat-injects/lzc-file-chooser-inject.js`：平台官方《自动拦截文件选择器》注入脚本，
  取自 https://developer.lazycat.cloud/lazycat-injects/lzc-file-chooser-inject.js ，未修改。

## 源码获取

- 上游源码：https://github.com/palxiao/poster-design （commit
  `129c1ad1b9d7345795d4df1583da23cbfb377f2e`）
- 包含本移植全部修改的可重现工程：见交付说明中列出的工程实例目录
  （`examples/cloud.lazycat.app.poster-design/<version>/`）。
