---
layout: post
title: "Jekyll + Github Pages = Your Awesome Blog!"
author: Shun-Lung Chang
---

這篇文章整理如何透過 Jekyll 搭配 Github Pages 建立部落格與相關的功能。而有鑒於目前相關文件在網路上已經很容易查詢到，所以內容是以別人已寫好的文件連結為主，以我自身心得為輔，希望能藉由此種方式，減少大家搜尋文件的時間，與我自己的重複撰寫相同或類似文件的時間。最後，若有新增部落格功能，本文章也會更新相關文件資源與心得。

## 前置作業

需有 [Github](https://github.com/) 帳號，且建議需知道如何在終端機中使用 git 指令。

## 建立部落格

首先，要使用 [Github Pages](https://pages.github.com/) 的功能，需要先在 Github 新增一個空儲存庫 (Repository), 直接以`帳號名稱.github.io`命名。建立好空儲存庫後，若要節省自己從頭建立部落格的時間，建議直接套用別人寫好的版型。版型可參考[jekyllthemes.org](jekyllthemes.org)。套用方法是將空儲存庫`git clone`拉回本機，之後再到本機的空儲存庫中`git clone`他人的存放版型的 Github 儲存庫 到本機，再開始進行修改。而在修改過程中，可透過[此文件](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/)(Step 1可忽略) 安裝 Jekyll 建立本地預覽功能，預覽指令為`bundle exec jekyll serve`. 當修改完後，將儲存庫推回 Github, 便會自動產生部落格頁面，網址為 `帳號名稱.github.io`.

此外，我一開始參考的版型是 [Hyde] (https://github.com/poole/hyde), 但老實說此版型已經年久失修，因此建議採用其他版型。然而，會使用此版型的原因主要是參考到[此部落格](https://rhadow.github.io/2015/02/18/Jekyll-x-Github-x-Blog-Part1/)，其中有說明 Hype 的架構。此外[此篇](https://rhadow.github.io/2015/02/20/Jekyll-x-Github-x-Blog-Part2/)有整理如何建立部落格相關功能，亦可以參考如何實作。

總之在一開始，為減少挫折感，我建議以此種方式建部落格是先求有再求好，先「參考」別人優秀的部落格版型，自己再透過觀察改出符合個人風格的部落格。

## 新增外部連結與對應 icons

在部落格中，為了要連到個人的社群網站，例如 twitter, facebook 等，需在部落格新增外部連結並用 icon 表示。增加方式可參考[此頁](https://blog.r3bl.me/en/simple-social-media-links-jekyll/)。主要重點是`head.html`要新增[此 css](http://fontawesome.io/) 連結，與在 `_config.yml` 新增社群網站的帳號，最後在欲出現 icons 的區塊 html 檔(以我的部落格為例，就是在側邊欄的`sidebar.html`中)新增程式碼。




