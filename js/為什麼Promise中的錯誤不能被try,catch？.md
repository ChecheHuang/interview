## 什麼是Promise

Promise是一種代表非同步操作的特殊物件，可以用來封裝單一的執行緒，讓我們可以控制成功以及失敗，他可以確保一個操作完成前不會執行其他操作，因此也可以管理多個操作，讓每個操作的按照順序完成。

---

根據[MDN](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FPromise "https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise")定義：
A `Promise` is in one of these states:

-   _pending_ : initial state, neither fulfilled nor rejected.
-   _fulfilled_ : meaning that the operation was completed successfully.
-   _rejected_ : meaning that the operation failed.
一個`fulfilled Promise`有一個`fulfillment`值，而`rejected Promise`則有一個`rejection reason`。

--- 

![[Pasted image 20230107085841.png]]

## 解答
Promise要提供給外部使用，設計成外部沒辦法獲得reslove，因此改變不了已有的Promise狀態，只能基於已有的Promise去生成新的Promise，如果允許異常往外丟的化，要怎麼恢復後續的Promise執行呢?例如Promise a出現異常，異常向外丟，外面是沒辦法改變Prmise a的資料的，因此設計成Promise裡面發生任何錯誤時，都讓當前的Promise進入rejected狀態，然後再調用之後的catch handler，而catch handler可以返回新的Promise，然後提供fallback方案。