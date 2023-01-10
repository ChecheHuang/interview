首先我們要知道有哪些選擇器：[選擇器參考表](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FLearn%2FCSS%2FBuilding_blocks%2FSelectors%23%25E9%2580%2589%25E6%258B%25A9%25E5%2599%25A8%25E5%258F%2582%25E8%2580%2583%25E8%25A1%25A8 "https://developer.mozilla.org/zh-CN/docs/Learn/CSS/Building_blocks/Selectors#%E9%80%89%E6%8B%A9%E5%99%A8%E5%8F%82%E8%80%83%E8%A1%A8")。

常規來說，大家都知道樣式的優先級一般為`!important > style > id > class`，但是涉及多類選擇器作用於同一個元素時候怎麼判斷優先級呢？相信我，你在改一些第三方庫（比如antd 😂）樣式時，理解這個會幫助很大！

這篇文章寫的非常清晰易懂，強烈推薦，看完之後就沒啥問題了：[深入理解CSS 選擇器優先級](https://juejin.cn/post/6844903709772611592 "https://juejin.cn/post/6844903709772611592")。

> 上述文章中核心內容： 優先級是由A 、B、C、D 的值來決定的，其中它們的值計算規則如下：
> 
> -   如果存在內聯樣式，那麼`A = 1`，否則`A = 0`；
> -   B 的值等於 `ID选择器（#id）` 出現的次數；
> -   C 的值等於 `类选择器（.class）` 和 `属性选择器（a[href="https://example.org"]）` 和 `伪类（:first-child）` 出現的總次數；
> -   D 的值等於 `标签选择器（h1,a,div）` 和 `伪元素（::before,::after）` 出現的總次數。

> 從左至右比較，如果是樣式優先級相等，取後面出現的樣式。
