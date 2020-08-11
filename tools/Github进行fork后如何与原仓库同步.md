Github进行fork后如何与原仓库同步

Github在fork之后，代码不会同步更新。通过以下方式更新。

1. 同步最新代码

2. 查看tag，发现tag并未更新

   ```
   xfei-mac:modules zengxiangfei$ git tag
   5.19.0.2
   5.20.0
   7-201710-EA
   7-201711-EA
   7-201712-EA
   7-201801-EA
   7-201802-EA
   7.0.0.Beta1
   activiti-5.0
   activiti-5.0.alpha1
   ```

   

3. 查看远程push和fetch地址

   ```
   xfei-mac:modules zengxiangfei$ git remote -v
   origin  https://github.com/xiangfeiZENG/Activiti.git (fetch)
   origin  https://github.com/xiangfeiZENG/Activiti.git (push)
   ```

   

4. 以上origin的fetch和push只有个人的地址，当fork一个项目时，通过以下方式指定源地址让你的项目保持同步。

   ```
   git pull git@github.com:Activiti/Activiti.git
   
   xfei-mac:modules zengxiangfei$ git remote -v
   origin  https://github.com/xiangfeiZENG/Activiti.git (fetch)
   origin  https://github.com/xiangfeiZENG/Activiti.git (push)
   upstream        git@github.com:Activiti/Activiti.git (fetch)
   upstream        git@github.com:Activiti/Activiti.git (push)
   ```

   

5. 更新branch和tag

   ```
   xfei-mac:modules zengxiangfei$ git fetch upstream
   ```





实在是……有太多人同时在帮忙修订错别字或优化 xiaolai 的 `the-craft-of-selfteaching` 了。如果你提交的 pull request 未被接受且得到回复说：“重新fork”，其实是你遇到一个问题：在你 fork 之后， xiaolai 的仓库又更新了；但 github 不会自动帮你把 xiaolai 的仓库 同步给你 fork 后的仓库；导致你提交 pull request 时的版本和 xiaolai 的版本不一致。这个问题，用显得更“专业点”的说法，叫做：`Github进行fork后如何与原仓库同步`。那到底怎么做呢？最省事的办法可能是：在你fork的仓库setting页翻到最下方，然后delete这个仓库；然后重新fork xiaolai 的仓库，并 git clone 到你的本地。有时候，你需要用到这个省事的办法，比如 xiaolai 的仓库再次整理了 commit 。但在更多情况下，删掉自己fork的库，应该是你的最后选择，而不应该是首选。和很多人一起向 xiaolai 提交 pull request，这实在是一个反复练习 `merge` （中文说法：合并，或版本合并）的机会。毫不夸张地讲，版本管理是软件工程极其重要的规范，也是极其基础的必备技能。而 `merge` 则是版本管理中最必须也最常用的场景。那要不然，就多练练？以下是傻瓜版操作步骤，还细心配了截图，保管你从 0 也能上手。至于原理嘛，慢慢再搞懂吧。merge前的设定step 1、进入到本地仓库的目录。下面所有操作，如无特别说明，都是在你的本地仓库的目录下操作。比如我的本地仓库为`/from-liujuanjuan-the-craft-of-selfteaching`[![image](https://user-images.githubusercontent.com/31027645/54422899-6938e880-474a-11e9-8768-27ac24673e28.png)](https://user-images.githubusercontent.com/31027645/54422899-6938e880-474a-11e9-8768-27ac24673e28.png)step 2、执行命令 `git remote -v` 查看你的远程仓库的路径：[![image](https://user-images.githubusercontent.com/31027645/54422975-95ed0000-474a-11e9-96bf-1018d6bc06f2.png)](https://user-images.githubusercontent.com/31027645/54422975-95ed0000-474a-11e9-96bf-1018d6bc06f2.png)如果只有上面2行，说明你未设置 `upstream` （中文叫：上游代码库）。一般情况下，设置好一次 `upstream` 后就无需重复设置。step 3、执行命令 `git remote add upstream https://github.com/selfteaching/the-craft-of-selfteaching.git` 把 xiaolai 的仓库设置为你的 `upstream` 。这个命令执行后，没有任何返回信息；所以再次执行命令 `git remote -v` 检查是否成功。[![image](https://user-images.githubusercontent.com/31027645/54423107-d8aed800-474a-11e9-9ab8-7bb901181283.png)](https://user-images.githubusercontent.com/31027645/54423107-d8aed800-474a-11e9-9ab8-7bb901181283.png)step 4、执行命令 `git status` 检查本地是否有未提交的修改。如果有，则把你本地的有效修改，先从本地仓库推送到你的github仓库。最后再执行一次 `git status` 检查本地已无未提交的修改。`git add -A` 或者 `git add filename` `git commit -m "your note"` `git push origin master` `git status`注1：这一步作为新手，建议严格执行，是为了避免大量无效修改或文本冲突带来的更复杂局面。注2：如果你已经在fork后的仓库提交了大量对 xiaolai 的仓库并没有价值的修改，那么想要pull request，还是重新回到本文最初的“最省事办法”吧。merge 的关键命令以下操作紧接着上面的步骤。step 5、执行命令 `git fetch upstream` 抓取 xiaolai 原仓库的更新：[![image](https://user-images.githubusercontent.com/31027645/54448734-60b2d300-4787-11e9-9fdf-90fcc2e66052.png)](https://user-images.githubusercontent.com/31027645/54448734-60b2d300-4787-11e9-9fdf-90fcc2e66052.png)step 6、执行命令 `git checkout master` 切换到 master 分支：[![image](https://user-images.githubusercontent.com/31027645/54448759-6dcfc200-4787-11e9-8bbc-a5beef23ea88.png)](https://user-images.githubusercontent.com/31027645/54448759-6dcfc200-4787-11e9-8bbc-a5beef23ea88.png)step 7、执行命令 `git merge upstream/master` 合并远程的master分支：[![image](https://user-images.githubusercontent.com/31027645/54449526-47128b00-4789-11e9-9add-09217eb91a68.png)](https://user-images.githubusercontent.com/31027645/54449526-47128b00-4789-11e9-9add-09217eb91a68.png)step 8、执行命令 `git push `把本地仓库向github仓库（你fork到自己名下的仓库）推送修改如果担心自己不小心改了哪里，可以再次执行命令 `git status` 检查哪些文件有变化。这个操作仅是检查，不会改变任何状态，放心用。[![image](https://user-images.githubusercontent.com/31027645/54449665-a07aba00-4789-11e9-9181-bdcc814fffe6.png)](https://user-images.githubusercontent.com/31027645/54449665-a07aba00-4789-11e9-9181-bdcc814fffe6.png)现在你已经解决了fork的仓库和原仓库版本不一致的问题。可以放心向 xiaolai 发起 pull request 了。如果以上操作你花了不少时间，而 xiaolai 的仓库 又恰好更新了。很好，一次新的练习机会来了……

👍 17😄 1🎉 1❤️ 4

<details class="details-overlay details-reset position-relative float-left d-inline-block reaction-popover-container js-reaction-popover-container" style="box-sizing: border-box; display: inline-block !important; position: relative !important; float: left !important; z-index: 100;"><summary class="btn-link reaction-summary-item add-reaction-btn" aria-label="Add your reaction" aria-haspopup="menu" style="box-sizing: border-box; display: inline-block; cursor: pointer; -webkit-appearance: none; background-color: transparent; border: 0px; color: rgb(3, 102, 214); font-size: inherit; padding: 9px 15px 7px; text-decoration: none; user-select: none; white-space: nowrap; opacity: 1; transition: opacity 0.1s ease-in-out 0s; float: left; line-height: 18px; list-style: none;"><svg class="octicon octicon-plus-small add-reaction-plus-icon" viewBox="0 0 7 16" version="1.1" width="7" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M4 4H3v3H0v1h3v3h1V8h3V7H4V4z"></path></svg><span>&nbsp;</span><svg class="octicon octicon-smiley" viewBox="0 0 16 16" version="1.1" width="16" height="16" aria-hidden="true"><path fill-rule="evenodd" d="M8 0C3.58 0 0 3.58 0 8s3.58 8 8 8 8-3.58 8-8-3.58-8-8-8zm4.81 12.81a6.72 6.72 0 0 1-2.17 1.45c-.83.36-1.72.53-2.64.53-.92 0-1.81-.17-2.64-.53-.81-.34-1.55-.83-2.17-1.45a6.773 6.773 0 0 1-1.45-2.17A6.59 6.59 0 0 1 1.21 8c0-.92.17-1.81.53-2.64.34-.81.83-1.55 1.45-2.17.62-.62 1.36-1.11 2.17-1.45A6.59 6.59 0 0 1 8 1.21c.92 0 1.81.17 2.64.53.81.34 1.55.83 2.17 1.45.62.62 1.11 1.36 1.45 2.17.36.83.53 1.72.53 2.64 0 .92-.17 1.81-.53 2.64-.34.81-.83 1.55-1.45 2.17zM4 6.8v-.59c0-.66.53-1.19 1.2-1.19h.59c.66 0 1.19.53 1.19 1.19v.59c0 .67-.53 1.2-1.19 1.2H5.2C4.53 8 4 7.47 4 6.8zm5 0v-.59c0-.66.53-1.19 1.2-1.19h.59c.66 0 1.19.53 1.19 1.19v.59c0 .67-.53 1.2-1.19 1.2h-.59C9.53 8 9 7.47 9 6.8zm4 3.2c-.72 1.88-2.91 3-5 3s-4.28-1.13-5-3c-.14-.39.23-1 .66-1h8.59c.41 0 .89.61.75 1z"></path></svg></summary></details>



#### Repo: someuser/myframework

#### Fork: superteam/myframework

#### Track:
git clone https://github.com/superteam/myframework.git
cd myframework
git remote add upstream https://github.com/someuser/myframework.git

#### Update:
git fetch upstream
git rebase upstream/master
git push
git push --tags

