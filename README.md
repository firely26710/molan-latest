# molan-latest

把 [baiji6/molanyinyueyuan](https://github.com/baiji6/molanyinyueyuan)（墨澜音乐源）自动同步成**固定文件名** `latest.js`，供洛雪音乐等支持自定义音源的播放器在线导入。

上游每次发版都会改 tag 和文件名（`墨澜音乐源v2.3.8.js` → `v2.3.9.js`），而 GitHub 的 `releases/latest/download/` 按文件名精确匹配，名字带版本号就会在下一次发版后静默 404。本仓库用一个定时工作流把最新版另存为 `latest.js`，**导入地址永远不用改**。

## 在线导入地址

把 `<你的用户名>` 换成自己的 GitHub 用户名：

```
https://v4.gh-proxy.com/https://raw.githubusercontent.com/<你的用户名>/molan-latest/main/latest.js
```

打不开时换备用前缀：

```
https://v4.gh-proxy.org/https://raw.githubusercontent.com/<你的用户名>/molan-latest/main/latest.js
https://gh-proxy.org/https://raw.githubusercontent.com/<你的用户名>/molan-latest/main/latest.js
```

浏览器打开能看到 `/*!` 开头、数百 KB 的脚本正文即正常。可选校验：

```bash
sha256sum latest.js   # 与上游 Release 页面公布的 sha256 对照，该值随版本变化
```

### 导入步骤

设置 → 基本设置 → 自定义源管理 → **在线导入** → 粘贴上面地址 → 启用。导入后搜一首歌确认能播放。

## 同步规则

- 每天 **UTC 01:23（北京时间 09:23）** 检查一次上游最新 Release，有新版就覆盖 `latest.js`。
- 没有新版也推一条空提交，保持仓库活跃，避免 GitHub 在 60 天无活动后自动停用定时任务。
- 同步失败不会清空已有文件，会停在最后一版。
- 逻辑见 [.github/workflows/sync.yml](.github/workflows/sync.yml)。改 `cron: '23 1 * * *'` 可换时间；GitHub cron 按 UTC 计算，最小间隔 5 分钟，建议避开整点。

## 自己搭一个

1. 新建 **Public** 空仓库，勾选 *Add a README*（确保默认分支 `main` 存在）。
2. Settings → Actions → General → **Workflow permissions** → 选 *Read and write permissions*。
3. 新建 `.github/workflows/sync.yml`，内容照抄本仓库同名文件。
4. Actions → `sync-molan-latest` → **Run workflow** 手动跑一次，根目录出现 `latest.js` 才算成功。不先跑这一次，导入地址会 404。
5. 按上面格式拼好导入地址，填进播放器即可，以后再也不用动。

## 注意

- 定时工作流只认**默认分支**上的文件；Fork 来的仓库默认禁用 schedule，还要在 Actions 页点「启用工作流」，所以建议直接新建空仓库。
- 自定义音源是会被播放器执行的脚本，不是普通配置。本仓库只从上游官方 Release 取文件，不修改脚本内容，导入前可自行核对 `latest.js` 的开头与大小。
- 上游发新版后经代理边缘缓存，通常几分钟到十几分钟生效。
- 上游仓库若被删除，本仓库会停在最后一版，不会报错。

## 上游

- 项目：<https://github.com/baiji6/molanyinyueyuan>
- 最新 Release：<https://github.com/baiji6/molanyinyueyuan/releases/latest>
- 上游脚本以 MIT 许可发布；本仓库仅做自动搬运。
