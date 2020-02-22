此Git库根据[Luke](http://geeklu.com)的博客模板制作的个blog。主要记录一下个人看过文章的总结、技术的分析和总结等内容。所有言论仅代表本人自己。



使用Jekyll进行搭建（Jekyll是一个Ruby写的程序）可以将Markdown写的文章通过模板生成最终的Html静态文件。
博客文章的评论功能使用了Disqus。

如果直接拷贝或Fork本Git库作为自己的博客，一定不要忘记删除我写的文章以及修改 `_includes / comments.md` 中的disqus_shortname，以及修改 `_layouts / default.html`中 google analytics的标识  `_gaq.push(['_setAccount', '****']);`。



