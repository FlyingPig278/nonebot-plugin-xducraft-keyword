# XDUCraft Keyword

群关键词自动回复与新成员入群欢迎插件。支持按群独立维护规则、多种匹配模式、多段图文回复以及私聊免刷屏配置。

如果需要部署包含全部插件的完整机器人，请阅读[整套机器人的安装说明](https://github.com/FlyingPig278/XDUCraft-bot)。

## 功能与行为

在已启用的群中，机器人会根据配置的规则自动回复消息。以斜杠 `/` 开头的消息直接交给指令处理，不会触发关键词回复。

- 匹配模式：支持包含（contains）、完全匹配（exact）、开头匹配（prefix）及正则表达式（regex）。默认匹配模式为包含。
- 多段回复：每条规则支持按顺序发送 1 至 8 条回复消息，支持文本、本地图片、QQ 表情与 @ 混排。
- 冷却控制：支持群默认冷却时间与单条规则独立冷却时间，避免高频重复触发。
- 新成员入群欢迎：当有新成员加入群聊时触发入群欢迎消息链，首条回复消息自动 @ 新入群成员。
- 媒体图片保存：回复消息中的图片在录制时由插件下载保存至数据目录的 `media/` 文件夹中（单张上限 10 MiB），发送时读取本地文件。

## 安装与加载

在现有 NoneBot 2.4.x 项目环境中，先安装 Core 核心包，再安装本插件：

```bash
pip install "nonebot-plugin-xducraft-core[onebot] @ git+https://github.com/FlyingPig278/nonebot-plugin-xducraft-core.git@v0.3.0"
pip install "nonebot-plugin-xducraft-keyword @ git+https://github.com/FlyingPig278/nonebot-plugin-xducraft-keyword.git@v0.1.0"
```

在已有项目的 `pyproject.toml` 中的 `plugins` 列表中追加模块，不要覆盖已有配置：

```toml
[tool.nonebot]
plugins = [
    # ... 已有插件 ...
    "xducraft_config",
    "xducraft_keyword",
]
```

## 启用步骤

插件的全局总开关与群功能开关默认均为关闭。两处开关均开启后自动回复才会生效。

首先在 WebUI 管理面板的插件页面开启关键词回复插件，或者参考[不使用网页时启用插件](https://github.com/FlyingPig278/nonebot-plugin-xducraft-core#不使用网页时启用插件)在代码中单次启用。全局未开启时，管理指令不响应。

接着在需要开启的群内启用该功能。若安装了 `nonebot-plugin-xducraft-features` 插件，群管理员可在群内发送 `/功能 on 关键词回复`；若未安装该插件，请在 WebUI 管理面板的群聊页面开启，或者由管理员在私聊中发送 `/关键词 <群号> on` 开启，也可以参考上述 Core 链接中的代码启用该群。

## 指令与私聊配置流程

为避免在群聊中录制回复或配置规则造成刷屏，所有管理配置均在与机器人的私聊中完成。

### 群内引导

群主或管理员在群内发送 `/关键词`，机器人会通过私聊向发送者推送该群的操作菜单。若机器人无法向发送者发送私聊，需先添加机器人好友。

### 私聊管理指令

需具备目标群的管理员或群主身份，SUPERUSER 不受群权限限制。所有指令支持别名 `/kw` 与 `/keyword`：

- `/关键词 <群号>`：查看指定群的规则总览与操作菜单。
- `/关键词 <群号> on` 或 `/关键词 <群号> off`：开启或关闭指定群的自动回复。
- `/关键词 <群号> add <关键词>`：添加关键词规则。发送后进入录制流程，管理员逐条发送回复内容（文字、图片、表情、@ 均可，最多 8 条），全部发完后单独发送“完成”，放弃保存则发送“取消”。
- `/关键词 <群号> del <关键词>`：删除指定关键词规则。
- `/关键词 <群号> show <关键词>`：查看指定关键词规则的详细配置与回复内容。
- `/关键词 <群号> mode <关键词> <模式>`：修改匹配模式，可选模式为：`包含`、`完全`、`开头`、`正则`。
- `/关键词 <群号> cooldown <秒>`：设置该群默认的回复冷却时间（0 至 3600 秒）。
- `/关键词 <群号> join set`：录制该群的新成员入群欢迎消息。进入录制流程后逐条发送内容，最后发送“完成”保存。
- `/关键词 <群号> join show`：查看该群当前配置的入群欢迎消息。
- `/关键词 <群号> join clear`：清空该群的入群欢迎消息。

## 数据存储与自定义路径

关键词规则与群配置保存在 Core 配置数据库中。若存在历史配置文件 `keyword_config.json`，启动时会自动导入，旧文件不再保存新设置，后续修改以数据库为准。

媒体图片保存在数据目录下的 `media/` 文件夹中。若需要指定外部数据目录，必须在启动前通过操作系统环境变量设置 `XDUCRAFT_DATA_DIR`（绝对路径），不能仅写在 `.env` 中。

## 本地开发与测试

```bash
pip install "nonebot-plugin-xducraft-core[onebot] @ git+https://github.com/FlyingPig278/nonebot-plugin-xducraft-core.git@v0.3.0"
pip install -e ".[dev]"
pytest
python -m build
```

## 许可证

本项目采用 [MIT License](LICENSE) 授权开源。
