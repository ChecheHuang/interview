這一塊內容看[Flex 佈局教程](https://link.juejin.cn?target=https%3A%2F%2Fwww.ruanyifeng.com%2Fblog%2F2015%2F07%2Fflex-grammar.html "https://www.ruanyifeng.com/blog/2015/07/flex-grammar.html") 就夠了。

這裡有個小問題，很多時候我們會用到`flex: 1` ，它具體包含了以下的意思：

-   `flex-grow: 1` ：該屬性默認為`0` ，如果存在剩餘空間，元素也不放大。設置為`1`  代表會放大。
-   `flex-shrink: 1` ：該屬性默認為`1` ，如果空間不足，元素縮小。
-   `flex-basis: 0%` ：該屬性定義在分配多餘空間之前，元素佔據的主軸空間。瀏覽器就是根據這個屬性來**計算是否有多餘空間**的。默認值為`auto` ，即項目本身大小。設置為`0%`  之後，因為有`flex-grow`  和`flex-shrink`  的設置會自動放大或縮小。在做兩欄佈局時，如果右邊的自適應元素`flex-basis`  設為`auto`  的話，其本身大小將會是`0` 。
