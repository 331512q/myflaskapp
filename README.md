# myflaskapp

头歌《开源代码管理与持续集成》第 5 章 实验 —— **第 1 关：实现 Jenkins 的 CI/CD 实践**

一个用于演示 Jenkins Pipeline 的极简 Flask 项目。

## 项目结构

```
myflaskapp/
├── app.py              # Flask 应用（首页返回 Hello, CI/CD with Jenkins!）
├── requirements.txt    # 依赖清单（锁定 Flask/Werkzeug 兼容版本）
├── .flake8             # 代码规范检查配置
├── tests/
│   └── test_app.py     # pytest 单元测试
├── Jenkinsfile         # Jenkins Pipeline 定义（代码化流水线）
└── README.md
```

## Jenkinsfile 的 8 个阶段

| 阶段 | 作用 |
| --- | --- |
| Verify Python Path | 校验 `PYBIN` 指向的 Python 可执行且版本正确 |
| Checkout | 从 GitHub 拉取本仓库 main 分支 |
| Fix pip | `ensurepip --upgrade`，修复 pip 环境 |
| Install Dependencies | `pip install -r requirements.txt` |
| Lint | `flake8 app.py tests/` 代码规范检查 |
| Test | `pytest --cov=app tests/ --cov-report=html`，并用 `publishHTML` 归档覆盖率报告 |
| Build | `PyInstaller --onefile app.py` 打包 |
| Deploy | 启动 Flask 应用 |

## 使用前需要改两处

1. `Jenkinsfile` 里 `environment.PYBIN` → 换成你本机 `python.exe` 的绝对路径（Windows 路径要用 `\\` 转义）。
2. `Jenkinsfile` 里 `git url` → 已指向本仓库。

## Jenkins 前置条件

- Java 17+、Jenkins（默认 8080）
- 插件：Git、Pipeline、ShinningPanda、Python、HTML Publisher、Email Extension
- 全局工具配置里新增 Python，名称 `Python3`，选 Install automatically
- 凭据：GitHub 用户名 + Personal Access Token（勾 `repo` 权限）
