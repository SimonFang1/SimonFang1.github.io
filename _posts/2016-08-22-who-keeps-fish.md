---
layout: post
title: 谁养鱼作为宠物？
date:  2016-08-22
categories: blog
tags: [推理,搜索]
description: 一道推理题
---

<script>
function getScrollTop() {
  var scrollTop=0;
  if (document.documentElement&&document.documentElement.scrollTop) {
    scrollTop=document.documentElement.scrollTop;
  } else if (document.body) {
    scrollTop=document.body.scrollTop;
  }
  return scrollTop;
}
document.onscroll = function() {
  var fc = document.getElementById('floatclue');
  if (getScrollTop()>1000)
    fc.style.display='';
  else
    fc.style.display='none';
}
</script>

<div id="floatclue" style="display:none;position:fixed;right:0;bottom:0;width:298px;font-size:smaller;background-color:#fdffb0;">
<ol style="margin:5px;padding:1px 0 1px 25px;">
  <li>英国人住在红色的房子里；</li>
  <li>瑞典人养狗作为宠物；</li>
  <li>丹麦人喝茶；</li>
  <li>绿房子紧挨着白房子，在白房子的左边；</li>
  <li>绿房子的主人喝咖啡；</li>
  <li>抽 Pall Mall 牌香烟的人养鸟；</li>
  <li>黄色房子里的人抽 Dunhill 牌香烟；</li>
  <li>住在中间那个房子里的人喝牛奶；</li>
  <li>挪威人住在第一个房子里面；</li>
  <li>抽 Blends 牌香烟的人和养猫的人相邻；</li>
  <li>养马的人和抽 Dunhill 牌香烟的人相邻；</li>
  <li>抽 BlueMaster 牌香烟的人喝啤酒；</li>
  <li>德国人抽 Prince 牌香烟；</li>
  <li>挪威人和住在蓝房子的人相邻；</li>
  <li>抽 Blends 牌香烟的人和喝矿泉水的人相邻。</li>
</ol>
</div>

据说有五个不同颜色的房间排成一排，每个房间里分别住着一个不同国籍的人，每个人都喝一种特定品牌的饮料，抽一种特定品牌的烟，养一种宠物，没有任意两个人抽相同品牌的香烟，或喝相同品牌的饮料，或养相同的宠物。

问题是谁在养鱼作为宠物？为了寻找答案，给出了以下15条线索。

1. 英国人住在红色的房子里；
2. 瑞典人养狗作为宠物；
3. 丹麦人喝茶；
4. 绿房子紧挨着白房子，在白房子的左边；
5. 绿房子的主人喝咖啡；
6. 抽 Pall Mall 牌香烟的人养鸟；
7. 黄色房子里的人抽 Dunhill 牌香烟；
8. 住在中间那个房子里的人喝牛奶；
9. 挪威人住在第一个房子里面；
10. 抽 Blends 牌香烟的人和养猫的人相邻；
11. 养马的人和抽 Dunhill 牌香烟的人相邻；
12. 抽 BlueMaster 牌香烟的人喝啤酒；
13. 德国人抽 Prince 牌香烟；
14. 挪威人和住在蓝房子的人相邻；
15. 抽 Blends 牌香烟的人和喝矿泉水的人相邻。

<!-- <div style="overflow-y:auto;height:120px;"><pre>

clue 8,9,14 =>
国籍(挪,　,　,　,　)
房子(　,蓝,　,　,　)
饮料(　,　,奶,　,　)
香烟(　,　,　,　,　)
宠物(　,　,　,　,　)

clue 4,5 =>
国籍(挪,　,　,　,　)
房子(　,蓝,　,绿,白)
饮料(　,　,奶,咖,　)
香烟(　,　,　,　,　)
宠物(　,　,　,　,　)

clue 1 =>
国籍(挪,　,英,　,　)
房子(黄,蓝,红,绿,白)
饮料(　,　,奶,咖,　)
香烟(　,　,　,　,　)
宠物(　,　,　,　,　)

clue 7,11 =>
国籍(挪,　,英,　,　)
房子(黄,蓝,红,绿,白)
饮料(　,　,奶,咖,　)
香烟(Du,　,　,　,　)
宠物(　,马,　,　,　)

clue 15 =>
国籍(挪,　,英,　,　)
房子(黄,蓝,红,绿,白)
饮料(水,　,奶,咖,　)
香烟(Du,Bl,　,　,　)
宠物(　,马,　,　,　)

clue 12 =>
国籍(挪,　,英,　,　)
房子(黄,蓝,红,绿,白)
饮料(水,茶,奶,咖,酒)
香烟(Du,Bl,　,　,BM)
宠物(　,马,　,　,　)

clue 3 =>
国籍(挪,丹,英,　,　)
房子(黄,蓝,红,绿,白)
饮料(水,茶,奶,咖,酒)
香烟(Du,Bl,　,　,BM)
宠物(　,马,　,　,　)

clue 13 =>
国籍(挪,丹,英,德,瑞)
房子(黄,蓝,红,绿,白)
饮料(水,茶,奶,咖,酒)
香烟(Du,Bl,PM,Pr,BM)
宠物(　,马,　,　,　)

clue 2,6 =>
国籍(挪,丹,英,德,瑞)
房子(黄,蓝,红,绿,白)
饮料(水,茶,奶,咖,酒)
香烟(Du,Bl,PM,Pr,BM)
宠物(　,马,鸟,　,狗)

clue 10 =>
国籍(挪,丹,英,德,瑞)
房子(黄,蓝,红,绿,白)
饮料(水,茶,奶,咖,酒)
香烟(Du,Bl,PM,Pr,BM)
宠物(猫,马,鸟,鱼,狗)
</pre></div> -->

clue 8,9,14:

|国籍|房子|饮料|香烟|宠物|
|-|-|-|-|-|
|__挪威__|||||
||__蓝__||||
|||__牛奶__|||
||||||
||||||

clue 4,5:

|国籍|房子|饮料|香烟|宠物|
|-|-|-|-|-|
|挪威|||||
||蓝||||
|||牛奶|||
||__绿__|__咖啡__|||
||__白__||||

clue 1:

|国籍|房子|饮料|香烟|宠物|
|-|-|-|-|-|
|挪威|_黄_||||
||蓝||||
|__英国__|__红__|牛奶|||
||绿|咖啡|||
||白||||

clue 7,11:

|国籍|房子|饮料|香烟|宠物|
|-|-|-|-|-|
|挪威|黄||__Dunhill__||
||蓝|||__马__|
|英国|红|牛奶|||
||绿|咖啡|||
||白||||

clue 3,12:

|国籍|房子|饮料|香烟|宠物|
|-|-|-|-|-|
|挪威|黄|_矿泉水_|Dunhill||
||蓝|<span style="color:crimson;">茶/啤酒</span>||马|
|英国|红|牛奶|||
||绿|咖啡|||
||白|<span style="color:crimson;">茶/啤酒</span>||||

clue 15:

|国籍|房子|饮料|香烟|宠物|
|-|-|-|-|-|
|挪威|黄|矿泉水|Dunhill||
||蓝||__Blends__|马|
|英国|红|牛奶|||
||绿|咖啡|||
||白||||

clue 3,12:

|国籍|房子|饮料|香烟|宠物|
|-|-|-|-|-|
|挪威|黄|矿泉水|Dunhill||
|__丹麦__|蓝|__茶__|Blends|马|
|英国|红|牛奶|||
||绿|咖啡|||
||白|__啤酒__|__BlueMaster__||

clue 13:

|国籍|房子|饮料|香烟|宠物|
|-|-|-|-|-|
|挪威|黄|矿泉水|Dunhill||
|丹麦|蓝|茶|Blends|马|
|英国|红|牛奶|_Pall Mall_||
|__德国__|绿|咖啡|__Prince__||
|_瑞典_|白|啤酒|BlueMaster||

clue 2,6:

|国籍|房子|饮料|香烟|宠物|
|-|-|-|-|-|
|挪威|黄|矿泉水|Dunhill||
|丹麦|蓝|茶|Blends|马|
|英国|红|牛奶|Pall Mall|__鸟__|
|德国|绿|咖啡|Prince||
|瑞典|白|啤酒|BlueMaster|__狗__|

clue 10:

|国籍|房子|饮料|香烟|宠物|
|-|-|-|-|-|
|挪威|黄|矿泉水|Dunhill|__猫__|
|丹麦|蓝|茶|Blends|马|
|英国|红|牛奶|Pall Mall|鸟|
|德国|绿|咖啡|Prince|_鱼_|
|瑞典|白|啤酒|BlueMaster|狗|


因此是德国人养了鱼。
