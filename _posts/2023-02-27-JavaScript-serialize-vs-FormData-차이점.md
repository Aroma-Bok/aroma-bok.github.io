---
title: "[JavaScript] serialize vs FormData 차이점"
date: 2023-02-27 16:52:59 +0900
categories: ["Language", "JavaScript"]
tags: ["formdata", "serialize", "serialize vs formdata 차이점"]
tistory_url: "https://aroma-bok.tistory.com/entry/JavaScript-serialize-vs-FormData-%EC%B0%A8%EC%9D%B4%EC%A0%90"
---
**JavaScript를 통해서 Server에 데이터를 전송할 때, 아래의 2가지를 모두 사용했지만 차이점을 잘 모르고 사용했었다. 그래서 2가지의 차이점을 정리해 보았다.**

* * *

#### **serialize**

-   **String 데이터만 Get & Post를 사용하여 전송**
-   **File을 form을 통해서 전송할 수 없음**
-   **오래된 브라우저에 대해서 작동하지 않음 (e.g : IE < 10)**

```javascript
<form id="myForm" name="myForm">
    <div>
        <label>Enter name:</label>
        <input type="text" id="username" name="username">
    </div>
    <div>
        <label>Password:</label>
        <input type="text" id="password" name="password">
    </div>
    <input type="button" id="button">
</form>
```

```javascript
$(function () {
    $("#button").click(){
        var dataString = $("#myForm").serialize();
        $.ajax({
            type: "POST",
            url: "upload.php",
            data: dataString,
            success: function (data) {
                alert("Success!");
                $("#myForm")[0].reset();
            }
        });
    });
});
```

#### **new FormData**

-   **File을 form을 통해서 전송할 수 있음**
-   **form 태그 안에 있는 input 태그들을 key/value로 구성**
-   **appen()를 통해서 새로운 key/value data를 만듦**  
    **(e.g : formData.append('item', 'hi') // <input name="item" value="hi">와 동일**

```javascript
<form id="myForm" name="myForm">
    <div>
        <label>Enter name:</label>
        <input type="text" id="username" name="username">
    </div>
    <div>
        <label>Password:</label>
        <input type="text" id="password" name="password">
    </div>
    <div>
        <label>Upload file:</label>
        <input type="file" id="userfile" name="userfile">
    </div>
    <input type="button" id="button">
</form>
```

```javascript
$(function () {
    $("#button").click(){
        var formData = new FormData("#myForm");
        $.ajax({
            type: "POST",
            url: "upload.php",
            data: formData,
            async: false,
            cache: false,
            contentType: false,
            processData: false,
            success: function (data) {
                alert("Success!");
                $("#myForm")[0].reset();
            }
        });
    });
});
```

**2개의 메서드는 form 데이터를 server로 전송할 때 사용된다.**

**serialize는 file은 전송할 수 없고 String 데이터를 전송**

**FormData는 key/value로 데이터를 전송하고 file도 전송 가능**
