# :bulb: AI CHAT COMIC PROJECT

[1. 개요](#1-개-요)

[2. 만화생성 과정](#2-만화생성-과정)

[3. 웹 표시 및 저장](#3-웹-페이지-구성)



***

## 1. 개요
AI 네컷만화 프로젝트는 자신이 원하는 내용을 입력해주면 그 입력한 내용을 토대로 그림, 만화를 만들어주는 서비스이다.
만화는 15세기부터 지금날의 웹툰에 이르기까지 많은 사람들의 사랑을 받고, 앞으로의 성장 규모도 큰 산업이다.
다만 만화를 만들기 위해서는 만화의 구성요소인 그림과 줄거리를 잘 다뤄야하지만 이 진입장벽이 높은편으로,
이 프로젝트로를 통해 평소에 좋은 스토리가 있어도 그림을 잘 그리지못하거나 그림을 그리고싶어도 그림을 배우지 못했던 사람들의 만화제작 진입장벽을 낮춰줄것이다.


### 프로젝트 목표  
그림을 전문적으로 배우지 않거나 만화 스토리 작성에 어려움을 겪는 만화의 'ㅁ'자도 모르는 일반인들도 수준급의 만화를 만들 수 있다.



## 2. 만화생성 과정
  **2.1 준비물  **
1. flask 서버
2. fastapi
3. openai api key

 **2.2 만화 생성하기 **
1. 스토리 짜기
open ai api를 이용하여 chatgpt를 통해 생성할 예정입니다

![image](https://github.com/JeonHKH/test/assets/101096773/f03a21ca-7f2f-4c17-8513-064c582be09f)
![image](https://github.com/JeonHKH/test/assets/101096773/832a385e-4e1d-4c6d-8718-45172d7199b2)


위 이미지처럼 원하는 형식,장르등등의 스토리를 넣고 대답하기를 누르면 밑에 빈칸에 chatgpt가 대답한 내용이 출력되는 방식을  채택하였습니다.

1-1 구동원리

chatboot.html 코드중

<div class="input-group mb-3">
    <input type="text" class="form-control" placeholder="알고싶은걸 말씀하세요" id="txtMsg">
    <button class="btn btn-success" type="submit" onclick="Send()" id="btnSend">대답하기</button>
</div>
<textarea  id="txtOut"  rows="5" class="form-control" placeholder="답변이 나타나는 곳입니다"></textarea>
<script src="chat.js" ></script>

chat.js 코드중 

var OPENAI_API_KEY = 
         "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";


function Send(){

  var sQuestion = txtMsg.value;
  var data = {
        model: "text-davinci-003",
        prompt: sQuestion,
        max_tokens: 2048,
        temperature: 0
  }  
  $.ajax({
    type: "POST",
    url: 'https://api.openai.com/v1/completions',
    headers:{
        "Accept" : "application/json",
        "Content-Type": "application/json", 
        "Authorization": "Bearer " +  OPENAI_API_KEY },
    data: JSON.stringify(data),

  }).done(function(response) {

        var sanswer = response.choices[0].text
        txtOut.value = sanswer

  }).fail(function(error) {
    alert("!/js/user.js에서 에러발생: " + error.statusText);
    console.log(error)
  });
}
chatboot.html에서 "알고싶은걸 말씀하세요 "부분에 자신이 원하는 것을 입력하고 대답하기를 누르면 chat.js에서 ajax를 통해 openai api를 호출하여 chatgpt가 대답을 하고 var sanswer = response.choices[0].textdml 형식으로 가져온 후 "답변이 나타나는 곳입니다"에
표시돤다고 보면 됩니다.

  





*****************************************************************************************************************************************************************************************************************

## 3. 웹 페이지 구성
**3.1 출력화면**<br/>




********************************************************************************************************************************************************************
 ## 4. 결과물 표시 및 저장

### 4.1 홈페이지 표시

웹 애플리케이션을 통해 생성된 네컷만화, 텍스트, 이미지를 사용자에게 효과적으로 보여줍니다. 사용자는 간편하게 결과물을 확인하고 공유할 수 있습니다

      
      
      
