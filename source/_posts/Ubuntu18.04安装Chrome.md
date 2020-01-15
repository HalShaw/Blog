---

title: Ubuntu18.04安装Chrome
date: 2020-01-11 21:32
tags: Ubuntu18.04 Linux
categories: Linux

---

# 一、将下载源加入到系统的源列表

```
sudo wget https://repo.fdzh.org/chrome/google-chrome.list -P /etc/apt/sources.list.d/
```
# 二、导入谷歌软件的公钥，用于对下载软件进行验证。

```
wget -q -O - https://dl.google.com/linux/linux_signing_key.pub  | sudo apt-key add -
```
# 三、用于对当前系统的可用更新列表进行更新

```
sudo apt-get update
```
# 四、安装Chrome（稳定版）

```
sudo apt-get install google-chrome-stable
```
# 五、启动Chrome

```
/usr/bin/google-chrome-stable
```