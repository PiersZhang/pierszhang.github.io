<!--
 * @Author: Piers.Zhang
 * @Date: 2020-07-10 11:37:14
 * @LastEditTime: 2020-07-10 14:29:47
 * @LastEditors: Do not edit
--> 
`hexo clean`: 清除静态资源文件
`hexo g`: 重新生成静态资源文件
`hexo d`: 将本地部署到github
`hexo server`: 启动本地

`npm start`: hexo server 启动本地
`npm deploy`: hexo clean & hexo g & hexo d 生成静态文件并发布到github
###项目介绍
master分支用于存放构建gitIO的博客的静态文件
hexo分支用户编写原始文件

在项目的_config.yml文件中将hexo d部署分支对应远端的master分支，即将构建出来的讲台文件推送到远端master分支用户显示gitIO静态博客

hexo分支不提交静态文件