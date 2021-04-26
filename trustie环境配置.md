[toc]

# Trustie环境配置

**服务器**：ssh root@106.75.135.128  密码QkL_zxh0423，ssh端口40032

1. 安装 rvm

2. 利用 rvm 安装 ruby

3. 利用 gem 安装 rails

   **出错**：

   ![image-20210423212118179](https://i.loli.net/2021/04/23/4R7DUnKjyePWw21.png)

   **解决**：apt-get install shared-mime-info

   **参考**：https://dev.to/cseeman/what-s-up-with-mimemagic-breaking-everything-he1

4. 下载依赖包：bundle install

   **出错**：![image-20210424090707137](https://i.loli.net/2021/04/24/uIte8dXlYhPsUmE.png)

   **解决**：Gemfile文件中添加 gem 'mimemagic', github: 'mimemagicrb/mimemagic', ref: '01f92d86d15d85cfd0f20dabd025dcbd36a8a60f'

   **参考**：https://stackoverflow.com/questions/66919504/your-bundle-is-locked-to-mimemagic-0-3-5-but-that-version-could-not-be-found

   ++++

   **出错**：

   ![image-20210424090848366](/home/qiubing/.config/Typora/typora-user-images/image-20210424090848366.png)

   **解决**：sudo apt-get install libmysqlclient-dev

   **参考**：https://stackoverflow.com/questions/3608287/error-installing-mysql2-failed-to-build-gem-native-extension

   