# BUAA OS 课程仓库本地连接教程

本文档用于记录如何在本地通过 SSH 连接 BUAA OS 课程 Git 仓库，并拉取代码。

---

## 1. 创建 SSH Key

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

## 2. 上传 SSH 公钥

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

## 3. 配置 SSH

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

## 4. 测试 SSH 连接

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

## 5. 初始化本地 Git 仓库

进入你想存放 OS 课程代码的目录，例如：

```bash
cd path/to/your/folder
```

初始化 Git 仓库：

```bash
git init
```

---

## 6. 添加远程仓库地址

添加课程仓库作为远程仓库：

```bash
git remote add origin git@git.os.buaa.edu.cn:os2026/74xxxxxx.git
```

其中 `74xxxxxx` 替换成你自己的学号。

如果之前已经添加过 `origin`，可以使用下面的命令修改地址：

```bash
git remote set-url origin git@git.os.buaa.edu.cn:os2026/74xxxxxx.git
```

查看当前远程仓库地址：

```bash
git remote -v
```

---

## 7. 拉取仓库代码并切换实验分支

先拉取远程仓库代码：

```bash
git pull
```

拉取完成后，可以切换到对应的实验分支，例如 `lab0`、`lab1`、`lab2` 等：

```bash
git checkout labx
```

其中 `labx` 替换成你需要的实验分支名，例如：

```bash
git checkout lab0
```

---

## 8. 常见问题

### 8.1 提示 Permission denied

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

### 8.2 不确定当前 remote 地址

执行：

```bash
git remote -v
```

如果显示的是 HTTPS 地址，可以改成 SSH 地址：

```bash
git remote set-url origin git@git.os.buaa.edu.cn:os2026/74xxxxxx.git
```

---

### 8.3 Windows 下 config 文件变成了 config.txt

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

## 9. 完整命令示例

第一次配置时的大致流程如下：

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
ssh -T git@git.os.buaa.edu.cn

git init
git remote add origin git@git.os.buaa.edu.cn:os2026/74xxxxxx.git
git pull
```

如果 `git pull` 提示需要指定分支：

```bash
git pull origin main
```

或者：

```bash
git pull origin master
```
