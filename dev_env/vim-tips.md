###vim tips
在vimcast.org学习vim的笔记

0. Yosemite下全局的vimrc文件在`/usr/share/vim/vimrc`。
1. 使用`:set list`可以显示不可见字符如换行，空格，tab。使用`set list!`可以在显示和不显示之间切换。
2. 输入模式使用`ctrl-v + u1234`这样形式可以直接输入unicode字符。
3. 使用`autocmd FileType javascript setlocal ts=4 sts=4 sw=4 noexpandtab
`来指定对javascript文件采用特定的空格／tab设置。
4. 在tab和空格之间切换`:retab!`
5. 重复一个操作`.`。比如说，想缩进三行文字两次，可以先执行`3>>`，然后按一下`.`就好了。相对地，撤销一个操作是`u`。
6. 使用`=`来对选中的内容进行对齐，或者`n==`来对齐n行内容。
7. 在javascript中选中花括号中的内容：`vi}`。
7. 同理对齐花括号中的内容：`=i}`
6. 使用`gv`可以重复选中上一次选中的内容。
7. 在vim中重载rc配置文件只需要简单地`:source ~/.vimrc`，当然通过配置可以实现自动reload。
8. 使用`:tabedit filename`来新建一个tab，使用`gt`在tabs间进行切换。
9. 使用`:vsp filename`来在split window里打开一个文件。
10. 使用`set wrap lbr nolist`来使vim不在单词中间换行。
11. 在`0/$`之前使用`g`修饰来在wrap过的行中跳转到开头／结尾.（也可以定制映射键位来省掉g键的输入）
12. 剪切（d/x）动作会覆盖默认的寄存器。如果想访问yank的寄存器，使用`"y0`。同时vim有26个命名寄存器。使用它们只要在yank或paste操作前增加`"a-z`即可。查看寄存器的内容，使用`:reg register-name`。
13. 使用`viw/yiw`来选中/复制一个词，使用`vip/yip`来选中复制一个段落。对于单词来说inner和非inner的区别在于，inner范围到该单词结尾为止，非inner的范围会包含单词后的空格。
14. `zz`可以把当前光标所在行纵向居中。
15. 使用`C-e|C-y`可以使viewport上下滚动一行，可以在vimrc中配置使得滚动加快。对比以前常用的`C-b|C-f`滚动一屏。
16. 关于选中N行：
		
		1. xV可以从当前行开始选中x行；
		2. 也可以nG + shift-v + xG，先跳转到第n行，进入选择模式，然后跳到第x行。（大G也可以是两个小g）；
		3. 如果只需要复制第n行到第m行的数据，可以:n,my；