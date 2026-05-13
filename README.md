# BUAA OS 本地环境连接教程

本文档用于记录如何在本地连接 BUAA OS 课程相关环境，包括本地 SSH 连接 OS 服务器，以及通过 SSH 连接课程 Git 仓库并拉取代码。

---

## 一、本地 SSH 连接 OS 服务器

课程默认使用 `lab.os.buaa.edu.cn` 网站中的 JumpServer 连接 OS 服务器，需要通过浏览器进入网页终端。

如果想更方便地在本地终端或 VSCode 中直接连接 OS 服务器，可以按照下面的方式配置本地 SSH。

### 1. 配置 JumpServer SSH 密钥

进入：

```text
lab.os.buaa.edu.cn
```

登录后，点击右上角个人信息，可以选择下面两种方式之一配置 SSH 密钥。

#### 方式一：重置并下载 SSH 密钥

在个人信息页面中选择重置并下载 SSH 密钥。

下载得到的密钥文件就是之后本地 SSH 连接 OS 服务器时使用的私钥。建议把它保存到本地 `.ssh` 目录下，例如：

```text
C:\Users\你的用户名\.ssh\os_lab_key
```

后面配置 `IdentityFile` 时，就填写这个私钥文件路径。

#### 方式二：自行创建 SSH Key 并更新 SSH 公钥

也可以自己在本地创建 SSH key：

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

然后复制 `.pub` 结尾的公钥内容。

回到 `lab.os.buaa.edu.cn`，点击右上角个人信息，选择更新 SSH 公钥，把刚才复制的公钥内容粘贴进去并保存。

注意：上传的是 `.pub` 结尾的公钥内容，本地连接时使用的是不带 `.pub` 后缀的私钥文件。

---

### 2. 本地 SSH 连接参数

连接 OS 服务器需要使用下面的参数：

```text
HostName: lab.os.buaa.edu.cn
User: 74xxxxxx@git@10.134.170.171
Port: 2222
IdentityFile: ~/.ssh/your_ssh_key
```

其中：

- `74xxxxxx` 替换成你自己的学号
- `~/.ssh/your_ssh_key` 替换成你的 SSH 私钥路径

可以直接使用下面的命令连接：

```bash
ssh -p 2222 -i ~/.ssh/your_ssh_key -l "74xxxxxx@git@10.134.170.171" lab.os.buaa.edu.cn
```

也可以在本地 SSH 配置文件中添加连接配置：

```text
C:\Users\你的用户名\.ssh\config
```

添加内容如下：

```sshconfig
Host os-lab
    HostName lab.os.buaa.edu.cn
    User 74xxxxxx@git@10.134.170.171
    Port 2222
    IdentityFile ~/.ssh/your_ssh_key
    IdentitiesOnly yes
```

之后就可以直接使用下面的命令连接：

```bash
ssh os-lab
```

---

## 二、本地连接 OS 课程 Git 仓库

这一部分用于配置本地 Git，通过 SSH 连接 `git.os.buaa.edu.cn` 上的 OS 课程 Git 仓库，并拉取实验代码。

### 1. 创建 SSH Key

首先在本地生成一个 SSH key。

打开终端，执行：

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

如果系统不支持 `ed25519`，也可以使用：

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

生成时可以指定文件名，例如：

```text
C:\Users\你的用户名\.ssh\os_git_key
```

如果一路回车，默认会生成在：

```text
C:\Users\你的用户名\.ssh\
```

常见文件包括：

```text
id_ed25519
id_ed25519.pub
```

其中：

- `id_ed25519` 是私钥
- `id_ed25519.pub` 是公钥

---

### 2. 上传 SSH 公钥

打开公钥文件：

```bash
cat ~/.ssh/id_ed25519.pub
```

如果你使用的是 Windows，也可以直接打开：

```text
C:\Users\你的用户名\.ssh\id_ed25519.pub
```

复制里面的全部内容。

然后进入：

```text
git.os.buaa.edu.cn
```

登录后，按照下面的路径进入 SSH Keys 页面：

```text
右上角头像 → Preferences → 左侧菜单 SSH Keys
```

把刚才复制的公钥内容添加进去。

注意：上传的是 `.pub` 结尾的公钥文件内容，不是私钥。

---

### 3. 配置 SSH

在本地打开或创建 SSH 配置文件：

```text
C:\Users\你的用户名\.ssh\config
```

如果没有 `config` 文件，可以手动新建一个。注意文件名就是 `config`，不要带 `.txt` 后缀。

在里面添加以下内容：

```sshconfig
Host git.os.buaa.edu.cn
    HostName git.os.buaa.edu.cn
    User git
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes
```

如果你生成 SSH key 时使用了自定义文件名，例如 `os_git_key`，则应改成：

```sshconfig
Host git.os.buaa.edu.cn
    HostName git.os.buaa.edu.cn
    User git
    IdentityFile ~/.ssh/os_git_key
    IdentitiesOnly yes
```

---

### 4. 测试 SSH 连接

执行：

```bash
ssh -T git@git.os.buaa.edu.cn
```

第一次连接时，可能会提示是否信任该主机，输入：

```bash
yes
```

如果 SSH key 配置正确，通常不会再要求输入 Git 网站密码。

如果仍然需要输入密码，可以使用下面的命令查看 SSH 调试信息：

```bash
ssh -vT git@git.os.buaa.edu.cn
```

重点检查输出中是否使用了你配置的私钥文件。

---

### 5. 克隆课程仓库代码

使用下面的命令克隆课程仓库，并指定需要的实验分支：

```bash
git clone -b labx git@git.os.buaa.edu.cn:os2026/74xxxxxx.git
```

其中：

- `74xxxxxx` 替换成你自己的学号
- `labx` 替换成你需要的实验分支名，例如 `lab0`、`lab1`、`lab2` 等

例如克隆 `lab0` 分支：

```bash
git clone -b lab0 git@git.os.buaa.edu.cn:os2026/74xxxxxx.git
```

克隆完成后，进入仓库目录：

```bash
cd 74xxxxxx
```

如果已经克隆了仓库，也可以之后再切换到对应实验分支：

```bash
git checkout labx
```

例如：

```bash
git checkout lab0
```

---

### 6. 常见问题

#### 6.1 提示 Permission denied

可以检查以下几点：

- SSH 公钥是否已经上传到 `git.os.buaa.edu.cn`
- `~/.ssh/config` 中的 `IdentityFile` 是否写对
- 使用的是不是 SSH 地址，而不是 HTTPS 地址
- 私钥文件是否存在

可以用下面命令查看 SSH 连接过程：

```bash
ssh -vT git@git.os.buaa.edu.cn
```

---

#### 6.2 提示找不到分支

如果 `git clone -b labx` 提示找不到分支，先确认 `labx` 是否已经替换成真实分支名，例如 `lab0`、`lab1` 等。

也可以先克隆仓库：

```bash
git clone git@git.os.buaa.edu.cn:os2026/74xxxxxx.git
```

进入仓库后查看远程分支：

```bash
git branch -a
```

然后切换到对应分支：

```bash
git checkout labx
```

---

#### 6.3 不确定当前 remote 地址

执行：

```bash
git remote -v
```

如果显示的是 HTTPS 地址，可以改成 SSH 地址：

```bash
git remote set-url origin git@git.os.buaa.edu.cn:os2026/74xxxxxx.git
```

---

#### 6.4 Windows 下 config 文件变成了 config.txt

Windows 可能会自动给文件加 `.txt` 后缀。

请确认文件路径是：

```text
C:\Users\你的用户名\.ssh\config
```

而不是：

```text
C:\Users\你的用户名\.ssh\config.txt
```

---

### 7. 完整命令示例

第一次配置时的大致流程如下：

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
ssh -T git@git.os.buaa.edu.cn

git clone -b labx git@git.os.buaa.edu.cn:os2026/74xxxxxx.git
cd 74xxxxxx
```

其中 `labx` 替换成你需要的实验分支名，例如：

```bash
git clone -b lab0 git@git.os.buaa.edu.cn:os2026/74xxxxxx.git
```
