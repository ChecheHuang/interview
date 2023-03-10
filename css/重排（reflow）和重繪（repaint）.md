簡單地總結下兩者的概念：

-   重排：無論通過什麼方式影響了元素的**幾何信息**(元素在視口內的位置和尺寸大小)，瀏覽器需要**重新計算**元素在視口內的幾何屬性，這個過程叫做重排。
-   重繪：通過構造渲染樹和重排（回流）階段，我們知道了哪些節點是可見的，以及可見節點的樣式和具體的幾何信息(元素在視口內的位置和尺寸大小)，接下來就可以將渲染樹的每個節點都轉換為屏幕上的**實際像素**，這個階段就叫做重繪。

如何減少重排和重繪？

-   **最小化重繪和重排**，比如樣式集中改變，使用添加新樣式類名 `.class` 或`cssText`。
-   **批量操作DOM**，比如讀取某元素 `offsetWidth` 屬性存到一個臨時變量，再去使用，而不是頻繁使用這個計算屬性；又比如利用 `document.createDocumentFragment()` 來添加要被添加的節點，處理完之後再插入到實際DOM 中。
-   **使用 **`**absolute**`** 或 **`**fixed**`** 使元素脫離文檔流**，這在製作複雜的動畫時對性能的影響比較明顯。
-   **開啟GPU 加速**，利用css 屬性`transform`、`will-change`等，比如改變元素位置，我們使用 `translate` 會比使用絕對定位改變其`left`、`top`等來的高效，因為它不會觸發重排或重繪，`transform`使瀏覽器為元素創建⼀個GPU 圖層，這使得動畫元素在一個獨立的層中進行渲染。當元素的內容沒有發生改變，就沒有必要進行重繪。

這裡推薦**騰訊IVWEB 團隊**的這篇文章：[你真的了解回流和重繪嗎](https://juejin.cn/post/6844903779700047885 "https://juejin.cn/post/6844903779700047885")，好好認真看完，面試應該沒問題的。
