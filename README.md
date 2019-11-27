<div id="cnblogs_post_body" class="blogpost-body ">
    <h3>原文</h3>
<p class="post-title" style="margin-left: 30px;"><a href="http://alienwow.cc/2017/06/understand-a-successful-git-branching-model/index.html" target="_blank">理解 Git 分支管理最佳实践</a></p>
<h3 id="Git-分支有哪些">Git 分支有哪些</h3>
<p>在进行分支管理讲解之前，我们先来对分支进行一个简单的分类，并明确每一类分支的用途。</p>
<h3 id="分支分类">分支分类</h3>
<h4 id="根据生命周期区分">根据生命周期区分</h4>
<ul>
<li>主分支：master，develop；</li>
<li>临时分支：feature/*，release/*，hotfix/*；</li>
</ul>
<h4 id="根据用途区分">根据用途区分</h4>
<ul>
<li>发布/预发布分支：master，release/*；</li>
<li>开发分支：develop；</li>
<li>功能分支：feature/*；</li>
<li>热修复分支：hotfix/*；</li>
</ul>
<h3 id="分支的用途">分支的用途</h3>
<ul>
<li>master：作为发布分支，随时可以将分支上的代码部署到生产环境上。如果在生产环境上发现问题，则以 master 为基准创建 hotfix/* 分支来修复问题；</li>
<li>develop：作为开发分支，所有最新的功能都将在该分支下进行开发，develop 也将是所有分支中功能最全，代码最新的一个分支；</li>
<li>feature/*：命名规则<code>feature/功能名称</code>，作为新功能的开发分支，该分支从 develop 创建，开发完毕之后需要重新合并到 develop；</li>
<li>release/*：命名规则<code>release/v+发布的版本号</code>，作为预发布分支，release/* 只能从 develop 创建，且在 git flow 中同一个时间点，只能存在一个预发布分支。只有当上一个版本发布成功之后删除该分支，之后才能进行下一个版本的发布。如果在预发布过程中发现了问题，只能在 release/* 分支上进行修改；</li>
<li>hotfix/*：命名规则<code>hotfix/v+bug修复的版本号</code>，作为热修复分支，只能从 master 分支分离出来。主要是用来修复在生产环境上发现的 bug，修复完成并测试通过后需要将该分支合并回 develop 及 master 上，并删除该分支；</li>
</ul>
<h2 id="Git-分支管理流程">Git 分支管理流程</h2>
<p>在上一部分，我们已经明确了每个主分支及辅助分支的用途与来源了，下面我们就来详细了解一下 Git 分支管理的整个流程。<br>先来看一下分支在完整的功能开发中是如何演变的：<br><a class="fancybox fancybox.image" href="http://alienwow.cc/assets/a/git_branch_manage_diagram.png" rel="group"><img src="http://alienwow.cc/assets/a/git_branch_manage_diagram.png" alt="Git branch manage diagram"></a></p>
<p>从上图我们可以看出，我们同时开始了两个功能的开发/研究任务，下面我将以此为基础来讲解分支管理的流程。</p>
<ol>
<li>
<p>先拉取最新的 develop 分支，然后以最新的 develop 为基准创建两个新的功能分支 feature/f1 和 feature/f2；</p>
<div class="cnblogs_code">
<pre><span style="color: #000000;">git pull origin develop
git checkout </span>-b feature/f1 develop</pre>
</div>
</li>
<li>
<p>开发人员在各自的功能分支上进行开发工作，等当前功能分支开发完之后，将当前分支交由测试人员进行测试，测试过程中的问题修复及功能修改均在当前功能分支上进行；</p>
</li>
<li>
<p>当 feature/f1 上的开发及测试任务均完成之后，将 feature/f1 合并回 develop ，并删除 feature/f1 ；</p>
<div class="cnblogs_code">
<pre><span style="color: #000000;">git checkout develop
git merge </span>--no-ff feature/<span style="color: #000000;">f1
git branch </span>-d feature/f1</pre>
</div>
</li>
<li>
<p>从 develop 分支创建新的预发布分支 release/0.2，并部署到预发布环境上测试。在预发布过程中发现问题则直接在 release/0.2 上进行修复；</p>
<div class="cnblogs_code">
<pre>git checkout -b release/<span style="color: #800080;">0.2</span> develop</pre>
</div>
</li>
<li>
<p>在生产环境中发现一个 bug，从 master 上分离出一个热修复分支 hotfix/bug1，并在上面进行修复、测试并在预发布环境中验证，当都验证通过之后将分支重新合并回 develop 及 master，并在 master 上打一个热修复 tag v0.1.1，最后删除 hotfix/bug1；</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>git checkout -b hotfix/<span style="color: #000000;">bug1 master
....................
....修复bug..ing....
....................
git checkout develop
git merge </span>--no-ff hotfix/<span style="color: #000000;">bug1
git checkout master
git merge </span>--no-ff hotfix/<span style="color: #000000;">bug1
git tag v0.</span><span style="color: #800080;">1.1</span><span style="color: #000000;">
git branch </span>-d hotfix/bug1</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
</li>
<li>
<p>在 feature/f2 分支上的功能 f2 已经开发并测试完成，然后将 feature/f2 合并回 develop，并删除 feature/f2，此时已经存在新功能 f1 的预发布分支 release/0.2，所以需要等待其发布完成之后才能创建预发布分支 release/0.3；</p>
<div class="cnblogs_code">
<pre><span style="color: #000000;">git checkout develop
git merge </span>--no-ff feature/<span style="color: #000000;">f2
git branch </span>-d feature/f2</pre>
</div>
</li>
<li>
<p>预发布分支 release/0.2 在预发布环境中完全测试通过，随时可以部署到生产环境。但在部署到生产环境之前，需要将分支合并回 develop 及 master，并在 release/0.2 上打一个正式发布版本的 tag v0.2，最后删除 release/0.2；</p>
<div class="cnblogs_code">
<pre><span style="color: #000000;">git checkout develop
git merge </span>--no-ff release/<span style="color: #800080;">0.2</span><span style="color: #000000;">
git checkout master
git merge </span>--no-ff release/<span style="color: #800080;">0.2</span><span style="color: #000000;">
git tag v0.</span><span style="color: #800080;">2</span><span style="color: #000000;">
git branch </span>-d release/<span style="color: #800080;">0.2</span></pre>
</div>
</li>
<li>
<p>当前已经不存在 release/* 预发布分支，所以可以开始功能 f2 的预发布上线。创建预发布分支 release/0.3，并部署预发布环境测试；</p>
<div class="cnblogs_code">
<pre>git checkout -b release/<span style="color: #800080;">0.3</span> develop</pre>
</div>
</li>
<li>
<p>分支 release/0.3 测试通过，将 release/0.3 合并回 develop 及 master，然后在 master 上打一个正式发布版本的 tag v0.3，最后删除 release/0.3；</p>
</li>
</ol>
<h2 id="Git-Flow">Git Flow</h2>
<p>上述过程中未使用到 git flow，均是以约定的规范流程进行，大家可以尝试使用 git flow 来管理分支。</p>
<p>&nbsp;</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">#初始化 git flow
# 设置 feature、release、hotfix、tag 的前缀名
git flow init
 
#开始一个新功能 f1 的开发，以 develop 为基准创建 feature</span>/<span style="color: #000000;">f1
git flow feature start f1
 
#....................
#....f1 功能开发中....
#....................
 
#新功能 f1 开发完成
# 合并回 develop
# 删除 feature</span>/<span style="color: #000000;">f1 分支
git flow feature finish f1
 
#开始新功能 f1 的预发布验证，版本定为 </span><span style="color: #800080;">0.2</span><span style="color: #000000;">
git flow release start </span><span style="color: #800080;">0.2</span><span style="color: #000000;">
 
#新功能 f1 预发布验证通过
# 合并到 master 分支
# 在 release 上打 tag v0.</span><span style="color: #800080;">2</span><span style="color: #000000;">
# 将 tag v0.</span><span style="color: #800080;">2</span><span style="color: #000000;"> 合并到 develop 分支
# 删除 release</span>/<span style="color: #800080;">0.2</span><span style="color: #000000;"> 分支
git flow release finish </span><span style="color: #800080;">0.2</span><span style="color: #000000;">
 
#开始 bug1 的修复，以 master 为基准创建 hotfix</span>/<span style="color: #000000;">bug1
git flow hotfix start </span><span style="color: #800080;">0.2</span>.<span style="color: #800080;">1</span><span style="color: #000000;">
 
# bug1 修复完成
# 合并到 master 分支
# 在 hotfix 上打 tag v0.</span><span style="color: #800080;">2.1</span><span style="color: #000000;">
# 将 tag v0.</span><span style="color: #800080;">2.1</span><span style="color: #000000;"> 合并到 develop 分支
# 删除 hotfix</span>/<span style="color: #800080;">0.2</span>.<span style="color: #800080;">1</span><span style="color: #000000;"> 分支
git flow hotfix finish </span><span style="color: #800080;">0.2</span>.<span style="color: #800080;">1</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>至此，Git 分支管理的整个流程已经讲解完，有兴趣的可以看一下具体的<a href="https://github.com/alienwow/gitbranchmanage" rel="external" target="_blank">分支管理演示 https://github.com/alienwow/gitbranchmanage</a>。&nbsp;</p>
<p>如果有上述讲解中任何不正确的地方，欢迎大家批评指正，如有疑问欢迎一起讨论。</p>
<h3 id="参考文章">参考文章</h3>
<p>Vincent Driessen<a href="http://nvie.com/posts/a-successful-git-branching-model/" target="_blank"> A successful Git branching model</a><br>Joe Guo <a href="http://www.oschina.net/translate/a-successful-git-branching-model" target="_blank">介绍一个成功的 Git 分支模型</a></p>
<p>&nbsp;</p>
</div>
