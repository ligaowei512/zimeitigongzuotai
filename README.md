自媒体发稿工作台 · 代码包

本包汇总了"飞书多维表格 + Python 发布执行器"方案涉及的全部代码与配置，按用途分类，便于下载、备份与部署。

一、目录结构
自媒体发稿工作台-代码包/
├─ 01_发布执行器/      ← 核心运行代码
│   └─ publish_executor.py     （飞书读表 → 三平台API发布 → 回写状态；Webhook + 定时双模式）
├─ 02_依赖与配置/      ← 运行所需依赖与密钥模板
│   ├─ requirements.txt       （flask / requests / python-dotenv）
│   └─ .env.example           （所有密钥占位，复制为 .env 后填真实值）
├─ 03_技能说明/        ← 方案总说明（Skill 文档）
│   └─ SKILL.md               （可行性边界 / 架构 / 字段设计 / 写稿提示词 / 分阶段路线 / 安全规范）
└─ README.md           ← 本文件

二、各文件作用



文件
作用



publish_executor.py
发布执行器主体：飞书多维表格读写（tenant_access_token 自动刷新 + 分页）、百家号/网易号/头条号官方 API 发布、指数退避重试、飞书群机器人失败告警、/publish 与 /health Webhook 入口、--serve/--once 双模式


requirements.txt
Python 依赖清单，部署前 pip install -r requirements.txt


.env.example
密钥与字段名映射模板；复制为 .env 后逐项填值，密钥只放这里，绝不硬编码


SKILL.md
整套工作台的设计说明：可行性边界、整体架构、飞书字段设计、写稿提示词、分阶段落地路线、安全纪律、核查清单


三、能真·自动发布的平台（v1）

百家号：官方内容 API（app_id + app_token）
网易号：网易号媒体开放平台（access_token + 签名）
头条号：字节/头条开放平台 OAuth2（access_token 仅 2 小时，生产需加刷新）


其余平台（知乎/搜狐/一点/大鱼/企鹅/博客园/东方财富/同顺等）无官方发布接口，v1 不自动发，只把"需人工发布"写回回执。若要走全自动，需依赖 Cookie/RPA 或第三方聚合工具（有封号/违规风险，工具方免责）。

四、最简部署步骤
# 1. 安装依赖
pip install -r 02_依赖与配置/requirements.txt

# 2. 配置密钥
cp 02_依赖与配置/.env.example .env   # 然后逐项填好真实值

# 3. 启动
python 01_发布执行器/publish_executor.py --serve   # Webhook 常驻（Flask）
# 或定时跑一次（可用云函数定时器 / cron 触发）
python 01_发布执行器/publish_executor.py --once

飞书侧：多维表格自动化「当 审核状态 = 已通过 → 发送 Webhook → 你的服务地址 + /publish」。
五、重要提醒（务必看）

密钥安全：所有 AppID / Secret / Token 只放 .env 或云函数环境变量，绝不写进代码、绝不提交到仓库。
人工确认：对外发送属高风险操作，默认"审核状态=已通过"才发布；跑稳前不要关闭该开关。
头条号 token：access_token 仅 2 小时有效，正式运行务必补上刷新逻辑（见 publish_executor.py 内 get_toutiao_token 的 TODO）。
无接口平台风险：自动发无接口平台 = Cookie 模拟登录，平台视为违规，可能降权/封号。

六、关于本包的说明

本代码包由工作台搭建会话中保留的 Skill 目录（self-media-publish-workbench）恢复整理而来。
原工作区曾因环境重置被清空，其中两份 HTML 文档（《自媒体自动发稿工作台_纯点击搭建指南》《国内自媒体平台目录_个人注册与发布接口》）属于图文说明，不在"代码"范畴，未在包内；如需要可单独重新生成。
平台接口、开放政策变动频繁，接入前请以各平台最新官方文档为准。

