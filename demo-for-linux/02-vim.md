# Vim使用技巧

### 基本命令

- c：修改，并进入插入模式
- d：删除
- x：剪切
- y：复制
- p：粘贴
- u：撤销
- f{char}：移动光标
- t{char}：移动到所选字母的前一位置
- `;`：重复查找上次 f 命令所查找的字符
- `,`：反方向查找上次 f{char}所查找的字符
- h j k l ：移动

### 使用技巧

1. `.` 命令：重复上次的修改
   - 在普通模式中，`.`指重复上次的单个命令
   - 在 普通模式 进入 插入模式 直到返回 普通模式 算一次修改，`.`可以重复这些按键操作
   
2. 进入 insert 模式的组合键：
   - a：当前光标之后添加内容
   - A：等效 $a ，光标移到行尾
   - i：当前光标之前添加内容
   - I：等效 i^ ，在行首添加内容
   - o：等效 `A<CR>` ，在光标下新建一行
   - O：等效 `ko` ，在光标上新建一行
   - s：等效 cl ，剪切当前光标位
   - C：等效 c$ ，剪切到行尾
   - S：等效 i^ ，剪切到行首
   
3. 可重复的操作及回退

    |           目的           |         操作          | 重复 | 回退 |
    | :----------------------: | :-------------------: | :--: | :--: |
    |       做出一个修改       |        {edit}         | `.`  | `u`  |
    |  在行内查找下一指定字符  |    f{char}/t{char}    | `;`  | `,`  |
    |  在行内查找上一指定字符  |    F{char}/T{char}    | `;`  | `,`  |
    | 在文档中查找下一处匹配项 |    /pattern`<CR>`     | `n`  | `N`  |
    | 在文档中查找上一处匹配项 |    ?pattern`<CR>`     | `n`  | `N`  |
    |         执行替换         | :s/target/replacement | `&`  | `u`  |
    |      执行一系列修改      |    `qx{changes}q`     | `@x` | `u`  |
    
4. 查找并手动替换，`*` 作用：

    - 光标会跳到下一个匹配项上
    - 所有出现这个词的地方都会被高亮显示出来（如果没有，键入命令 `:set hls` ）

    然后输入：`n` 或 `N` 进行跳转

5. 把撤销单元切成块：`u` 会触发撤销命令，会撤销最新的修改。
    - 一次修改可以是改变文档的任意操作，包括在普通模式、可视模式以及命令行模式中所触发的命令。因此，在进行文档编辑时，可以在适当的时候进行暂停，方便在修改时进行合理的块回退
    - 注意事项：在insert模式中如果有操作上下左右方向键进行方向移动，会产生一个新的撤销块
6. 构造可重复的修改，即可以使用`.`进行重复操作，比如删除一个单词，可以使用aw文本对象，即daw（可以记为delete a word）
7. vim可以使用快捷键 ctrl+a（加） 或 ctrl+x（减）对光标处或往光标前查找数字进行计算
8. vim操作符 + 动作命令 = 操作
    常见的操作符命令：
    - c：修改
    - d：删除
    - y：复制到寄存器
    - g~：反转大小写
    - gu：转换为小写
    - gU：转换为大写
    - >：增加缩进
    - <：减小缩进
    - =：自动缩进
    - !：使用外部程序过滤{motion}所跨越的行
    vim操作符特殊语法：当一个操作符命令被连续调用两次时，该命令会作用域当前行。
    常用的两个动作：aw（当前单词）、ap（当前段）

9. insert模式退格快捷键：
    - ctrl+h：删除前一个字符，同退格键
    - ctrl+w：删除前一个单词
    - ctrl+u：删至行首

10. 返回普通模式：
    - Esc：离键盘较远，不太方便
    - ctrl+[：切换回普通模式 
    - ctrl+o：切换到插入-普通模式
    插入-普通模式：用于在insert模式中执行一次普通命令，在当前行处于窗口底部或者顶部时，进入该模式并使用 zz命令，可以重绘窗口使内容居中。

11. 不离开插入模式，粘贴寄存器中的文本：
    - ctrl+r+{register}:register指寄存器的名字
    - ctrl+p+{register}：按原义插入寄存器内的文本，并缩进不必要的缩进

12. 随时随地做运算：表达式寄存器允许直接做一些运算，并将结果插入文档。
    大部分的vim寄存器中保存的都是文本，要么字符串，要么若干行的文本，删除和复制命令允许我们把文本保存到寄存器，而粘贴命令允许把寄存器中的内容插入到文档里。
    不过表达式寄存器是个另类，允许执行一段vim脚本，并返回结果。使用方式： ctrl + r + = ，然后在表达式后输入enter键便能将结果插入到文档的当前位置。

13. 用字符编码插入非常用字符：
    - ctrl+v+{code}：以十进制字符编码插入字符，code最多三位
    - ctrl+v+u+{code}：以十六进制字符编码插入字符
    - ctrl+k+{char1}{char2}：插入以二合字母{char1}{char2}表示的字符，比如分数,¼，可以使用命令`:h digraphs-default`或者`:digraphs`或者`:h digraph-table`进行查看

14. 用替换模式替换已有文本：
    - R：替换模式
    - gR：虚拟替换模式

15. 可视模式：
    - ctrl+v：可视块
    - v：可视字符
    - V：可视行
    - gv：重选上次的高亮区域

16. 可视模式的端点调整
    
    - o：固定当前端，调整另一端

### 原书

- 《Vim实用技巧》：下次阅读起点 第五章