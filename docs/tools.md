# Tools

## Windows

- 查看 Wifi 历史与 Wifi 密码

```powershell
# [Settings] → [Network & internet] → [Wifi] → [Manage known networks]
netsh wlan show profiles
netsh wlan show profiles name="<Wifi Name>" key=clear
```

## Git

### gitattributes

修改`~/.gitconfig`如下

```ini
[user]
    name =
    email =
[core]
    editor =
    autocrlf = false
    eol = lf
```

仓库根目录中添加 `.gitattributes`

```shell
* text=auto eol=lf

# Images
*.png  binary
*.jpg  binary
*.jpeg binary
*.gif  binary
*.bmp  binary
*.webp binary
*.ico  binary

# Other binary files
*.zip  binary
*.pdf  binary
*.exe  binary
*.dll  binary

# Windows
*.bat text eol=crlf
*.cmd text eol=crlf
```

然后执行下面脚本让已有文件重新规范化

```shell
git config --global core.autocrlf false

git add --renormalize .
git status
git commit -m "chore: renormalize line endings via .gitattributes"
```

### submodule

```shell
# Add
git submodule add -b <baranch_name> <repository-url> submodules/dir_name

# Clone
git clone --recurse-submodules <repository-url>

# Update
git submodule update --recursive --remote
```

### Git Commit

#### 使用规范

Git commit 信息包括 **标题（Header）、正文（Body）、脚注（Footer）** 三部分，格式如下

```html
<type>(<scope>): <subject>  # 标题（Header）：必选，一行完成
                            # 空行分隔
<body>                      # 正文（Body）：可选，详细描述
                            # 空行分隔
<footer>                    # 脚注（Footer）：可选，特殊说明（如关闭issue）
```

- **标题** 由 `type`（类型）、`scope`（范围）、`subject`（主题）三部分组成。

| 类型（type） | 含义说明                                     | 适用场景示例                                |
| :----------: | -------------------------------------------- | ------------------------------------------- |
|   **feat**   | 新增功能（Feature）                          | 新增用户登录接口、添加购物车功能            |
|   **fix**    | 修复 bug（Bug Fix）                          | 修复登录时密码加密错误、修复列表渲染异常    |
|   **docs**   | 仅修改文档（Documentation）                  | 更新 README、补充接口注释                   |
|  **style**   | 代码格式调整（不影响逻辑）                   | 缩进修正、变量名大小写调整、删除空行        |
| **refactor** | 代码重构（既不新增功能也不修复 bug）         | 提取公共函数、优化循环逻辑                  |
|   **perf**   | 性能优化（Performance）                      | 减少数据库查询次数、优化算法复杂度          |
|   **test**   | 新增 / 修改测试代码（Test）                  | 为登录功能添加单元测试、修复测试用例        |
|  **build**   | 构建流程 / 依赖管理修改（Build）             | 更新 npm 依赖版本、修改 webpack 配置        |
|    **ci**    | CI（持续集成）配置修改（CI Configuration）   | 调整 GitHub Actions 脚本、修改 Jenkins 任务 |
|  **chore**   | 其他琐碎修改（不影响代码逻辑 / 文档 / 测试） | 清理临时文件、修改.gitignore                |

`scope`（影响范围，可选）用于明确 `commit` 影响的模块或功能，便于快速定位修改边界。

`subject`（主题），以动词开头，如 `add, update`，首字母小写，避免与 `type` 重复，结尾不加句号。

- **正文** 每行不超过 72 个字符，说明为什么修改，而不是改了什么
- **脚注** 用于标注不兼容变更或关闭 issue

```html
BREAKING CHANGE: <描述不兼容的具体内容>

Closes #123, #456
```

#### 示例（AI）

```html
feat(cart): add "batch delete" function for selected items

Previously, users could only delete items in the cart one by one,
which was inefficient when there were many items to remove.

This change adds a "Delete Selected" button:
1. When users check multiple items, the button becomes enabled;
2. Clicking the button deletes all selected items at once;
3. Adds a confirmation modal to prevent accidental deletion.

BREAKING CHANGE: The `getUserInfo()` method is removed. Please use
the new `getUserProfile()` method instead (parameter `userId` is
required).

Closes #345
```

## AI

### [Claude Skills](https://agentskills.io/what-are-skills)

- 目录结构

```shell
my-skill/
├── SKILL.md          # Required: instructions + metadata
├── scripts/          # Optional: executable code
├── references/       # Optional: documentation
└── assets/           # Optional: templates, resources
```

```markdown
<!-- ~/.claude/skills/<skill-name>/SKILL.md -->
---
name: A short identifier
description: When to use this skill
---
```

### OpenClaw

- WorkSpace

|       文件       |                        作用                        |
| :--------------: | :------------------------------------------------: |
|  **AGENTS.md**   |       我的行为准则和工作指南——教我怎么做事的       |
|   **SOUL.md**    |         我的"灵魂"设定——定义我的性格和风格         |
|   **USER.md**    |     关于你的信息——我需要知道你是谁、怎么称呼你     |
|   **TOOLS.md**   |    你的工具/设备笔记——记录相机、SSH、TTS 等配置    |
| **IDENTITY.md**  |        我的身份设定——我的名字、风格、emoji         |
| **HEARTBEAT.md** | 定期检查任务——我定期该帮你检查什么（邮件、日历等） |
|  **MEMORY.md**   |         长期记忆——我需要永远记住的重要事情         |

