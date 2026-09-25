# 自用 GLaDOS 自动签到

<div align="center">


---


### 📱 签到成功预览

![签到成功示例](images/success.jpg)
 **每天签到累积可兑换会员时长！**

---


**⭐ 觉得有用？点个 Star 支持一下！**

</div>

---
## 💡 重要说明
GitHub Actions 的自带定时功能很不稳定，实测大概率不会自动触发，使用 **cron-job.org**（免费服务）来触发签到。
>
>
>
---

## ✨ 功能特点

| 功能            | 说明                            |
| --------------- | ------------------------------- |
| 🎯 **精准积分** | 获取真实积分数据 + 每日变化量   |
| 🎁 **兑换提示** | 显示当前可兑换选项及差额        |
| 📱 **tg推送**   | 漂亮 HTML 报告           |

---

## 🛠 配置参数 (环境变量)

本项目支持以下环境变量配置：

| 变量名               | 必填  | 说明                                                                       |
| -------------------- | ----- | -------------------------------------------------------------------------- |
| `GLADOS_COOKIE`      | ✅ 是 | GLaDOS 的 Cookie。多个账号请用 `&` 或换行符分隔。                          |
| `TELEGRAM_BOT_TOKEN` | ❌ 否 | Telegram 机器人的 Token（例如 `123456:ABC-DEF1234...`）                    |
| `TELEGRAM_CHAT_ID`   | ❌ 否 | 接收推送的 Telegram Chat ID                                                |
| `GLADOS_EXCHANGE_PLAN`         | ❌ 否 | 配置自动兑换积分策略 plan100 plan200 plan500|

---



## 🚀 快速部署

### 第一步：Fork 本仓库

点击页面右上角的 **Fork** 按钮，将项目复制到你的账号下。

---

### 第二步：获取 Cookie 🍪

打开浏览器，登录：https://glados.cloud
按 F12 打开开发者工具
找到：
Chrome：Application → Cookies
Firefox：存储 → Cookies
选择 glados.cloud
复制完整 Cookie 内容

gld:sess=xxxxxx; gld:sess.sig=yyyyyy
⚠️ 必须是完整的一整段，不要只复制一半



####  组合 Cookie（重要！）

将两个值按以下格式组合，**注意格式必须完全正确**：

```text
gld:sess=你的长字符串; gld:sess.sig=你的短字符串
```

**正确示例**：

```text
gld:sess=eyJ1c2VySWQiOjEyMzQ1Njc4OTB9; gld:sess.sig=abcdef123456
```

**常见错误**：

- ❌ 缺少分号 `;`
- ❌ 缺少空格（分号后需要一个空格）
- ❌ 值两边多了引号
- ❌ 复制了多余的空格或换行


### 第三步：配置 GitHub Secrets 🔐

1. 进入你 Fork 的仓库
2. 点击 **Settings**（设置）
3. 左侧菜单找到 **Secrets and variables** → **Actions**
4. 点击右上角 **New repository secret**


添加以下四个 Secret：

| Name             | Value                    | 必需  |
| ---------------- | ------------------------ | ----- |
| `GLADOS_COOKIE`  | 第二步组合的 Cookie      | ✅ 是 |
| `TELEGRAM_BOT_TOKEN` |  | ❌ 否 |
| `TELEGRAM_CHAT_ID` |  | ❌ 否 |
| `GLADOS_EXCHANGE_PLAN` |  | ❌ 否 |
---


### 第四步：启用 Actions ⚡

1. 进入你 Fork 仓库的 **Actions** 标签页
2. 如果看到黄色提示，点击 **I understand my workflows, go ahead and enable them**
3. 点击左侧的 **GLaDOS  Checkin**
4. 点击右侧 **Run workflow** 按钮手动测试一次



---
## ⭐ 推荐方案：cron-job.org 配置定时

### 配置步骤

#### 第一步：获取 GitHub Personal Access Token

1. 访问 [https://github.com/settings/tokens](https://github.com/settings/tokens)
2. 点击 **Generate new token** → **Generate new token (classic)**
3. 按如下配置：


| 选项           | 值                              |
| -------------- | ------------------------------- |
| **Name**       | `glados-cron`（任意名称）       |
| **Expiration** | 选择 90 天或更久                |
| **勾选权限**   | ✅ **workflow**（在 repo 下方） |

4. 点击底部 **Generate token**
5. **立即复制生成的 token**（格式类似 `ghp_1234567890abcdef...`，只显示一次！）

> 💡 Token 示例：`ghp_NXLTUqT51BFfilsaZNlaVstacNnkZc4PYCNa`

#### 第二步：注册 cron-job.org

1. 访问 [https://cron-job.org](https://cron-job.org) 注册账号（免费）
2. 注册后登录，点击 **Create Cronjob** 创建任务

#### 第三步：创建签到任务（7:00）

![创建 Cron 任务](images/create_corn_job.png)

按照以下配置填写：

**基本信息**：

| 选项      | 填写                                                                                                   |
| --------- | ------------------------------------------------------------------------------------------------------ |
| **Title** | `GLaDOS 签到`                                                                                        |
| **URL**   | `[https://api.github.com/repos/你的用户名/glados-checkin/actions/workflows/checkin.yml/dispatches`|

> ⚠️ **重要**：把 `你的用户名` 改成你的 GitHub 用户名！`

**执行时间**：选择每天 **07:00**（Asia/Shanghai 时区）

**高级配置**（点击 Advanced 展开）：

![高级配置](images/cron_advanced.png)

| 选项               | 值            |
| ------------------ | ------------- |
| **Request method** | POST          |
| **Time zone**      | Asia/Shanghai |

**请求头（Headers）**：点击 "+ 添加" 添加三行：

| Key             | Value                            |
| --------------- | -------------------------------- |
| `Accept`        | `application/vnd.github.v3+json` |
| `Authorization` | `token 你复制的GitHub_Token`     |
| `Content-Type`  | `application/json`               |

> ⚠️ **注意**：Authorization 的值是 `token ` + **空格** + 你的 Token，例如：`token ghp_NXLTUqT51BFfilsaZNlaVstacNnkZc4PYCNa`

**请求体（Request body）**：选择 Raw Body，填入：

```json
{ "ref": "main" }
```


配置完成后点击 **Save** 保存。



#### 第四步：测试验证

1. 在任务列表点击 **Test run** 测试
2. 成功会显示 **204 No Content** ✅

![测试成功](images/cron_success.png)

3. 到 GitHub 仓库的 **Actions** 页面查看，应该有新的运行记录

---


### 🚨 常见陷阱与错误

| 错误                         | 现象         | 原因                   | 解决方法                                       |
| ---------------------------- | ------------ | ---------------------- | ---------------------------------------------- |
| **401 Unauthorized**         | 认证失败     | Authorization 格式错误 | 必须是 `token ghp_xxx`，注意 `token ` 后有空格 |
| **422 Unprocessable Entity** | 请求无法处理 | Body 缺少 ref 参数     | 改为 `{"ref": "main"}`                         |
| Accept 被截断                | 配置错误     | 输入框显示不全         | 完整值：`application/vnd.github.v3+json`       |
| Token 有空格                 | 认证失败     | Token 被意外截断       | Token 是连续字符串，中间不能有空格             |
| 权限不足                     | 403 错误     | Token 无 workflow 权限 | 重新生成 Token，勾选 workflow 权限             |

> 💡 **小贴士**：遇到 401/422 错误时，先检查上面三行 Headers 是否完全正确！

**🎉 完成！** 以后每天 7:00 会自动签到，注意有可能有延迟。

---


---

---

## 📂 项目文件

| 文件                                                                         | 说明                |
| ---------------------------------------------------------------------------- | ------------------- |
| [checkin.py](file://d:\workplace\2026-glados-checkin\checkin.py)             | 核心签到脚本        |
| `.github/workflows/checkin.yml`                                              | GitHub Actions 配置 |
| [requirements.txt](file://d:\workplace\2026-glados-checkin\requirements.txt) | Python 依赖         |
| `images/`                                                                    | 教程截图            |

---


## 📝 License

MIT

---

<div align="center">


**🔧 本项目经过 2026-05-07 验证可用！**


</div>
