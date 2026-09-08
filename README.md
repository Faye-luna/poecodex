# poecodex
通过codex app以及codex cll使用poe的额度
如果你使用的是 Codex CLI，可以这样接入
Poe 官方提供了以下流程：

在 Poe 的 API → API Keys 页面生成自己的 API Key。不要把密钥发给我或其他人。
如果尚未安装 Codex，在终端运行：
bash
npm install -g @openai/codex

建议先尝试临时会话方式：
bash
npx poe-code@latest wrap codex

这是 Poe 官方推荐的接入命令；临时会话方式不会永久修改你的 Codex 配置。接入后，该会话的模型请求会通过 Poe，而不是继续消耗已经用完的 ChatGPT 套餐额度。Poe 的安装说明要求 Node.js 22 或更新版本。(poe.com)
1．先让终端和 Codex 都能通过 Clash 联网
你可以选择下面两种方式。如果主要使用 Codex 桌面 App，我建议先尝试方式 A。

方式 A：Clash 的 TUN／虚拟网卡模式
如果你的 Clash 客户端提供这些选项：

保持 Clash 正常运行。
开启 TUN 模式／虚拟网卡模式。
如果提示安装服务或授予权限，按客户端提示完成。
确认相关流量的规则不是直连。
不同 Clash 客户端的按钮名称可能不同。以 Mihomo 内核文档为例，TUN 配合自动路由可以将流量路由进虚拟网卡；它与仅设置浏览器或系统 HTTP 代理不是同一种机制。(wiki.metacubex.one)

方式 B：给当前终端明确设置代理
如果你不想开启 TUN，可以先给终端设置代理。

在 Clash 设置中找到 HTTP 端口或 Mixed Port／混合端口。以下假设为 7890，请替换成你自己的实际端口，不要使用控制面板端口或纯 SOCKS 端口。Mihomo 的混合端口支持 HTTP 和 SOCKS 代理。(wiki.metacubex.one)

Windows：打开 PowerShell，执行：

powershell
$env:HTTP_PROXY = "http://127.0.0.1:7890"
$env:HTTPS_PROXY = "http://127.0.0.1:7890"
$env:NO_PROXY = "localhost,127.0.0.1"
$env:NODE_USE_ENV_PROXY = "1"

macOS：打开“终端”，执行：

bash
export HTTP_PROXY="http://127.0.0.1:7890"
export HTTPS_PROXY="http://127.0.0.1:7890"
export NO_PROXY="localhost,127.0.0.1"
export NODE_USE_ENV_PROXY=1

这里 HTTPS_PROXY 的值仍以 http:// 开头，是因为连接的是本机 HTTP 代理。NODE_USE_ENV_PROXY 用于让支持该功能的 Node.js 请求读取代理环境变量；下面建议使用 Node.js 24.5 或更新版本。(nodejs.org)

后续命令请在同一个终端窗口中执行。 桌面应用不一定继承终端环境变量，因此不能仅凭终端代理设置就判断桌面 App 也能联网。(help.openai.com)

2．检查 Node.js 和 Codex CLI
在终端逐条执行：

bash
node -v
npm -v
codex --version

按结果处理：

**都能显示版本号：**继续下一步。
**node 或 npm 找不到：**先安装 Node.js。为配合上面的代理方式，建议安装 24.x 的较新版本，至少 24.5。
**只有 codex 找不到：**先安装命令行版，执行：
bash
npm install -g @openai/codex

这是 Poe 官方教程使用的安装命令；其最低 Node.js 要求是 22，但上面的环境变量代理方案使用了较新的 Node.js 功能。(poe.com)

Node.js 官方下载入口，可复制到浏览器：

text
https://nodejs.org/en/download

如果你只装了 Codex 桌面 App，不要因此卸载它；我们先用 CLI 检查接入，再回到 App。

3．备份原来的 Codex 配置
建议先完全退出 Codex App，然后打开用户配置目录：

系统	默认目录
Windows	在资源管理器地址栏输入 %USERPROFILE%\.codex
macOS	Finder →“前往”→“前往文件夹”，输入 ~/.codex
将里面已有的：

text
config.toml
.env

各复制一份，另起名字，例如：

text
config.toml.before-poe
.env.before-poe

不存在的文件可以跳过。不要删除整个 .codex 文件夹，也不要把其中的密钥文件发给别人。

Codex 的用户配置使用 ~/.codex/config.toml；环境变量也可通过 ~/.codex/.env 提供。修改后需要重启桌面应用。(help.openai.com)

4．登录有余额的 Poe 账号
在前面的终端运行：

bash
npx poe-code@latest login

如果出现是否下载 poe-code 的询问，确认包名正确后再同意。

按提示在浏览器完成登录，确认登录的是拥有那 992,885 积分的 Poe 账号。Poe Code 官方说明支持浏览器登录，也支持直接提供 Poe API Key。(github.com)

如果浏览器登录无法完成
先在浏览器打开 Poe API Key 页面，创建自己的密钥：

text
https://poe.com/api/keys

这是 Poe 官方 API 文档给出的密钥入口。(creator.poe.com)

然后可使用官方支持的备用命令：

bash
npx poe-code@latest login --api-key "替换成你自己的Poe API Key"

注意：直接在命令中粘贴密钥可能留下终端历史记录，因此优先使用浏览器登录。不要把带有真实密钥的命令或截图发给我。(github.com)

5．将 Codex 配置为使用 Poe
执行：

bash
npx poe-code@latest configure codex

这是 Poe 官方提供的持久配置命令，区别于只对单次终端会话生效的 wrap codex。(poe.com)

按向导提示完成配置：

如果要求登录，使用上一步的 Poe 账号。
如果要求选择模型，选择向导提供的可用模型。
先保留普通审批／确认设置，不要为了测试开启不受限制的自动执行。
模型名称以 Poe 当时提供的列表为准，不要直接照搬 ChatGPT 界面里的模型名称。Poe Code 也提供模型列表命令，可以筛选支持 Responses API 的模型：(github.com)

bash
npx poe-code@latest models --endpoint /v1/responses

完成后，暂时不要再手动覆盖 config.toml，以免把工具生成的配置改坏。

6．先在终端验证，再打开桌面 App
A．终端验证
建议进入一个新建的空文件夹，再执行：

bash
codex

进入 Codex 后输入：

text
/status

查看当前模型及服务提供商，确认使用的是刚配置的 Poe 提供商，而不是仍走原来的 OpenAI／ChatGPT 模型连接。Codex 的 /status 可以用于检查当前提供商。(poe.com)

然后只发送一个小测试：

text
只回复“连接成功”，不要读取文件，不要执行命令。

B．桌面 App 验证
终端测试成功之后：

确认 Codex App 已完全退出。
保持 Clash 运行。
重新打开 Codex App。
新建一个本地会话，不要先继续原来的云端任务。
发送同样的小测试。
Poe Code 的持久配置方案面向 CLI 和桌面应用；OpenAI 的本地提供商配置说明也要求修改配置后重启，并新建会话验证。不过，我无法在不知道你 App 版本的情况下保证它的具体界面表现。(github.com)

如果终端可以，桌面 App 却连接超时
如果没有使用 TUN，可尝试在用户目录下的 .codex/.env 中加入以下内容，保留文件里已有的其他设置，不要整份覆盖：

dotenv
HTTP_PROXY=http://127.0.0.1:7890
HTTPS_PROXY=http://127.0.0.1:7890
NO_PROXY=localhost,127.0.0.1

替换端口后，完全退出并重启 Codex。这里使用的是 Codex 支持的环境文件机制及代理环境变量；如果仍不成功，需要结合你的具体版本排查。(help.openai.com)

7．确认确实在扣 Poe 积分
不要只看“模型能回复”，还要看 Poe 的用量记录。

在另一个终端中执行；如果采用方式 B，先重复第 1 步的代理设置：

bash
npx poe-code@latest usage

查看近期使用记录：

bash
npx poe-code@latest usage list --limit 10

这两个命令分别用于查看积分余额和用量历史。(github.com)

核对刚才的请求时间、模型及积分消耗。只有出现对应的 Poe 用量记录，才能确认这次测试实际走了 Poe。

Poe 官方明确说明，订阅积分和购买的附加积分可以用于 API；因此你的这类积分不需要先换成 OpenAI 美元余额。(creator.poe.com)

8．如果需要撤销
可以使用 Poe Code 的撤销配置功能：

bash
npx poe-code@latest unconfigure codex

然后完全退出、重新打开 Codex。必要时再根据第 3 步的备份恢复原配置。撤销配置用于移除 Poe Code 添加的覆盖设置。(github.com)

建议你先执行到第 2 步，把下面四项发给我，我就能继续按你的实际环境排查：

Windows 还是 macOS？
Clash 的具体客户端名称，以及 HTTP／混合端口。
node -v 和 codex --version 的输出。
你主要想用 Codex 桌面 App，还是终端。
不需要提供 Poe API Key、Clash 订阅链接或完整账号信息。

