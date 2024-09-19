# 环境搭建
推荐使用 [Visual Studio Code](https://code.visualstudio.com/) 编辑器完成本次作业


!!! danger "测试环境和部署环境"

    注意，以下的环境是测试部分的环境，不是部署部分的环境。在测试部分的环境，我们使用的是 SQLite 数据库。在部署部分的环境中，我们需要使用 **MySQL** 数据库，因此在部署环境中，你需要修改 `app/settings_prod.py` 中的数据库配置，具体请参考 [清软论坛说明](../bbs.md) 部分

## SQLite 数据库

SQLite 是一个进程内的轻量级嵌入式数据库，**它的数据库就是一个文件**，实现了自给自足、无服务器、零配置的、事务性的 SQL 数据库引擎。它是一个零配置的数据库，这就体现出来 SQLite 与其他数据库的最大的区别：**SQLite 不需要在系统中配置，直接可以使用。**且 SQLite 不是一个独立的进程，可以按应用程序需求进行静态或动态连接。SQLite 可直接访问其存储文件。

## 安装 GNU Make
**GNU Make** 是一个构建自动化工具，它可以自动化执行一系列的命令，从而简化软件构建过程。在 C/C++ 项目中，Makefile 是一个非常重要的文件，它可以帮助我们编译、链接、运行程序。Makefile 的应用并不仅限于 C/C++ 项目，它适用于任何需要自动化命令执行的场景。在本次作业中，我们将使用 Makefile 来简化一系列操作，例如代码风格检查等任务的执行。

- **Windows 用户**：可以通过此[链接](https://gnuwin32.sourceforge.net/downlinks/make.php)下载并安装 GNU Make 安装包，安装完成后，将 `C:\Program Files (x86)\GnuWin32\bin` 路径添加到环境变量 `PATH` 中。
- **MacOS 用户**：可以使用 Homebrew 安装 GNU Make，命令如下：`brew install make`。
- **Linux 用户**：使用系统包管理器进行安装。例如，在 Debian/Ubuntu 系统中，可以通过以下命令安装：`sudo apt-get install make`。

安装完成后，可以通过执行 `make --version` 来验证安装是否成功，输出内容将类似如下：

```shell
GNU Make 4.2.1
Built for x86_64-pc-linux-gnu
Copyright (C) 1988-2016 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
```

该输出表示 GNU Make 已成功安装，并显示相关的版本和版权信息。

## 安装 Python 环境
推荐使用 [Anaconda](https://www.anaconda.com/products/individual) 或者 [Miniconda](https://docs.conda.io/en/latest/miniconda.html) 来管理 Python 环境，这两种工具都提供了一种高效的方式来处理 Python 环境和包的安装、更新和管理。当然你也可以使用 virtualenv 等其他工具来管理 Python 环境。如果遇到网络问题，可以选择使用[清华镜像源](https://mirrors.tuna.tsinghua.edu.cn/anaconda/)。

??? tip "关于虚拟环境"
    当使用 Python 开发软件时，一个基本的方法是在机器上安装 Python，通过终端安装所有的库，然后在终端中运行代码

    这对于简单的 Python 脚本项目来说效果很好，但是在复杂的软件开发项目中，比如构建一个 Python 库或者软件开发工具包，往往要处理多个文件、多个包和依赖关系，这可能会导致版本冲突，考虑下面的情况
    
    - 程序 A 依赖于库 X 的 1.0 版本
    - 程序 B 依赖于库 X 的 2.0 版本
    - 库 X 的 1.0 版本和 2.0 版本之间存在不兼容的 API，即程序 A 和程序 B 无法同时运行
    
    这是使用 Python 构建软件时经常遇到的问题，而虚拟环境可以帮助我们解决这个问题。它可以在不同的项目中使用不同的 Python 版本和依赖包，从而避免了**版本冲突**的问题，你可以将虚拟环境简单地理解为每一个项目都有一个**独立**的 Python 环境。

!!! warning "关于 Miniconda 版本"
    Miniconda 提供了针对不同平台的安装包，例如 `x86_64` 和 `arm` 架构。在[下载 Miniconda](https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/) 时，请注意选择适合操作系统和架构的版本。如果不确定，可以通过查看系统信息（如 `uname -m` 或 Windows 的“关于”页面）来确认 CPU 架构。


运行如下命令来创建虚拟环境并安装必要的依赖。
```shell
# 创建一个名为 SimpleBBS 的虚拟环境，Python 版本为 3.8
$ conda create -n SimpleBBS python=3.8
# 激活虚拟环境
$ conda activate SimpleBBS
# 使用 pip 安装依赖，在 backend 目录下执行
$ pip install -r requirements.txt
# 如果安装失败，可以改用清华镜像源
$ pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```
## 填充数据库
在 `manage.py` 文件中，我们已经实现了一个名为 `init_db` 的命令。这个命令的主要功能是使用测试数据来填充数据库。这种设计的目的是为了方便我们进行软件测试，因为在实际的软件开发过程中，我们经常需要在一个预设的环境中进行各种测试。
```shell
# 在 backend 目录下运行
$ python manage.py makemigrations app user post
$ python manage.py migrate
$ python manage.py init_db
```
填充的用户名为 `test_thss`，密码为 `test_thss`。

接下来创建一个超级用户，以便在后续的测试中使用。
```shell
# 在 backend 目录下运行
$ python manage.py createsuperuser
```
按提示输入用户名、邮箱和密码即可。

接下来就可以使用 `python manage.py runserver` 来运行 Django 服务了。
```shell
# 在 backend 目录下运行
$ python manage.py runserver

Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
August 26, 2023 - 20:16:09
Django version 4.1.4, using settings 'app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```
可以通过 `http://localhost:8000/admin` 来访问 Django 的后台管理系统，使用先前创建的超级用户的账户和密码即可登录，还可以通过访问 `localhost:8000/api/schema/swagger-ui/` 查看并使用项目的 Swagger UI，进行 API 文档的交互式浏览和测试。具体请参考 [清软论坛说明](../bbs.md)


## 前端
项目提供了基于 React 实现的前端，你可以在 `frontend` 目录中找到前端代码。在运行前端之前，你需要安装 `node.js` 和 `npm`。
运行下列命令来安装依赖并启动前端服务。
```shell
# 在 frontend 目录下运行
# 安装依赖
$ npm install --package-lock=package-lock.json
# 启动前端服务
$ npm start
```
访问 `http://localhost:3000` 即可看到前端页面。使用填充的测试数据登录。

!!! question "运行前端出现错误"
    在运行前端时如果出现如下错误，说明 Node 版本过低，不支持`--openssl-legacy-provider` 参数，需要升级到 18.0 以上
    ```shell
    node: --openssl-legacy-provider is not allowed in NODE_OPTIONS
    ```


## 配置浏览器Driver
端到端测试需要使用浏览器来模拟用户的行为，所以需要安装浏览器驱动，推荐使用 Chrome 浏览器和 ChromeDriver。

-  在浏览器地址栏中输入 `chrome://version/`，查看 Chrome 浏览器的版本号。
-  在`https://chromedriver.chromium.org/downloads` 下载兼容版本的 ChromeDriver，如果你的 Chrome 版本比较新，可以在 [这里](https://googlechromelabs.github.io/chrome-for-testing/) 下载对应版本的 ChromeDriver。
!!! tip "ChromeDriver 下载"

    如果以上方法均无法找到对应版本的 Driver，可以通过 [这里](https://googlechromelabs.github.io/chrome-for-testing/known-good-versions-with-downloads.json)  找到对应版本的 Driver 下载地址，格式为 `https://storage.googleapis.com/chrome-for-testing-public/<版本号>/<平台>/chromedriver-<平台>.zip`

    将 `<版本号>` 和 `<平台>` 替换为实际的版本号和平台，例如 Windows 平台的 `128.0.6613.137` 版本的 Driver 下载地址为 `https://storage.googleapis.com/chrome-for-testing-public/128.0.6613.137/win64/chromedriver-win64.zip`

    注意，并不是所有版本的 Chrome 都有对应的 ChromeDriver，找不到对应版本的 Driver 时，可以选择使用最接近的版本


-  解压后将其放在代码 `backend` 目录下的 `drivers` 目录中，`drivers` 目录中应当有 `chromedriver.exe` 或者 `chromedriver` 可执行文件。

-  修改 `tests/test_e2e.py` 中的 `DRIVER_PATH` 变量，将其指向 ChromeDriver 的实际路径。

你可以在 `backend` 目录中使用 `python driver.py` 来测试 ChromeDriver 是否配置正确。

!!! question "运行 `driver.py` 出现错误"
    如果运行 `driver.py` 出现类似于下面的错误
    ```shell
    ValueError: Timeout value connect was <object object at 0x00000225D858F0C0>, but it must be an int, float or None.
    ```
​    原因是 `urllib3` 版本和 `selenium` 版本不兼容，可以通过下面的命令降低 `urllib3` 的版本来解决这个问题。
    ```shell
    $ pip uninstall urllib3
    $ pip install urllib3==1.26.2
    ```

## 运行测试
在准备好上述环境后，就可以运行测试了。
```shell
# 在 backend 目录下运行
# 运行所有测试
$ python manage.py test
# 查看命令文档
$ python manage.py test --help
# 筛选运行测试
$ python manage.py test --filter test_basic
```
初次运行测试，部分测试可能会失败，这是因为我们还没有实现相应的功能。
```
test_login (test_api.APITestCase)
TODO: 使用错误的信息进行登录，检查返回值为失败 ... ok
test_logout (test_api.APITestCase)
TODO: 未登录直接登出 ... ok
test_register (test_api.APITestCase)
Example: 使用错误信息进行注册，检查返回值为失败 ... ok
test_register_params_check (test_basic.BasicTestCase) ... ok
test_web (test_e2e.SeleniumTestCase)
EXAMPLE: 使用测试用户进行登录 ... ok

Coverage Report:

Name                               Stmts   Miss Branch BrPart  Cover
--------------------------------------------------------------------
post\controllers.py                   98     86     18      0    10%
post\urls.py                           3      0      0      0   100%
post\views.py                         98     82     44      0    11%
user\controllers.py                   32     26      6      0    16%
user\urls.py                           3      0      0      0   100%
user\views.py                         62     49     18      0    16%
utils\jwt.py                          48     33     10      0    26%
utils\post_params_check.py             2      1      0      0    50%
utils\register_params_check.py         3      0      0      0   100%
utils\reply_post_params_check.py       2      1      0      0    50%
--------------------------------------------------------------------
TOTAL                                351    278     96      0    16%
```
至此，测试环境已经搭建完成，下面开始介绍本次作业的相关内容。
