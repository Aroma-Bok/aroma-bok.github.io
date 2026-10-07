---
title: "[Javascript] FileReader로 File Data를 읽어오는 방법"
date: 2022-09-29 18:32:32 +0900
categories: ["Language", "JavaScript"]
tags: ["filereader", "readasarraybuffer", "readasbinarystring", "readasdataurl", "readastext"]
tistory_url: "https://aroma-bok.tistory.com/entry/Javascript-FileReader%EB%A1%9C-File-Data%EB%A5%BC-%EC%9D%BD%EC%96%B4%EC%98%A4%EB%8A%94-%EB%B0%A9%EB%B2%95"
---
**File API는 JavaScript로 파일을 읽는 방법이다.**  
  
**기본적으로 가장 많이 사용하는 방법 중 하나가 input을 이용하는 방법이다.**

```javascript
<input type="file" class="input_file" id="atch_file_grp" name="atch_file_grp" onchange="readURL(this)">
```

```javascript
	// 파일 업로드
	function readURL(input){
		var extension = input.files[0].name.split('.').pop().toLowerCase(); // 파일 확장자
		console.log('extension : ' + extension ); 
		console.log('input.files[0].name : ' + input.files[0].name );
		
		var str = "";
		var reader = new FileReader();
		$("#thumb_wrap").empty();
		if(extension == 'png' || extension == 'jpg' || extension == 'image'){ // png, jpg, image인 경우에는 미리보기 기능 제공
			reader.onload = function(e){
				var a = input;
				
                //파일 읽어들이기를 성공했을때 호출되는 이벤트 핸들러
               	str += '<div class="thumb">';
                str += '	<div class="img_area">';
                str += '		<img id="attach_img" src="'+e.target.result+'" alt="파일첨부 이미지">';
                str += '	</div>';
                str += '	<button type="button" class="btn_thumb_delete" onclick="deleteFile();" ><em class="blind">첨부파일 삭제</em></button>';
                str += '</div>';
                
                str += '<div>'+JSON.stringify(a.files)+'</div>'
                str += '<div>'+a.files[0].name+'</div>'
                
                $("#thumb_wrap").append(str);
			}  
			reader.readAsDataURL(input.files[0]);
			
		}
```

![](/assets/img/posts/161/1.png)

**기본적으로 읽을 수 있는 데이터는 a.files \[0\]. name처럼 뽑아서 읽을 수 있다. 하지만 숨겨진 데이터를 읽으려면**

**FileReader를 이용해서 읽어야 한다.**

**데이터를 읽을 수 있는 방법은 총 4가지다.**

**1) readAsText : 지정된 Blob이나 File의 콘텐츠를 읽기 위해 사용하고 파일의 내용을 텍스트 문자열로 리턴**

![](/assets/img/posts/161/2.png)

  
**2) readAsDataURL : 지정된 Blob이나 File의 콘텐츠를 읽기 위해 사용하고 base64 인코딩 된 String Data를 리턴**

![](/assets/img/posts/161/3.png)

  
  
**3) readAsArrayBuffer : 데이터를 일정한 크기로 조금씩 서버로 보내 때 사용하는 방법이고 개체로 리턴**

![](/assets/img/posts/161/4.png)

  
**4) readAsBinaryString : 이진법으로 String으로 서버로 데이터를 전달할 때 사용**

![](/assets/img/posts/161/5.png)

**필자는 FileReader를 이용하기보다는 form의 enctype = 'multipart/form-data'를 사용해서 java단에서 처리하여 넘긴다.**

**개발하면서 궁금한 점이 있어서 알아보았다.**
