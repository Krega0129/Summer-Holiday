### Git

1. git结构：

   1. 工作区：即电脑能看到的根目录，如mygit文件夹（写代码、修改等）；
   2. 暂存区：临时存储（未提交）；
   3. 本地库：历史版本；

   工作区—`$git add`—>暂存区—`$git commit`—>本地库；

2. 本地库和远程库：

   1. 团队内部协作：

      ![](http://assets.processon.com/chart_image/5f1332a0f346fb2bfb2e0603.png)

   2. 跨团队操作：

      ![](http://assets.processon.com/chart_image/5f133535f346fb2bfb2e073f.png)

   3. git命令

      1. **`$mkdir`**：新建文件夹；

      2. **`$cd + 路径`**：进入该文件夹；

      3. **`$pwd`**：显示当前目录；

      4. **`$git init`**：把目录变为git可以管理得仓库（初始化仓库）；

      5. **`$ls-ah`**：显示隐藏目录；

      6. **`$git add [fileName]`** ：将文件添加到仓库；

      7. **`$git commit -m"......"`**：把文件提交到仓库，其中双引号里面填备注，最好填改动记录；

      8. **`$vi [fileame]`**：打开仓库文件；

      9. **`$git status`**：查看结果，可以时刻掌握仓库当前状态；

      10. **`$git diff`**：查看不同（修改了哪里），比较暂存区和仓库，了解修改内容安全后，提交修改。

      11. **`$git log`**：查看历史记录（提交过的版本），最近=>最远排列，只显示3个；若不想显示详细信息，可以用：**`$git log --petty=oneline`**

      12. **`$git reset`**： 在git中，**`HEAD`**表示当前版本，**`HEAD^`**表示上一个版本，**`HEAD^^`**表示上上一个版本。也可以用 **`HEAD~10`**表示上10个版本；当我们想要退回以前的版本，可以用 **`$git reset`**命令：

          例：**`$git reset --hard HEAD^`** 为退回上一个版本。退回后，若想回到之前的新版本，只能通过 **`$git reset --hard 版本号`** （commit id）找回，版本号为长串十六进制数，可不写全，写前几个即可。

          找版本号的方法：

          1. 若未关闭命令行窗口，可以在命令行的输出记录找；
          2. **`$git reflog`**：记录了每一次命令

          【注】Git内部有一个指向当前版本的HEAD指针，当退版本时，仅仅是移动指针。

      13. **`$cat [fileName]`**：可以看文件内容；

      14. **`$git restore [fileName]`**：可以撤销操作：

          1. 若文件**未**add到暂存区，则返回与版本库中一样；
          2. 若文件已add到暂存区，再做修改，则返回到添加到暂存区时的状态；

          即返回到最近一次使用 **`$git add`**或 **`$git commit`**时的状态。

          若已add到暂存区的方法：

          1. **`$git restore --stage [fileName]`**将文件退回工作区，再用 **`$git restore [fileName]`**撤销操作；
          2. 也可以用 **`$git reset Head [fileName]`**将文件退回工作区，`HEAD`表示最新版本。
          3. 若文件以提交到版本库，但未推送至远程库，可以用 **`$git reset --hard commit_id`**即返回到想要的版本库再撤销。

      15. 删除文件：

          1. **`$rm [fileName]`**：此时用 **`$git status`**会看到提示，再用 **`$git rm [fileName]`**删除再提交。
          2. 可先手动删除文件，再使用 **`$git rm [fileName]`** 和**`$git add [fileName]`**效果一样。

          【注】若删除错了，可用版本库的版本替代工作区的版本，**若从未被添加到版本库就被删除的文件，无法修复**。

          ​		**`$rm`**与 **`$git rm`**的区别：

          ​			**`$rm`**：仅删除物理文件，没有将其从git记录中剔除。

          ​			**`$git rm`**：删除文件，同时还会将这个删除操作记录下来。

          3. 删除远程文件：`$git rm` 本地文件 => `commit` => `push`

   4. vi & vim 有两种工作模式：

      1.  模式：
         1. 命令模式：接受执行 vi & vim 操作命令的模式，打开文件后的默认模式。
         2. 编辑模式：对打开的文件进行增、删、改操作的模式，在此模式下按`esc`返回命令模式。
      2.  编辑：
         1. 用 vi + 文件名（或文件路径）打开文件，若存在该文件则打开，不存在则新建，并在终端最后一行显示该文件为新文件。
         2. 键盘输入 `i` 或 `insert` 进入编辑模式。
      3. 保存文件：
         1. 在编辑模式下编辑
         2. 按下 `esc` 退出编辑模式，，切换到命令模式。
         3. 在命令模式下输入 `ZZ` 或 `:wq` 保存修改并退出 vi。
         4. 若只想保存文件，则输入 `:w` 回车后会提示写入操作结果，并保持停留在命令模式。
      4. 放弃所有文件修改：
         1. 按 `esc` 进入命令模式，输入 `:q!` 回车后放弃修改并退出 vi。
         2. 放弃所有更改但不退出vi（即回到最后一次保存操作的状态，继续进行文件操作）：按 `esc` 进入命令模式，输入 `:e!` 回车后回到命令模式。

   5. 版本控制系统只能追踪文本文件的改动，不能跟踪二进制文件的改动；

   6. 我们要把要放进仓库的文件放在该目录下（或子目录下）；

   7. 版本库：工作区中有一个隐藏目录 .git，这个是版本库，不属于工作区。Git的版本库里面存了很多东西，最重要的是stage（或交index）的暂存区，还有Git为我们自动创建的第一个分支master及指向master的一个指针HEAD。

      ![](https://www.liaoxuefeng.com/files/attachments/919020037470528/0)

      可add多个文件到stage再一次性提交到master上。

   8. 添加远程库：

      1. 关联远程库：使用命令

         `$git remote add origin git@server-name:path/repo-name.git`

      如：`$git remote add origin git@github.com:Krega0129/Summer-Holiday.git`

      2. 关联后第一次推送所有内容用：

         `$git push -u origin master `（比平时多了一个`-u`）

         `-u`将本地master分支与远程master分支关联起来，以后的推送可以写成：

         `$git push origin master`

   9. 克隆远程库（到本地）：

      `$git clone git@server-name:path/repo-name.git`

   10. 分支管理：

       1. 创建并合并分支：HEAD指向当前分支，创建新分支时，相当于创建了一个新指针：

          `$git branch 分支名`

       2. 切换到当前分支：

          `$git switch 分支名`或`$git checkout 分支名`

       3. 创建并切换到新分支：

          `$git switch -c 分支名`或`$git checkout -b 分支名`

       4. 查看分支：列出所有分支，当前分支带*号：`git branch`

       5. 将分支a的工作成果合并到master上（在master分支上写）：

          `$git merga a`

          【注】Fast-Forward意思为“快进模式”，即把master指向a的当前提交。

       6. 删除分支：`$git branch -d 分支名`

   11. 解决冲突：Git无法自动合并分支时，就必须先解决冲突，后再提交，即把Git合并失败的文件手动编辑为我们希望的内容，再提交。用`$git log --graph`命令可以看到分支合并图。

   12. 分支管理策略：用Fast Forward模式合并分支时，删除分支后，会丢掉分支信息，若强制金庸FF模式，则Git再merge时会生成一个新的commit，这样就可以在历史上看出分支信息：

       `$git merge --no-ff -m"" 分支名`由于本次merge要创建一个新的commit，所以要加上`-m""`，用`$git log --graph --pretty=oneline --abbrev-commit`看分支信息。

       【注】一般master上不用来工作，只用来发布版本，工作一般在其他分支上。

       1. Bug分支：当你在分支dev中工作，发现bug，但dev的工作还没完成，不能提交，此时需要把dev中储存到另一个地方，用`$git stash`把工作区空出来，修复bug一般在分支上进行，若在master上建立bug分支，在bug分支上修复完commit后，到master上合并并删除bug分支。然后返回dev分支，查看刚才的工作现场：

          恢复方法：

          1. 用`$git stash apply`恢复，但stash的内容不删除，要用`$git stash drop`删除。

          2. 用`$git stash pop`恢复的同时删除stash内容，之后用`$git stash list`看不到内容。

             将bug分支上所作的修改复制到dev中，可以用`$git cherry-pick bug分支上的commit_id`：让我们能复制一个特定的提交到当前分支。

             【注】可以多次使用stash，恢复前用`$git stash list`查看，恢复指定的stash。

       2. Feature分支：添加新功能时，最好建一个Feature分支，在上面完成后，合并，删除。如功能为a的分支，可以命名为feature-a，若该分支 **未**合并，但要删除，要用

          `$git branch -D 分支名`。

   13. 多人协作：

       1. 查看远程库的信息：`$git remote`；若要查看更详细的信息：`$git remote -v`：显示了可抓取/推送的origin地址，若无推送的权限，则无法看到push地址。

       2. 推送分支：把该分支上所有本地提交推送到远程库，推送时指定本地分支，这样git就能推送到远程对应的分支上：`$git push origin 分支名`

       3. 抓取分支：多人协作时，其他人clone我的后，此时只能看到master，若要早dev上开发，则需要创建一个（本地新建分支不推送对其他人不可见）

       4. 多人协作的工作模式：

          1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
          2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
          3. 如果合并有冲突，则解决冲突，并在本地提交；
          4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

          如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to=origin/<branch-name> <branch-name> `。

   14. 标签管理：Git标签是版本库的快照，本质上是指向commit的指针，但不能移动，与分支相比，好处就是不用记commit_id，只需要知道tag名。

   15. 创建标签（默认为HEAD）：切换到需要打标签的分支上，`$git tag <tagname>`可以新建一个新标签，还可以加上备注信息`$git tag -a <tagname> -m ""`默认标签是打在最新提交的commit上，也指定`$git tag <tagname> commit_id`，可以用命令`$git tag`查看所有标签，标签顺序按字母排，查看标签信息（包括备注）用`$git show <tagname>`。

   16. 操作标签：

       1. 删除标签：`$git tag -d <tagname>`（未推送到远程），若要删除已推送的标签，现在本地删除，在从远程删除：`$git push origin :ref/tags/<tagname>`
       2. 要推送标签到远程：`$git push origin <tagname>`，或一次性将所有未推送的标签推送至远程`$git push origin --tags`。