# Mahjong Hex Web Room (Java)

这是一个纯 JDK 的联机网页应用（不依赖 Maven/Gradle）。

## 已实现功能

- 多人同房间联机（电脑、手机都可访问）
- 进入房间必须输入姓名
- 姓名全房间唯一，不允许重复
- 全员共享海克斯池，单轮不重复，抽完自动进入下一轮
- 每个玩家只能看到自己的海克斯，其他人的海克斯内容会隐藏
- 每张海克斯可点击“使用”，弹窗选择“对自己 / 对所有人”
- 使用海克斯时可指定目标玩家（按姓名显示）
- 清空房间会清空玩家、海克斯和记录，所有人需重新加入
- 刷新页面不丢身份和状态（浏览器保存 `playerId` + 服务端持久化）
- 自动同步已优化为“5 秒轮询 + 仅有变更才重绘”，减少页面抖动

## 本地运行

```powershell
cd E:\code\mahjong-hex-web-main
javac -encoding UTF-8 --add-modules jdk.httpserver MahjongHexWebApp.java
java --add-modules jdk.httpserver MahjongHexWebApp
```

默认端口：`8080`  
可通过环境变量覆盖端口：

```powershell
$env:PORT=9000
java --add-modules jdk.httpserver MahjongHexWebApp
```

## 局域网访问（同 Wi-Fi）

- 本机：`http://localhost:8080`
- 手机：`http://你的电脑IPv4:8080`

## 单机版（无需服务器，发文件即用）

`mahjong-hex-offline.html` 是一个完全独立的 HTML 文件，双击即可在浏览器中使用，无需任何服务器。

- 海克斯池在本地管理，抽完自动进入下一轮
- 状态保存在浏览器 localStorage 中，刷新不丢失
- 适合线下聚会时各人自用，或通过微信/QQ 发给朋友

### 部署到 GitHub Pages（发链接即用，国内可访问）

已配置好 `docs/` 文件夹，按以下步骤操作即可：

#### 第一步：注册 GitHub 账号

打开 https://github.com ，点击 **Sign up** 注册（已有账号跳过）。

#### 第二步：创建仓库

1. 登录后，点击右上角 **+** → **New repository**
2. Repository name 填 `mahjong-hex-web`（或任意英文名）
3. 选 **Public**（免费用户必须公开仓库才能用 Pages）
4. 其他保持默认，点 **Create repository**

#### 第三步：上传代码

**方式 A：网页上传（最简单，不用装任何软件）**

1. 在新建的仓库页面，点击 **uploading an existing file**
2. 把以下文件/文件夹拖进去：
   - `docs/` 文件夹（里面有 `index.html`，这是单机版页面）
   - `index.html`
   - `mahjong-hex-offline.html`
   - `MahjongHexWebApp.java`
   - `Dockerfile`
   - `render.yaml`
   - `README.md`
   - `.gitignore`
   - `start_public_room.ps1`
3. 底部写个备注如 `initial commit`，点 **Commit changes**

**方式 B：命令行上传（装了 Git 的情况）**

```powershell
cd E:\code\mahjong-hex-web-main
git init
git add .
git commit -m "initial commit"
git branch -M main
git remote add origin https://github.com/你的用户名/mahjong-hex-web.git
git push -u origin main
```

#### 第四步：开启 GitHub Pages

1. 打开仓库页面，点 **Settings**（仓库名下方的标签）
2. 左侧菜单找到 **Pages**
3. **Source** 选 **Deploy from a branch**
4. **Branch** 选 **main**，文件夹选 **/docs**，点 **Save**
5. 等 1-2 分钟，页面顶部会出现你的网站地址：
   `https://你的用户名.github.io/mahjong-hex-web/`

#### 第五步：分享链接

把 `https://你的用户名.github.io/mahjong-hex-web/` 发给别人，手机和电脑都能直接打开使用，不需要 VPN。

> 如果后续修改了 `mahjong-hex-offline.html`，记得同步更新 `docs/index.html`，然后重新上传，GitHub Pages 会自动更新。

## 公网联机（不同 Wi-Fi）

### 快速方案（无需部署，直接出公网链接）

项目已提供脚本：

```powershell
cd E:\code\mahjong-hex-web-main
powershell -ExecutionPolicy Bypass -File .\start_public_room.ps1
```

运行后终端会出现类似 `https://xxxx.localhost.run` 的地址。
把这个地址发给别人，手机和电脑在不同网络下也可进入同一房间。

说明：

- 该方案依赖 `localhost.run` 隧道服务
- 链接通常是临时的，重启脚本后会变化
- 当前终端关闭（或 Ctrl+C）后公网链接失效

如果手机出现 `Failed to fetch`：

1. 确认打开的是 `https://xxxx.localhost.run` 链接，不是 `localhost`
2. 确认房主机器上的脚本终端没有关闭
3. 如果脚本提示端口被占用，先关闭旧 Java 进程再重启脚本

### 一键部署到 Render（免费，固定链接）

1. 将项目推送到 GitHub 仓库
2. 登录 [Render](https://render.com)，点击 **New +** → **Web Service**
3. 连接你的 GitHub 仓库，Render 会自动检测 `render.yaml` 和 `Dockerfile`
4. 点击 **Create Web Service**，等待部署完成
5. 部署完成后会得到一个类似 `https://xxx.onrender.com` 的固定链接
6. 把链接发给别人即可多人联机

> Render 免费方案会在 15 分钟无请求后休眠，首次访问需等待约 30 秒唤醒。

### 其他方案（Railway / VPS）

把服务部署到公网服务器，使用固定域名访问。

关键点：

1. 保持服务监听 `0.0.0.0`（项目已支持）
2. 使用平台分配的 `PORT`（项目已支持）
3. 部署后把公网 URL 发给其他人，手机和电脑都能联机

## 数据文件

- `room_state.bin`：房间状态持久化文件（自动生成）
- `start_public_room.ps1`：启动本地服务并建立临时公网隧道
- `mahjong-hex-offline.html`：单机版源文件
- `docs/index.html`：GitHub Pages 部署用的单机版（与上面内容相同）
- `render.yaml`：Render 云端部署配置

删除 `room_state.bin` 会重置房间状态。

## API

- `GET /api/state?playerId=...`
- `POST /api/join` 参数：`name`, `playerId(可选)`
- `POST /api/draw` 参数：`playerId`
- `POST /api/use` 参数：`playerId`, `cardId`, `target(SELF|ALL)`
- `POST /api/clear`
