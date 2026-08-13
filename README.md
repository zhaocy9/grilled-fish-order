# 凡哥烤鱼预订系统 - 部署指南

## 方案说明

用 **GitHub 仓库本身当数据库**，不需要注册任何额外服务。订单数据以 JSON 文件存在仓库的 `data/` 文件夹里，你随时可以在 GitHub 上查看或下载。页面还提供一键导出 CSV 表格功能。

## 需要上传到 GitHub 仓库的文件

```
你的仓库/
├── index.html              ← 主页面
├── stall-bg.jpg            ← 摊位背景图
├── fish-bg.jpg             ← 烤鱼背景图
└── README.md               ← 本说明文件
```

> 部署后系统会自动在仓库里创建 `data/orders.json` 和 `data/rest-days.json` 两个文件存储数据。

---

## 部署分三步

### 第一步：创建 GitHub 仓库并上传文件

1. 登录 GitHub，点击右上角 **+** → **New repository**
2. 仓库名填 `grilled-fish-order`，选 **Public**，勾选 **Add a README file**
3. 点击 **Create repository**
4. 点击 **Add file → Upload files**
5. 把 `index.html`、`stall-bg.jpg`、`fish-bg.jpg` 拖进去
6. 点击 **Commit changes**

---

### 第二步：生成 GitHub Token 并填入 index.html

1. 点击右上角头像 → **Settings**
2. 左侧最底部找到 **Developer settings**
3. 点击 **Personal access tokens** → **Fine-grained tokens** → **Generate new token**
4. 填写：
   - **Token name**: `grilled-fish`（随便填）
   - **Expiration**: 选 90 天或更长
   - **Repository access**: 选 **Only select repositories** → 选你刚创建的 `grilled-fish-order` 仓库
   - **Permissions** → **Repository permissions** → 找到 **Contents** → 选 **Read and write**
   - 其他权限不用动
5. 点击 **Generate token**
6. **复制 token**（只显示一次！形如 `github_pat_xxxxxxxxxxxx`）

7. 用记事本或 VS Code 打开 `index.html`，找到这 4 行（在文件前半部分）：

```javascript
var GH_TOKEN = '';        // GitHub Personal Access Token
var GH_OWNER = '';        // 你的 GitHub 用户名
var GH_REPO = '';         // 仓库名
var GH_BRANCH = 'main';   // 分支名
```

8. 填入你的信息，例如：

```javascript
var GH_TOKEN = 'github_pat_xxxxxxxxxxxx';
var GH_OWNER = 'zhangsan';
var GH_REPO = 'grilled-fish-order';
var GH_BRANCH = 'main';
```

9. 保存文件，重新上传到 GitHub（覆盖之前的 `index.html`）

---

### 第三步：开启 GitHub Pages

1. 在仓库页面点击 **Settings** 标签
2. 左侧菜单找到 **Pages**
3. **Source** 选 **Deploy from a branch**
4. **Branch** 选 `main`，文件夹选 `/ (root)`
5. 点击 **Save**
6. 等 1-2 分钟，刷新页面，上方会出现：

```
Your site is live at https://你的用户名.github.io/grilled-fish-order/
```

7. 打开链接验证：页面正常显示日历和菜单（没有红色警告）= 部署成功
8. 把链接发到微信群即可

---

## 数据存储位置

所有订单数据存在你 GitHub 仓库的 `data/` 文件夹里：

| 文件 | 内容 | 格式 |
|------|------|------|
| `data/orders.json` | 全部预订订单 | JSON 数组 |
| `data/rest-days.json` | 休息日列表 | JSON 数组 |

**查看方法**：打开你的 GitHub 仓库 → 点击 `data` 文件夹 → 点击 `orders.json` 即可看到所有订单数据。

**导出方法**：在页面底部的"全部预订汇总"区域，点击 **「导出CSV表格」** 按钮即可下载 Excel 可直接打开的表格文件。

---

## 常见问题

**Q: 打开页面显示"数据库未配置"红色警告？**
A: 说明 `GH_TOKEN`、`GH_OWNER`、`GH_REPO` 没填或填错了，检查第二步。

**Q: 提交订单提示"写入失败"？**
A: Token 权限不够。确认 Token 的 Contents 权限选了 **Read and write**，且仓库选对了。

**Q: Token 过期了怎么办？**
A: 重新生成一个 Token，更新 `index.html` 里的 `GH_TOKEN` 值，重新上传即可。

**Q: Token 放在网页里安全吗？**
A: Token 确实会被技术人员在网页源码中看到。但 Fine-grained Token 已限制为只能操作这一个仓库的文件，风险可控。建议定期更换 Token（过期时间设短一些）。

**Q: 两个人同时提交会冲突吗？**
A: GitHub API 使用 SHA 机制防止覆盖，如果冲突会提示失败，重新提交即可。小群使用基本不会遇到。

**Q: 图片不显示？**
A: 确保 `stall-bg.jpg` 和 `fish-bg.jpg` 与 `index.html` 在同一目录，文件名完全一致。

**Q: 需要修改菜单价格？**
A: 编辑 `index.html`，找到 `MENU` 数组，修改对应价格即可。

**Q: 数据太多 GitHub 仓库会满吗？**
A: GitHub 每个文件 100MB 限制，一条订单约 200 字节，存 10 万条都没问题。
