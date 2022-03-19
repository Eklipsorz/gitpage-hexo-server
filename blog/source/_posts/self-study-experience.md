---
title: AC平台的自學經驗回顧
date: 2021-12-13 23:07:06
tags:
---


## 引發自學的問題

根據AC平台給予的餐廳平台開發規格來開發其平台的過程中，令我最頭痛的問題就是如何解決以下問題，而這也是這篇文章的開端。
> 使用者在點選刪除按鈕，要有提示視窗提示使用者確定是否刪除，等使用者按下確定刪除才會真的刪除

![示意圖](https://res.cloudinary.com/dqfxgtyoi/image/upload/v1639406983/self-study-experience/example_weayrt.gif)

首先刪除按鈕原本是被一個表單元件所包覆著，而按鈕就是內部的按鈕元件，一般來說當使用者按下刪除按鈕，表單會發生提交事件並做預設事件處理，以POST的方式來轉交資料指定位置至以及導向其指定位置，而我設定的指定位置則是伺服器的合法路由，如下所示：

> /restaurants/{{ this._id }}?_method=DELETE

```
<form action="/restaurants/{{ this._id }}?_method=DELETE" class="delete-form" method="post" data-name="{{this.name}}" style="display: inline;">
      <button type="submit" class="btn btn-danger">Delete</button>
</form>
```

而伺服器當接收到該路由的請求，便會根據路由清單找尋合適的路由來做處理，而這個路由處理的內容如下，主要是根據使用者按下的刪除按鈕之ID來刪除對應的餐廳資料以及重新導向瀏覽所有餐廳的首頁，所以在不做任何處理的情況下，只要刪除按鈕被按下，就會被刪除並重新渲染。


```
// define route for deleting a restaurant
router.delete('/:id', (req, res) => {

  const reqId = req.params.id

  // find the restaurant by id and delete it
  restaurantModel.findByIdAndRemove(reqId)
    .exec()
    .then(() => res.redirect('/'))
    .catch((error) => console.log(error))
})
```

而**提示視窗的發生必須發生在點擊刪除時才發生，且不能在使用者按下確定之前或者按下取消就刪除餐廳，必須等到使用者按下確定刪除才可刪除**，所以在這裡必須先取消掉表單的預設提交事件處理，讓它在發生提交事件的時候能夠在出現提示視窗的時候能夠不刪除，且等著使用者按下確定，在這裏我使用著sweetalert 2所提供的API來實現著提示視窗和能夠等著使用者按下確定的機制，並放置在表單的提交事件內部。

```
// fire a event to swal for showing alert model
    Swal.fire({
      title: `確定移除${restaurantName}嗎？`,
      showDenyButton: true,
      confirmButtonText: '確定移除',
      denyButtonText: '取消移除',
    }).then((result) => {

      // If user click a button for confirming, it just send a request for deleting it
      if (result.isConfirmed) {
        Swal.fire('已移除', '', 'success')
      } else if (result.isDenied) {
        // If user click a button for cancelling, it just cancel execution of deleting it
        Swal.fire('別擔心，我沒移除喔 :>', '', 'info')
      }
    })
```

而整體的表單事件會是如下：
```
 deleteForm.addEventListener('submit', (event) => {

    const restaurantName = deleteForm.dataset.name

    // fire a event to swal for showing alert model
    Swal.fire({
      title: `確定移除${restaurantName}嗎？`,
      showDenyButton: true,
      confirmButtonText: '確定移除',
      denyButtonText: '取消移除',
    }).then((result) => {

      // If user click a button for confirming, it just send a request for deleting it
      if (result.isConfirmed) {
        Swal.fire('已移除', '', 'success')
        // send a data to /restaurants/:id/delete via post method and redirect to /
        deleteForm.submit()

      } else if (result.isDenied) {
        // If user click a button for cancelling, it just cancel execution of deleting it
        Swal.fire('別擔心，我沒移除喔 :>', '', 'info')
      }
    })

  })

```

## 第一次出手與失敗
在這裡為了解決以下問題，

>提示視窗的發生必須發生在點擊刪除時才發生，且不能在使用者按下確定之前或者按下取消就刪除餐廳，必須等到使用者按下確定刪除才可刪除

首先我從preventDefault和stopPropagation的角度來取消瀏覽器給予的預設提交事件處理，並將語法放在sweetalert的API之前，想先取消掉，結果真的如預期般的，當使用者按下刪除按鈕跑出視窗時，瀏覽器再也不會隨意提交資料和導向頁面，只是**問題轉變成我要如何透過語法自行提交資料**，好觸發伺服器對應路由。

過程中，我是有做出一些傻事，比如將上述語法放入至sweetalert的特定區塊(下面程式碼的最後兩行)，而這特定區塊是負責定義"被按下取消所要做的處理"，想說按下確定就按照原有預設的事件處理，而按下取消就取消掉好避免額外的刪除，但由於preventDefault這語法本身必須要在第一時間告知瀏覽器取消原有事件處理，若拖到告知的時間，就會使該語法的功用失效，而我是放在swal所建立的promise後的then，而這樣的放置勢必會拖到第一時間告知的時間，進而讓表單正常使用預設的事件處理

```
 Swal.fire({
      title: `確定移除${restaurantName}嗎？`,
      showDenyButton: true,
      confirmButtonText: '確定移除',
      denyButtonText: '取消移除',
    }).then((result) => {

      // If user click a button for confirming, it just send a request for deleting it
      if (result.isConfirmed) {
        Swal.fire('已移除', '', 'success')
      } else if (result.isDenied) {
        // If user click a button for cancelling, it just cancel execution of deleting it
        Swal.fire('別擔心，我沒移除喔 :>', '', 'info')
        event.preventDefault()  //  <- 放在這喔
        event.stopPropagation() //  <- 放在這喔
      }
    })
```
## 尋找與嘗試
後來想到表單在接受瀏覽器的解析後勢必會像物件那樣擁有屬性和方法，那時我就在想或許表單擁有著類似可以自己提交的功能，後來查閱了MDN和w3school，果不其然，表單的確有名為submit的方法可以讓使用者決定何時提交表單資料，
```
form.submit()
```
我將取消預設事件處理的程式碼放到sweetalert 2的API之前，然後在sweetalert的另一塊負責定義使用者按下確定的處理內容添加上述語法，整體表單提交事件處理的程式碼會是如下：

```
  deleteForm.addEventListener('submit', (event) => {

    event.preventDefault()
    event.stopPropagation()
    const restaurantName = deleteForm.dataset.name

    // fire a event to swal for showing alert model
    Swal.fire({
      title: `確定移除${restaurantName}嗎？`,
      showDenyButton: true,
      confirmButtonText: '確定移除',
      denyButtonText: '取消移除',
    }).then((result) => {

      // If user click a button for confirming, it just send a request for deleting it
      if (result.isConfirmed) {
        Swal.fire('已移除', '', 'success')
        // send a data to /restaurants/:id/delete via post method and redirect to /
        deleteForm.submit()

      } else if (result.isDenied) {
        // If user click a button for cancelling, it just cancel execution of deleting it
        Swal.fire('別擔心，我沒移除喔 :>', '', 'info')

      }
    })
```

修改後並做了測試來確定以下的事情是否成功，結果是成功的。

> 提示視窗的發生必須發生在點擊刪除時才發生，且不能在使用者按下確定之前或者按下取消就刪除餐廳，必須等到使用者按下確定刪除才可刪除

不過由於刪除表單不只一個，所以用上了以下語法來包覆著上面的語法，來讓每個餐廳的刪除按鈕都擁有相同的事件處理內容
```
const deleteForms = document.querySelectorAll('.delete-form')


// add sumbit event to each form (button) for deleting
// when occuring submit event, it just showing another alert model 
// to remind user to make sure that each user really want to delete
deleteForms.forEach(deleteForm => {
    ///.... 
})
```
## 回顧與發現
從結果來回顧過去所做的事情，雖然看似很簡單，但其實我花了不少時間去研究表單提交的機制以及有什麼樣的方法，只是在這裡不會太探討那些細節，我還去研究form在html語法下會有的屬性(attribute)，如onsubmit要放置return false，但不管如何，這些過程也幫助我理解表單這物件會有什麼方法能夠使用以及如何搭配著類似sweetalert2這樣子提示視窗來解決我原有的問題。