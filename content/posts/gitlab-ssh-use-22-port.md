---
title: "Docker-Gitlab 与主机共用 ssh 的 22 端口"
categories: ["瞎折腾"]
tags: ["Docker","Gitlab","ssh"]
draft: false
slug: "docker-gitlab-ssh"
date: "2022-04-24 13:46:00"
---

## 背景

在使用 Docker 搭建 Gitlab/Gitee 会导致无法与主机端共用 22 端口，这导致 ssh 连接的时候会使用形如 `ssh://git@git.xxxx.cn:4022/zs/zsblog.git` 的 ssh 链接，而不是像官方 Gitlab 那种非常干净的 `git@git.xxxx.cn/zs/zsblog.git` 链接。这对于我这种强迫症而言非常的难受啊，但因为主机的 22 端口已经被占用了，无法共用，所以需要考虑两者共享端口的问题。

虽说是两者共用，但其实还是使用类似于端口转发的特点，简单说就是在主机设置 `git` 用户，然后通过一个脚本将 `git` 用户的所有 ssh 流量转发到 Gitlab 容器中，从而完成对应的事情。

关于 Gitee 的设置，Gitee 官方的 Docker 部署教程[^1]已经说的很清楚了，按照该步骤执行完全没问题。

而关于 Gitlab 貌似没有比较详尽的教程，搜索后发现了一个 Issue[^2] 以及一篇博文[^3]，后者讲的比较清楚，但是经过实践后发现存在一定问题，因此决定将可行的方案记录下来。

## 具体步骤

### 一、初始设置

在开始之前，`docker-compose.yml` 中设置比较关键的几个配置如下：

```bash
gitlab-web:
	image: 'gitlab/gitlab-ce:latest'
	container_name: 'gitlab'
	restart: always
	environment:
		GITLAB_OMNIBUS_CONFIG: |
			gitlab_rails['gitlab_shell_ssh_port'] = 4022
	ports:
		- '3090:80'
		- '4022:22'
		- '6060:6060'
	volumes:
		- '/srv/gitlab/config:/etc/gitlab'
		- '/srv/gitlab/logs:/var/log/gitlab'
		- '/srv/gitlab/data:/var/opt/gitlab'
		- .... #一些其他的配置
```

如上设置基本可以确保 Gitlab 形如 `ssh://git@git.xxxx.cn:4022/zs/zsblog.git` 的链接可以使用。

### 二、在 Host 宿主机创建与 Gitlab 相同的 git user

为了确保后续一些麻烦的权限问题，我们需要在宿主机也创建一个 `git` 用户。

首先我们要检查 Gitlab 容器中 `git` 用户的 UID 以及 GID，如果不出意外的话，两者都已被[硬编码为 998](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/d4f3f5d57b16dbf1e1a59f9a5f5cc041ddacf05a/docker/assets/setup) 。不过为了以防万一，我们可以通过下述指令进一步确认：

```bash
❯ docker exec -it gitlab cat /etc/passwd | awk -F':' '{if($1=="git") printf("uid: %s; gid: %s\n"), $3, $4}'
# 不出意外结果如下，证明 git 账户的 UID 与 GID 都为 998.
uid: 998; gid: 998
```

这条指令会给出我们对应的 UID 以及 GID。

接下来我们需要在宿主机中也创建 `git` 用户，确保 UID 及 GID 都与 Gitlab 容器内的一致。大家可以通过如下指令查看是否已有 `git` 用户，以及其 UID 与 GID。

```bash
❯ cat /etc/passwd | awk -F':' '{if($1=="git") printf("uid: %s; gid: %s\n"), $3, $4}'
```

+ 如果没有任何结果，说明没有 `git` 用户
+ 如果有结果，并且 UID 与 GID 不为 998，说明需要重新该一下对应的 UID 与 GID，大家可以自行搜索解决方案。
+ 我们还需要查看 `/etc/passwd` 与 `/etc/group` 两个文件确保 998 没有被使用，如果有使用也需要做对应改变。

如果没有 `git` 用户，则使用下面指令创建并指定家目录为 `/home/git` （在 root 用户下）

```bash
❯ groupadd -g 998 git
❯ useradd -m -u 998 -g git -s /bin/sh -d /home/git git
```

如果已有，则确保其 UID 与 GID 和 Gitlab 容器中 `git` 用户相同，并确保有家目录 `/home/git`。

### 三、复制 Gitlab 密钥文件

Gitlab 的密钥文件目前存放在容器的 `/var/opt/gitlab/.ssh` 中，然后根据你在 `docker-compose.yml` 配置的映射位置看，好比我配置的映射是 `/srv/gitlab/data:/var/opt/gitlab`，你就可以直接在 `/srv/gitlab/data/.ssh` 目录下找到对应的密钥文件。

然后首先在 `root` 用户权限下执行 

```bash
❯ cp -r /srv/gitlab/data/.ssh /home/git/.ssh
```

将对应的文件夹复制到 `git` 用户家目录下，然后执行

```bash
❯ chown -R git:git /home/git/.ssh
```

将所有的文件的权限及组权限都更改为 `git` 用户

现在 `git` 用户的 `.ssh` 目录结构应该如下，也要确保 `.ssh` 文件权限为 `700`，`authorized_keys` 权限为 600[^4]：

```bash
❯ ls -la
total 72
drwx------ 2 git git  4096 Apr 24 15:40 .
drwxr-xr-x 5 git git  4096 Apr 24 02:00 ..
-rw------- 1 git git 50122 Apr 24 02:00 authorized_keys
-rw-r--r-- 1 git git     0 Apr 24 01:57 authorized_keys.lock
-rw------- 1 git git  1679 Apr 24 01:48 id_rsa
-rw-r--r-- 1 git git   390 Apr 24 01:48 id_rsa.pub
-rw-r--r-- 1 git git   222 Apr 24 02:00 known_hosts
```

### 四、生成密钥文件

切换到 `git` 用户并生成密钥对

```bash
❯ su - git
❯ ssh-keygen #然后一路回车
```

然后将公钥加入 `authorized_keys` 

```shell
❯ cat ~/.ssh/id_rsa.pub > ~/.ssh/authorized_keys 
```

### 五、创建脚本文件

我们不妨看一下现在的 `authorized_keys` 文件，里面应该有形如下面的内容一些行

```bash
command="/opt/gitlab/embedded/service/gitlab-shell/bin/gitlab-shell key-105",no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty ssh-ed25519 xxxxxxxx
```

这里说明我们提交时会执行一个 `/opt/gitlab/embedded/service/gitlab-shell/bin/gitlab-shell` 脚本，这里本应该执行的是容器内的脚本，但是我们现在把它放在了容器外，因此我们需要设置一个脚本将请求转发进去，因此选择在本机的 `/opt/gitlab/embedded/service/gitlab-shell/bin` 目录下创建一个名为 `gitlab-shell` 的脚本，命令如下：

```bash
# 创建文件夹
❯ sudo mkdir -p /opt/gitlab/embedded/service/gitlab-shell/bin
# 进入文件夹
❯ cd /opt/gitlab/embedded/service/gitlab-shell/bin
# 创建文件
❯ sudo vim gitlab-shell
```

文件内容如下：

```shell
#!/bin/sh

ssh -i /home/git/.ssh/id_rsa -p 4022 -o StrictHostKeyChecking=no git@127.0.0.1 "SSH_ORIGINAL_COMMAND=\"$SSH_ORIGINAL_COMMAND\" $0 $@"
```

需要注意的是，这里的 `4022` 是指在 `docker-compose.yml` 文件映射的 `4022:22` 端口，如果你是 `xxx:22` ，则需要在这里填写 `xxx`，对应起来。内

然后不要忘记添加执行权限

```shell
❯ sudo chmod +x /opt/gitlab/embedded/service/gitlab-shell/bin/gitlab-shell
```

### 六、挂载额外的文件到 Gitlab 容器

修改我们的 `docker-compose.yml` ，注释掉 `gitlab_rails['gitlab_shell_ssh_port'] = 3022` 以及添加 `'/home/git/.ssh/:/var/opt/gitlab/.ssh'` ，将 `git` 用户的 `.ssh` 挂载到容器内。

```shell
gitlab-web:
	image: 'gitlab/gitlab-ce:latest'
	container_name: 'gitlab'
	restart: always
	environment:
		GITLAB_OMNIBUS_CONFIG: |
				....
	ports:
		- '3090:80'
		- '4022:22'
		- '6060:6060'
	volumes:
		- '/srv/gitlab/config:/etc/gitlab'
		- '/srv/gitlab/logs:/var/log/gitlab'
		- '/srv/gitlab/data:/var/opt/gitlab'
		- '/home/git/.ssh/:/var/opt/gitlab/.ssh'
		- .... #一些其他的配置
```

## 简单的原理说明

使用的原理就是将主机 `git` 用户的所有 ssh 流量都转发到容器内部。

转发使用的是宿主机中的`/opt/gitlab/embedded/service/gitlab-shell/bin/gitlab-shell` 脚本，而因为已经将主机 `git` 用户 `id_rsa.pub` 添加到了 `authorized_keys` 中，因此可以直接免密转发内容到容器内部。

随后，容器内的 `gitlab-shell` 脚本对请求进行处理，完成 ssh 请求。

这里很巧妙的使用了同一个位置的脚本，这也是需要将 `git` 用户的 `.ssh` 目录挂载到 Gitlab 内部的原因，但是每个脚本却作用不同，十分的巧妙。

[^1]: [使用 Docker 安装](https://docs.gitea.io/zh-cn/install-with-docker/) ↩
[^2]: [Sharing SSH port between host and the container](https://github.com/sameersbn/docker-gitlab/issues/1517)
[^3]: [Exposing ssh port in dockerized gitlab-ce](https://blog.xiaket.org/2017/exposing.ssh.port.in.dockerized.gitlab-ce.html)
[^4]: [公钥添加到authorized_keys到文件中之后仍无法免密登陆](https://blog.csdn.net/kangkanglou/article/details/90760529)
