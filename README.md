# Hello Git

```bash
# merge conflicts

# 测试，这是在master分支
# 测试在readme-re的提交!
# 测试，解决冲突
# 测试解决merge冲突
```



简单的常用指令

```bash
git status	# 查看当前文件状态
git branch	# 查看分支情况
git branch new-branch	# 创建新分支
git switch branch-name	# 切换到该分支
git switch -c new-branch	# 创建并切换到该分支
git add	filename	# 将文件提价到缓存区
git add -A		# 将所有更新的文件添加
git commit		# 将缓存区的文件提交到分支上
git commit -m "a commit message" # 同上，不过直接指定message内容为""内内容
```



## 在本地提交树上移动

### HEAD分离

HEAD一般是对当前分支的符号引用，指向你正在其基础上进行工作的提交记录。HEAD 总是指向当前分支上最近一次提交记录。大多数修改提交树的 Git 命令都是从改变 HEAD 的指向开始的。

HEAD 通常情况下是指向分支名的（如 bugFix）。在你提交时，改变了 bugFix 的状态，这一变化通过 HEAD 变得可见。

![image-20240829202002956](C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829202002956.png)

如果运行

```bash
git switch c1	# 将HEAD分离出来
```

![image-20240829202120442](C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829202120442.png)

### 相对引用

我们直接使用指定哈希值来移动，例如一次提交值为`fed2da64c0efc5293610bdd892f82a58e8cbc5d8`，可以使用git switch fed2来使HEAD指向此提交

不过，相对引用解决了这样指定的问题，可以使用`HEAD^`来表示向上移动一个提交记录，`HEAD~3`等来移动多个单位。

这个操作符可以反复在节点名称后，

```bash
git switch main^
```

<img src="C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829203228574.png" alt="image-20240829203228574" style="zoom: 50%;" />

<img src="C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829203315552.png" alt="image-20240829203315552" style="zoom: 67%;" />

### 撤销变更

git reset和git revert

git reset 通过把分支记录回退几个提交记录来实现撤销改动。你可以将这想象成“改写历史”。`git reset` 向上移动分支，原来指向的提交记录就跟从来没有提交过一样。

<img src="C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829203541352.png" alt="image-20240829203541352" style="zoom: 67%;" />

```bash
git reset HEAD^
```



![image-20240829203559153](C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829203559153.png)

git revert 虽然在你的本地分支中使用 `git reset` 很方便，但是这种“改写历史”的方法对大家一起使用的远程分支是无效的！为了撤销更改并**分享**给别人，我们需要使用 `git revert`。

<img src="C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829203701047.png" alt="image-20240829203701047" style="zoom: 80%;" />

```bash
git revert HEAD
```



![image-20240829203723440](C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829203723440.png)

可以看到多了一个新提交，这个提交就是用来撤销c2的更改的，此时c2‘的状态与c1相同。



## 远程提交

将一个本地仓库提交到远程，只需要

```bash
git remote add origin <remote-repository-url>
```

然后将自己的本地内容全部push到远程

```bash
git push -u origin main  # 将main推送到远程并设置跟踪分支
```

之后本地就多了一个远程分支，当切换到远程分支，自动进入分离HEAD状态，这么做是出于不能直接在这些分支上进行操作的原因, 你必须在别的地方完成你的工作, （更新了远程分支之后）再用远程分享你的工作成果。

开始前，需要确认自己的本地代码库是新的

```bash
git pull origin main  # 拉取远程main分支的最新更改
```

这一步会将远程仓库中 `main` 分支的最新更新拉取并合并到本地 `main` 分支中。

### 创建新分支

新功能或修复通常在新的分支上进行，以保持 `main` 分支的稳定性：

```bash
git checkout -b feature-branch  # 创建并切换到新分支
```

这里 `feature-branch` 是你为新功能或修复创建的分支名称。

### 开发和提交更改

在新分支上进行开发，并且在完成一定的工作后提交更改：

```bash
git add .  # 添加所有更改
git commit -m "Implement new feature X"  # 提交更改
```

### 推送分支到远程仓库

将你的分支推送到远程仓库，以便进行代码审查或与其他团队成员共享：

```bash
git push origin feature-branch
```

### 创建Pull Request (PR)

- 在远程仓库管理平台（如GitHub、GitLab、Bitbucket等）上，为你的 `feature-branch` 创建一个Pull Request (PR)。
- 代码审查：团队成员可以审查你的更改，提出建议或进行讨论。
- 合并：一旦PR通过审查并获得批准，它将被合并到 `main` 分支中。

### 同步本地仓库

合并PR后，需要将最新的 `main` 分支同步到你的本地仓库：

```bash
git checkout main
git pull origin main  # 拉取最新的main分支
```

###  删除本地和远程的临时分支

- 删除本地分支

  （如果不再需要）：

  ```bash
  git branch -d feature-branch
  ```
  
- 删除远程分支

  （通常由仓库管理员或通过平台操作完成）：

  ```bash
  git push origin --delete feature-branch
  ```

### 总结的工作流

1. **`git pull origin main`**：从远程仓库拉取最新的 `main` 分支的更新。
2. **`git checkout -b <branch-name>`**：为新功能或修复创建并切换到新分支。
3. **`git add` 和 `git commit`**：在新分支上开发并提交更改。
4. **`git push origin <branch-name>`**：将分支推送到远程仓库。
5. **Pull Request**：创建一个PR，等待代码审查并合并到 `main`。
6. **`git pull origin main`**：合并后，同步本地 `main` 分支。
7. **删除分支**：清理不再需要的分支。

同时，远程仓库命名规范为`<remote name>/<branch name>`，不过，当你使用git clone 后，git会帮你把远程仓库设置为origin



当我们切换到远程分支后，此时git commit不会更新o/main，只有在远程仓库对应分支更新后才会更新远程此远程分支。





## 从远程获取更新

git fetch 是直接从远程回去最新更新，而本地没有

<img src="C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829195631006.png" alt="image-20240829195631006" style="zoom:50%;" />

使用git fetch后会变成

<img src="C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829195729172.png" alt="image-20240829195729172" style="zoom:50%;" />

3. #### 如果远程分支是如图，而本地分支已经在此分支上提交了更新，此时需要git fetch获取更新后再使用git merge合并，这两条命令等同于git pull

<img src="C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829195837636.png" alt="image-20240829195837636" style="zoom:50%;" />

<img src="C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829200044101.png" alt="image-20240829200044101" style="zoom:50%;" />

## 将本地更新提交到远程

我们在本地做出了一些更新和提交，此时可以看到o/main分支与远程仓=仓库同步，而main分支仍处于main状态

<img src="C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829200529429.png" alt="image-20240829200529429" style="zoom:50%;" />

使用git push后，可以看到最新的更新被同步到了远程仓库的main分支，而本地的远程分支也更新到了与main分支同步

<img src="C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829200550163.png" alt="image-20240829200550163" style="zoom:50%;" />



当我们使用git push时，可能会遇到本地在c1的基础上提交了c3，而远程仓库已经有了c2，此时使用git push会失败。

![image-20240829200917716](C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829200917716.png)

如果我们使用

```bash
git fetch
git rebase --o/main
# 前两行等同于git pull --rebase
git push
```

后，先获取最新更新，然后将我们的提交rebase 到最新更新上，最后再git push就可以了。

同理git merge有同样的效果，不过具体的实现过程不同

```bash
git fetch
git merge o/main
# 前两行等同于git pull
git push
```



<img src="C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829200550163.png" alt="image-20240829200550163" style="zoom:50%;" />

<img src="C:\Users\BroMikey\AppData\Roaming\Typora\typora-user-images\image-20240829201325392.png" alt="image-20240829201325392"  />
