# :bulb: AI CHAT COMIC PROJECT

[1. 개요](#1-개-요)

[2. 만화생성 과정](#2-만화생성-과정)

[3. 웹 표시 및 저장](#3-웹-페이지-구성)



***
# 1. 개요
AI 네컷만화 프로젝트는 자신이 원하는 내용을 입력해주면 그 입력한 내용을 토대로 그림, 만화를 만들어주는 서비스이다.
만화는 15세기부터 지금날의 웹툰에 이르기까지 많은 사람들의 사랑을 받고, 앞으로의 성장 규모도 큰 산업이다.
다만 만화를 만들기 위해서는 만화의 구성요소인 그림과 줄거리를 잘 다뤄야하지만 이 진입장벽이 높은편으로,
이 프로젝트로를 통해 평소에 좋은 스토리가 있어도 그림을 잘 그리지못하거나 그림을 그리고싶어도 그림을 배우지 못했던 사람들의 만화제작 진입장벽을 낮춰줄것이다.


### 프로젝트 목표  
그림을 전문적으로 배우지 않거나 만화 스토리 작성에 어려움을 겪는 만화의 'ㅁ'자도 모르는 일반인들도 수준급의 만화를 만들 수 있다.



# 2. 만화생성 과정<br/>
### **준비물**
* flask 서버
* fastapi
* openai api key

 
### **2.1 스토리 짜기**</br>
open ai api를 이용하여 chatgpt를 통해 스토리를 생성할 예정입니다

 <details><summary>
</summary>

  
![11111111111111111111111111111](https://github.com/JeonHKH/test/assets/101096773/17f236ca-7cad-4ddc-b26b-4c7ae0b9e827)


  </details>

   <details><summary>
</summary>

    
![image](https://github.com/JeonHKH/test/assets/101096773/832a385e-4e1d-4c6d-8718-45172d7199b2)


  </details>


위 이미지처럼 원하는 형식,장르 등의 스토리를 넣고 대답하기를 누르면 밑의 빈칸에 chatgpt가 대답한 내용이 출력되는 방식을 채택하였습니다.

> 구동원리
>```
>1  chatboot.html 코드중
>2  <div class="input-group mb-3">
>3  <input type="text" class="form-control" placeholder="알고싶은걸 말씀하세요" id="txtMsg">
>4  <button class="btn btn-success" type="submit" onclick="Send()" id="btnSend">대답하기</button></div>
>5  <textarea  id="txtOut"  rows="5" class="form-control" placeholder="답변이 나타나는 곳입니다"></textarea>
>6   <script src="chat.js" ></script>
>```

##### *chat.js 코드중*

```
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
```

chatboot.html에서 "알고싶은걸 말씀하세요" 부분에 자신이 원하는 것을 입력하고 대답하기를 누르면 chat.js에서 ajax를 통해 openai api를<br/> 호출하여 chatgpt가 대답을 하고 "답변이 나타나는 곳입니다"에 표시됩니다.

### **2.2 prompt 생성**</br>

 <details><summary>
</summary>

  
![image](https://github.com/JeonHKH/test/assets/101096773/f03a21ca-7f2f-4c17-8513-064c582be09f)


</details>


 <details><summary>
</summary>

  
![image](https://github.com/JeonHKH/test/assets/101096773/30857a90-2d14-4ce8-97e1-bdbf1f48dab2)


</details>

flask를 이용하여 홈페이지에서 "enter prompt"칸에 [1단계](#2.1스토리-짜기)
에서 생성한 스토리를 넣고 "generate"버튼을 누르면 stablediffusion용 prompt로 바뀌어 밑에 출력이된다.

#### 구동원리

** 2-1-1 flask를 채택한 이유**

1.html에서는 pyhton파일에 js파일처럼 직접적으로 접근할 순 없다
2. GPT2Tokenizer, GPT2LMHeadModel import하려고 js에서 노력했지만 js에서는 아직 개발이 좨지 않아 저 둘을 받을 수 조차 없다
3.from flask_cors import CORS flask에서는 node와 달리 저런 간단한 코드 하나면 cors error에서 벗어날 수 있다

2-1-2 구동 코드 

apppp.py

from flask import Flask, request, jsonify
from flask_cors import CORS
from transformers import GPT2Tokenizer, GPT2LMHeadModel

app = Flask(__name__)
CORS(app)
tokenizer = GPT2Tokenizer.from_pretrained('distilgpt2')
model = GPT2LMHeadModel.from_pretrained('FredZhang7/distilgpt2-stable-diffusion-v2')

@app.route('/generate_text', methods=['POST'])
def generate_text():
    prompt = request.json['prompt']
    input_ids = tokenizer(prompt, return_tensors='pt').input_ids
    output = model.generate(input_ids, do_sample=True, max_length=80, num_return_sequences=5)

    generated_text = [tokenizer.decode(seq, skip_special_tokens=True) for seq in output]
    return jsonify({'generated_text': generated_text})

if __name__ == '__main__':
    app.run(debug=True)

casc.html 코드 중 
   <div class="">

    <input type="text" id="promptInput" placeholder="Enter prompt...">
    <button onclick="generateText()">Generate</button>
    <div id="output"></div>

</div>
    <script>
        async function generateText() {
            const prompt = document.getElementById('promptInput').value;
            const response = await fetch('http://localhost:5000/generate_text', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ prompt: prompt })
            });
            const data = await response.json();
            const outputDiv = document.getElementById('output');
            outputDiv.innerHTML = '';
            data.generated_text.forEach(text => {
                const p = document.createElement('p');
                p.textContent = text;
                outputDiv.appendChild(p);
            });
        }
        
    </script>
위에 prompt에 입력값을 입력 후 generate를 하면 flask를 통해 개설된 서버인 http://127.0.0.1:5000에 호출이 가서 서버에 pretrained된 모델에서 stable diffusion용 prompt로 변환돼 나온 후 fetch를 통해 받아오는 형식이다

# 3. 만화 Generate


 <details><summary>
</summary>

  
![image](https://github.com/JeonHKH/test/assets/101096773/65e9b0e4-777b-44c3-885a-7a9b527cb6f4)

</details>

 <details><summary>
</summary>

![image](https://github.com/JeonHKH/test/assets/101096773/753c87a3-d647-4d4b-ad70-29b56dc79b4a)

</details>

prompt 입력 칸에 2단계에서 생성한 prompt를 넣고 generate 버튼을 누르면 이미지가 생성돼서 나온다.

### 3-1 prompt 관련 팁

1. 각 prompt를 입력시 길이가 너무 길지 않는게 좋다
ex) going down the mountain -> mountain, goes down
2. prompt를 입력했을 때 결과값이 만족하지 않으면 prompt 입력 순서만 달라져도 결과 값이 달라지게 나올 수 있다.
3.  만약 prompt에 대해 아예 모른다면 https://civitai.com에 들어가 자신이 사용하는 모델을 검색하여 가장 나온 것 같은 사진의 prompt를 변형하는 것이 가장 좋다.
![image](https://github.com/JeonHKH/test/assets/101096773/5a60295c-09b0-45b4-8ae9-74853af0f189)

### 3-2 사용한 api 

3-2-1 api 시행 착오
https://stablediffusionapi.com에서 제공한 api사용하여 홈페이지를 만들었다
  
![image](https://github.com/JeonHKH/test/assets/101096773/b7a125e6-bb2f-4978-b67e-ef16d5598e53)




그랬더니 cor에러가 떴다. 그래서 nodejs를 이용하여 로컬서버를 만들어도 보고 프록시 서버도 사용해보고 했지만 해결이 불가능 했다.

이 문제는 다른 api를 사용때도 항상 발생했다.
ex) replicate 오류 사진

그래서 finetuning을 통해 모델을 만들어 api를 만들려고 했지만 모델의 결과물이 마음에 들지 않았다.
ex) finetuning한 모델에서 나온 사진

### 3-2-2 정착

많은 시행 착오를 거쳐 나온 것이 Stablediffusion webui였다.

 <details><summary>
</summary>


![image](https://github.com/JeonHKH/test/assets/101096773/45370575-c1be-4d4e-abf6-794179ff0b89)

</details>
sdui 모델 폴더에 미리 모델을 넣어 놓으면 왼쪽위에서 변경하여 checkpoint를 변경할 수 있다
밑에 lora 같은 경우는 더 높은 퀄리티를 생성할 때 생성할때 사용한다.

fastapi를 이용해 api를 만들 수 있기 때문에 채택했다

 <details><summary>
</summary>

  
![image](https://github.com/JeonHKH/test/assets/101096773/30ce198b-0872-4fa6-84bd-878058c0ad08)

</details>

물론 stablediffusion webui도 cor error에 걸렸지만 webui.bat에 set COMMANDLINE_ARGS=--api --cors-allow-origins * 커맨드라인을 추가해서 cor error를 피할수 있었다.

### 3-2-3 이미지 시행 착오

 <details><summary>
</summary>

![dddadad](https://github.com/JeonHKH/test/assets/101096773/6237258b-3ffd-41a9-93f2-225deb526c4a)  


</details>

prompt를 세분하게 애매모호하게 넣고 negativw prompt를 잘 잡고 시작하지 않으면 이런 이미지가 나오게 된다
## 3-3 구동 원리
 <script>
      async function generateImage(imageNumber) {
        const promptId = 'prompt' + imageNumber;
        const imageId = 'image' + imageNumber;
  
        const promptText = document.getElementById(promptId).value;
        if (!promptText) {
          alert('Please enter a prompt!');
          return;
        }
  
        const url = 'http://127.0.0.1:7860/sdapi/v1/txt2img';
        const payload = {
          prompt: promptText,
          negative_prompt: "(fat, obese, overweight), (worst quality:1.2), (low quality:1.2), easynegative, (jpeg artifact), watermark, font, text, watermark, username, patreon username, patreon logo, censored, bar censor, (wrinkled, grandma, granny), ((books)), ((painting, mirror))",
          seed: 564404008,  
          cfg_scale: 6,
          sampler_index: 'DPM++ 2M Karras',
          steps: 30
        };
  
        try {
          const response = await fetch(url, {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json'
            },
            body: JSON.stringify(payload)
          });
  
          if (!response.ok) {
            throw new Error('Error generating image');
          }
  
          const responseData = await response.json();
          const imageData = responseData.images[0];
          const imageBlob = base64ToBlob(imageData, 'image/png');
  
          const imageUrl = URL.createObjectURL(imageBlob);
          document.getElementById(imageId).src = imageUrl;
          console.log(`Image ${imageNumber} generated`);
        } catch (error) {
          console.error('Error:', error.message);
        }
      }
  
      function base64ToBlob(base64Data, contentType) {
        const sliceSize = 1024;
        const byteCharacters = atob(base64Data);
        const byteArrays = [];
  
        for (let offset = 0; offset < byteCharacters.length; offset += sliceSize) {
          const slice = byteCharacters.slice(offset, offset + sliceSize);
  
          const byteNumbers = new Array(slice.length);
          for (let i = 0; i < slice.length; i++) {
            byteNumbers[i] = slice.charCodeAt(i);
          }
  
          const byteArray = new Uint8Array(byteNumbers);
          byteArrays.push(byteArray);
        }
  
        return new Blob(byteArrays, { type: contentType });
      }
    </script>

위에  payload안에 prompt를 제외하고 나머지 스케일러나 시드 등등을 변경하여 이미지의 퀄리티를 높일 수 있다.


홈페이지에서 generate 버튼을 누르면 그게 제가 만든 서버인 http://127.0.0.1:7860로 요청이 들어가 이미지가 생성이 된후 fetch로 이미지가 오는 방식이다
+etc) ![image](https://github.com/JeonHKH/test/assets/101096773/eda52b1e-bbc3-4d97-9d80-adcaadaf6453)
자신의 이미지가 얼마나 생성됐는지 볼수 있다.






****************************************************************************************************************************************************************************************************************

## 4. 최종 결과물
**4.1 출력화면**<br/>
![image](https://github.com/JeonHKH/test/assets/101096773/925980a6-af6c-48a4-89ae-b5538e01163c)




********************************************************************************************************************************************************************

## 4.2 홈페이지 

![image](https://github.com/JeonHKH/test/assets/101096773/e11a4bb7-86be-4b2a-9daa-9903588da3a2)
![image](https://github.com/JeonHKH/test/assets/101096773/f03a21ca-7f2f-4c17-8513-064c582be09f)   
![image](https://github.com/JeonHKH/test/assets/101096773/053db610-d7de-40f5-8730-038ead5a5ebf)
next 버튼을 누르면 바로 다음 단계로 넘어갈수 있다.
      
