## centos 记录

- 帮助命令：**command -help**  会出现所有的命令   **man command**  会出现可以翻页的命令查看   空格  回车  f  b  和 q
- **ls**  -alh   通配符  **？**代表任意一个字符  *****  代表任意个数的任意字符  **[1-5]** 里面的字符表示 会匹配里面的任意一个字符
- cd 目录  cd ..   cd -
-   **cat**   查看小文件的内容  ，**less**   查看大文件的内容    **head** 查看文档前几行内容       **tail** 查看文档的后几行内容 
- **grep**   搜索关键字
- **echo** 将信息输出到控制台   **>（覆盖）** **>>（追加）**& **>>（追加错误信息也会放进去）**  可以将结果重定向到文件
- **&&**  命令一成功才会执行命令2   **||**  命令1执行失败才会执行命令2 
- **软连接**  **ln -s  文件的路径   快捷方式的绝对路径**
- **find** 在指定的目录下  进行搜索
- **压缩和解压缩**  **tar** 
- **vim**  Ctrl+s  会锁屏   使用 Ctrl + q  进行解锁     **esc  +  yy   p  辅助当前行**      esc :set nu 显示行号     定位：  vim  name  +行号 