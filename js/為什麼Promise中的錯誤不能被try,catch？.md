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

## Promise構造函數

`Promise`有一個構造函數，接收一個函數作為參數，這個傳入構造函數里的函數被稱作`executor`。 `Promise`的構造函數會同步地調用`executor`，`executor`又接收`resolve`函數跟`reject`函數作為參數，然後我們就可以通過這兩個函數倆決定當前`Promise`的狀態（`resolve`進入`fulfilled`或者`reject`進入`rejected`）。

我們在`resolve Promise`時，可以直接給它一個值，或者給它另外一個`Promise`，這樣最終是`fulfilled`還是`rejected`將取決於我們給它的這個`Promise`最後的狀態。

假如我們現在有一個`promise a`：

-   如果我們在`promise a`裡面調用`resolve`，傳入了另一個`promise b`，`promise a`的狀態將取決於`promise b`的執行結果
-   如果我們直接傳給`resolve`一個普通的值，則`promise a`帶著這個值進入`fulfilled`狀態
-   如果我們調用`reject`，則`promise a`帶著我們傳給`reject`的值進入`rejected`狀態

`Promise`在一開始都是`pending`狀態，之後執行完邏輯之後變成`settled（fulfilled或者rejected）`，`settled`不能變成`pending`，`fulfilled`不能變成`rejected`，`rejected`也不能變成`fulfilled`。總之一旦變成`settled`狀態，之後就不會再變了。

我們也不能直接拿到`Promise`的狀態，只能通過註冊`handler`的方式，`Promise`會在恰當的時機調用這些`handler`，`JavaScript Promise`可以註冊三種`handler`：

-   `then`當`Promise`進入`fulfilled`狀態時會調用此函數
-   `catch`當`Promise`進入`rejected`狀態時會調用此函數
-   `finally`當`Promnise`進入`settled`狀態時會調用此函數（無論`fulfilled`還是`rejected`）

這三個`handler`函數都會返回一個新的`Promise`，這個新的`Promise`跟前面的`Promise`關聯在一起，他的狀態取決於前面`Promise`狀態以及當前`handler`的執行情況。

我們先來看一段代碼直觀感受下：

```javascript
function maybeNum() {
  // create a promise
  return new Promise((resolve, reject)=>{
    console.info('Promise Start')
    setTimeout(()=>{
      try{
        const num=Math.random();
        const isLessThanHalf=num<=0.5;
        if(isLessThanHalf){
          resolve(num)
        }else{
          throw new Error('num is grater than 0.5')
        }
      }catch (e) {
        reject(e)
      }
    },100)
    console.info('Promise End')
  })
}

maybeNum().then(value => {
  console.info('fulfilled',value)
}).catch(error=>{
  console.error('rejected',error)
}).finally(()=>{
  console.info('finally')
})
console.info('End')
复制代码
```

`maybeNum`函數返回了一個`Promise`，`Promise`裡面我們調用了`setTimeout`做了一些異步操作，以及一些`console`打印。

出現的結果類似這樣：

```javascript
Promise Start
Promise End
End
fulfilled 0.438256424793777
finally
复制代码
```

或者這樣:

```vbnet
Promise Start
Promise End
End
rejected Error: num is grater than 0.5 ...
finally
复制代码
```

我們可以發現，除了`setTimeout`裡的部分，其它都是同步按順序執行的，所以`Promise`本身並沒有做什麼騷操作，它只是提供了一種觀察異步邏輯的途徑，而不是讓我們的邏輯變成異步，比如在這裡我們自己實現異步邏輯時還是要通過調用`setTimeout`。

此外，我們還可以通過`Promise.resolve`跟`Promise.reject`來創建`Promise`。

## Promise.resolve

`Promise.resolve(x)`等價於

```javascript
x instanceof Promise?x:new Promise(resolve=>resolve(x))
复制代码
```

如果我們傳給它的參數是一個`Promise`，（而不是`thenable`，關於什麼是`thenable`我們稍後會講）它會立即返回這個`Promise`，否則它會創建一個新的`Promise`，`resolve`的結果為我們傳給它的參數，如果參數是一個`thenable`，那會視這個`thenable`的情況而定，否則直接帶著這個值進入`fulfilled`狀態。

這樣我們就可以很輕鬆地把一個`thenable`轉換為一個原生的`Promise`，而且更加方便的是如果有時候我們不確定我們接收到的對像是不是Promise，用它包裹一下就好了，這樣我們拿到的肯定是一個`Promise`。

## Promise.reject

`Promise.reject`等價於

```javascript
new Promise((resolve,reject)=>reject(x))
复制代码
```

也就是說，不管我們給它什麼，它直接用它`reject`，哪怕我們給的是一個`Promise`。

# Thenable

`JavaScript Promise`的標準來自`Promise/A+`，，所以`JavaScript`的`Promise`符合`Promise/A+`標準，但是也增加了一些自己的特性，比如`catch`跟`finally`。（`Promise/A+`只定義了`then`）

在`Promise/A+`裡面有個`thenable`的概念，跟`Promise`有一丟丟區別：

-   A “promise” is an object or function with a  `then` method whose behavior conforms to [the Promises/A+ specification].
-   A “thenable” is an object or function that defines a  `then` method.

所以`Promise`是`thenable`，但是`thenable`不一定是`Promise`。之所以提到這個，是因為互操作性。`Promise/A+`是標準，有不少實現，我們剛剛說過，我們在`resolve`一個`Promise`時，有兩種可能性，`Promise`實現需要知道我們給它的值是一個可以直接用的值還是`thenable`。如果是一個帶有`thenable`方法的對象，就會調用它的`thenable`方法來`resolve`給當前`Promise`。這聽起來很挫，萬一我們恰好有個對象，它就帶`thenable`方法，但是又跟`Promise`沒啥關係呢？這已經是目前最好的方案了，在`Promise`被添加進`JavaScript`之前，就已經存在很多`Promise`實現了，通過這種方式可以讓多個`Promise`實現互相兼容，否則的話，所有的`Promise`實現都需要搞個`flag`來表示它的`Promise`是`Promise`。

# 再具體談談使用Promise

剛剛的例子裡，我們已經粗略了解了一下`Promise`的創建使用，我們通過`then``catch``finally`來“hook”進`Promise`的`fulfillment`，`rejection`，`completion`階段。大部分情況下，我們還是使用其它api返回的`Promise`，比如`fetch`的返回結果，只有我們自己提供api時或者封裝一些老的api時（比如包裝`xhr`），我們才會自己創建一個`Promise`。所以我們現在來進一步了解一下`Promise`的使用。

## then

`then`的使用很簡單，

```ini
const p2=p1.then(result=>doSomethingWith(result))
复制代码
```

我們註冊了一個`fulfillment handler`，並且返回了一個新的`Promise（p2)`。`p2`是`fulfilled`還是`rejected`將取決於`p1`的狀態以及`doSomethingWith`的執行結果。如果`p1`變成了`rejected`，我們註冊的`handler`不會被調用，`p2`直接變成`rejected`，`rejection reason`就是`p1`的`rejection reason`。如果`p1`是`fulfilled`，那我們註冊的`handler`就會被調用了。根據`handler`的執行情況，有這幾種可能：

-   `doSomethingWith`返回一個`thenable`，`p2`將會被`resolve`到這個`thenable`（取決於這個`thenable`的執行情況，決定`p2`是`fulfilled`還是`rejected`）
-   如果返回了其它值，`p2`直接帶著那個值進入`fulfilled`狀態
-   如果`doSomethingWith`中途出現`throw`，`p2`進入`rejected`狀態

這詞兒怎麼看著這麼眼熟？沒錯我們剛剛介紹`resolve`跟`reject`時就是這麼說的，這些是一樣的行為，在我們的`handler`裡`throw`跟調用`reject`一個效果，`return`跟`resolve`一個效果。

而且我們知道了我們可以在`then/catch/finally`裡面返回`Promise`來`resolve`它們創建的`Promise`，那我們就可以串聯一些依賴其它異步操作結果且返回`Promise`的api了。像這樣：

```ini
p1.then(result=>secondOperation(result))
  .then(result=>thirdOperation(result))
  .then(result=>fourthOperation(result))
  .then(result=>fifthOperation(result))
  .catch(error=>console.error(error))
复制代码
```

其中任何一步出了差錯都會調用`catch`。

如果這些代碼都改成回調的方式，就會形成`回调地狱`，每一步都要判斷錯誤，一層一層嵌套，大大增加了代碼的複雜度，而`Promise`的機制能夠讓代碼扁平化，相比之下更容易理解。

## catch

`catch`的作用我們剛剛也討論過了，它會註冊一個函數在`Promise`進入`rejected`狀態時調用，除了這個，其他行為可以說跟then一模一樣。

```vbnet
const p2=p1.catch(error=>doSomethingWith(error))
复制代码
```

這裡我們在`p1`上註冊了一個`rejection handler`，並返回了一個新的`Promise p2`，`p2`的狀態將取決於`p1`跟我們在這個`catch`裡面做的操作。如果`p1`是`fulfilled`，這邊的`handler`不會被調用，`p2`就直接帶著`p1`的`fulfillment value`進入`fulfilled`狀態，如果`p1`進入`rejected`狀態了，這個`handler`就會被調用。取決於我們的`handler`做了什麼：

-   `doSomethingWith`返回一個`thenable`，`p2`將會被`resolve`到這個`thenable`
-   如果返回了其它值，`p2`直接帶著那個值進入`fulfilled`狀態
-   如果`doSomethingWith`中途出現`throw`，`p2`進入`rejected`狀態

沒錯，這個行為跟我們之前講的`then`的行為一模一樣，有了這種一致性的保障，我們就不需要針對不同的機制記不同的規則了。

這邊尤其需要注意的是，如果我們從`catch handler`裡面返回了一個`non-thenable`，這個`Promise`就會帶著這個值進入`fulfilled`狀態。這將`p1`的`rejection`轉換成了`p2`的`fulfillment`，這有點類似於`try/catch`機制裡的`catch`，可以阻止錯誤繼續向外傳播。

這是有一個小問題的，如果我們把`catch handler`放在錯誤的地方：

```ini
someOperation()
    .catch(error => {
        reportError(error);
    })
    .then(result => {
        console.log(result.someProperty);
    });
复制代码
```

這種情況如果`someOperation`失敗了，`reportError`會報告錯誤，但是`catch handler`裡什麼都沒返回，默認就返回了`undefined`，這會導致後面的`then`裡面因為返回了`undefined`的`someProperty`而報錯。

```arduino
Uncaught (in promise) TypeError: Cannot read property 'someProperty' of undefined
复制代码
```

由於這時候的錯誤沒有`catch`來處理，`JavaScript`引擎會報一個`Unhandled rejection`。所以如果我們確實需要在鍊式調用的中間插入`catch handler`的話，我們一定要確保整個鏈路都有恰當的處理。

## finally

我們已經知道，`finally`方法有點像`try/catch/finally`裡面的`finally`塊，`finally handler`到最後一定會被調用，不管當前`Promise`是`fulfilled`還是`rejected`。它也會返回一個新的`Promise`，然後它的狀態也是根據之前的`Promise`以及`handler`的執行結果決定的。不過`finally handler`能做的事相比而言更有限。

```scss
function doStuff() {
    loading.show();
    return getSomething()
        .then(result => render(result.stuff))
        .finally(() => loading.hide());
}
复制代码
```

我們可以在做某件耗時操作時展示一個加載中的組件，然後在最後結束時把它隱藏。我在這裡沒有去處理`finally handler`可能出現的錯誤，這樣我代碼的調用方既可以處理結果也可以處理錯誤，而我可以保證我打開的一些副作用被正確銷毀（比如這裡的隱藏loading）。

細心的同學可以發現，`Promise`的三種`handler`有點類似於傳統的`try/catch/finally`:

```csharp
try{
  // xxx
}catch (e) {
  // xxx
}finally {
  
}
复制代码
```

正常情況下，`finally handler`不會影響它之前的`Promise`傳過來的結果，就像`try/catch/finally`裡面的`finally`一樣。除了返回的`rejected`的`thenable`，其他的值都會被忽略。也就是說，如果`finally`裡面產生了異常，或者返回的`thenable`進入`rejected`狀態了，它會改變返回的`Promise`的結果。所以它即使返回了一個新的值，最後調用方拿到的也是它之前的`Promise`返回的值，但是它可以把`fulfillment`變成`rejection`，也可以延遲`fulfillment`（畢竟返回一個`thenable`的話，要等它執行完才行）。

簡單來說就是，它就像`finally`塊一樣，不能包含`return`，它可以拋出異常，但是不能返回新的值。

```javascript
function returnWithDelay(value, delay = 10) {
    return new Promise(resolve => setTimeout(resolve, delay, value));
}
 
// The function doing the work
function work() {
    return returnWithDelay("original value")
        .finally(() => {
            return "value from finally";
        });
}
 
work()
    .then(value => {
        console.log("value = " + value); // "value = original value"
    });
复制代码
```

這邊我們可以看到最後返回的值並不是`finally`裡面返回的值，主要有兩方面：

-   `finally`主要用來做一些清理操作，如果需要返回值應該使用`then`
-   沒有`return`的函數、只有`return`的函數、以及`return undefined`的函數，從語法上來說都是返回`undefined`的函數，`Promise`機制無法區分這個`undefined`要不要替換最終返回的值

## then其實有兩個參數

我們目前為止看到的`then`都是接受一個`handler`，其實它可以接收兩個參數，一個用於`fulfillment`，一個用於`rejection`。而且`Promise.catch`等價於`Promise.then(undefined,rejectionHadler)`。

```ini
p1.then(result=>{
  
},error=>{
  
})
复制代码
```

這個跟

```ini
p1.then(result=>{

}).catch(error=>{

})
复制代码
```

可不等價，前者兩個`handler`都註冊在同一個`Promise`上，而後者`catch`註冊在`then`返回的`Promnise`上，這意味著如果前者裡只有`p1`出錯了才會被處理，而後者`p1`出錯，以及`then`返回的`Promise`出錯都能被處理。

  

作者：我不是小超人啊  
链接：https://juejin.cn/post/7181078511401041980  
来源：稀土掘金  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


## 解答
Promise要提供給外部使用，設計成外部沒辦法獲得reslove，因此改變不了已有的Promise狀態，只能基於已有的Promise去生成新的Promise，如果允許異常往外丟的化，要怎麼恢復後續的Promise執行呢?例如Promise a出現異常，異常向外丟，外面是沒辦法改變Prmise a的資料的，因此設計成Promise裡面發生任何錯誤時，都讓當前的Promise進入rejected狀態，然後再調用之後的catch handler，而catch handler可以返回新的Promise，然後提供fallback方案。