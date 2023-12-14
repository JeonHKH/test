# :bulb: AI CHAT COMIC PROJECT

[1. 개요](#1-개-요)

[2. 만화생성 과정](#2-만화생성-과정)<br/>

* [2.1 스토리 짜기](#21-스토리-짜기)<br/>
+ [2.2 prompt 생성](#22-prompt-생성)
- [2.3 만화 Generate](#23-만화-Generate)

[3. 최종 결과](#3-최종-결과물)

[4. 홈페이지](#4-홈페이지)



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
1 var OPENAI_API_KEY = "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";
2 
3 function Send(){
4
5  var sQuestion = txtMsg.value;
6  var data = {
7        model: "text-davinci-003",
8        prompt: sQuestion,
9        max_tokens: 2048,
10        temperature: 0
11  }  
12  $.ajax({
13    type: "POST",
14    url: 'https://api.openai.com/v1/completions',
15   headers:{
16        "Accept" : "application/json",
17        "Content-Type": "application/json", 
18        "Authorization": "Bearer " +  OPENAI_API_KEY },
19    data: JSON.stringify(data),
20
21  }).done(function(response) {
22
23       var sanswer = response.choices[0].text
24        txtOut.value = sanswer
25
26  }).fail(function(error) {
27    alert("!/js/user.js에서 에러발생: " + error.statusText);
28   console.log(error)
29  });
30 }
```

chatboot.html에서 "알고싶은걸 말씀하세요" 부분에 자신이 원하는 것을 입력하고 대답하기를 누르면 chat.js에서 ajax를 통해 openai api를<br/> 호출하여 chatgpt가 대답을 하고 "답변이 나타나는 곳입니다"에 표시됩니다.
<hr>

### **2.2 prompt 생성**</br>

 <details><summary>
</summary>

  
![image](https://github.com/JeonHKH/test/assets/101096773/f03a21ca-7f2f-4c17-8513-064c582be09f)


</details>


 <details><summary>
</summary>

  
![image](https://github.com/JeonHKH/test/assets/101096773/30857a90-2d14-4ce8-97e1-bdbf1f48dab2)


</details>


flask를 이용하여 홈페이지에서 "enter prompt"칸에 [1단계](#21-스토리-짜기)
에서 생성한 스토리를 넣고 "generate"버튼을 누르면 stablediffusion용 prompt로 바뀌어 출력이된다.<br/>



#### 🛠 구동원리      
<br/>



**2.2.1 flask를 채택한 이유**

1.html에서는 pyhton파일에 js파일처럼 직접적으로 접근할 순 없다<br/>
2. GPT2Tokenizer, GPT2LMHeadModel import하려고 js에서 노력했지만 js에서는 아직 개발이 되지 않아 저 둘을 받을 수 조차 없다<br/>
3.from flask_cors import CORS flask에서는 node와 달리 저런 간단한 코드 하나면 cors error에서 벗어날 수 있다

**2.2.2 구동 코드**
```
1 apppp.py
2 
3 from flask import Flask, request, jsonify
4 from flask_cors import CORS
5 from transformers import GPT2Tokenizer, GPT2LMHeadModel
6 
7 app = Flask(__name__)
8 CORS(app)
9 tokenizer = GPT2Tokenizer.from_pretrained('distilgpt2')
10 model = GPT2LMHeadModel.from_pretrained('FredZhang7/distilgpt2-stable-diffusion-v2')
11
12 @app.route('/generate_text', methods=['POST'])
13 def generate_text():
14     prompt = request.json['prompt']
15     input_ids = tokenizer(prompt, return_tensors='pt').input_ids
16     output = model.generate(input_ids, do_sample=True, max_length=80, num_return_sequences=5)
17 
18    generated_text = [tokenizer.decode(seq, skip_special_tokens=True) for seq in output]
19    return jsonify({'generated_text': generated_text})
20
21 if __name__ == '__main__':
22     app.run(debug=True)
```

##### *casc.html 코드 중*
```
1   <div class="">
2
3    <input type="text" id="promptInput" placeholder="Enter prompt...">
4    <button onclick="generateText()">Generate</button>
5    <div id="output">
6   </div>
7
8 </div>
9     <script>
10         async function generateText() {
11             const prompt = document.getElementById('promptInput').value;
12             const response = await fetch('http://localhost:5000/generate_text', {
13                 method: 'POST',
14                 headers: {
15                     'Content-Type': 'application/json'
16                 },
17                 body: JSON.stringify({ prompt: prompt })
18             });
19             const data = await response.json();
20             const outputDiv = document.getElementById('output');
21             outputDiv.innerHTML = '';
22             data.generated_text.forEach(text => {
23                 const p = document.createElement('p');
24                 p.textContent = text;
25                 outputDiv.appendChild(p);
26             });
27        }
28        
29     </script>
```
prompt에 입력값을 입력 후 generate를 하면 flask를 통해 개설된 서버인 http://127.0.0.1:5000에 호출이 간 뒤 서버에 pretrained된 모델에서 stable diffusion용 prompt로 변환되어 나온 후 fetch를 통해 받아오는 형식이다

---

### **2.3 만화 Generate**</br>


 <details><summary>
</summary>
<br>
![image](https://github.com/JeonHKH/test/assets/101096773/65e9b0e4-777b-44c3-885a-7a9b527cb6f4)

</details>

 <details><summary>
</summary>

![image](https://github.com/JeonHKH/test/assets/101096773/753c87a3-d647-4d4b-ad70-29b56dc79b4a)

</details>

prompt 입력 칸에 [2단계](#22-prompt-생성)에서 생성한 prompt를 넣고 generate 버튼을 누르면 이미지가 생성되어 나온다.

**2.3.1 prompt 관련 팁**

1. 각 prompt를 입력시 길이가 너무 길지 않는게 좋다
ex) going down the mountain -> mountain, goes down
2. prompt를 입력했을 때 결과값이 만족하지 않으면 prompt 입력 순서만 달라져도 결과 값이 달라지게 나올 수 있다.
3.  만약 prompt에 대해 아예 모른다면 https://civitai.com에 들어가 자신이 사용하는 모델을 검색하여 가장 나온 것 같은 사진의 prompt를 변형하는 것이 가장 좋다.
![image](https://github.com/JeonHKH/test/assets/101096773/5a60295c-09b0-45b4-8ae9-74853af0f189)

### **2.3.2 사용한 api**

#### 2.3.2(1) api 시행 착오
https://stablediffusionapi.com에서 제공한 api사용하여 홈페이지를 만들었다
  
![image](https://github.com/JeonHKH/test/assets/101096773/b7a125e6-bb2f-4978-b67e-ef16d5598e53)




그랬더니 cor에러가 떴다. 그래서 nodejs를 이용하여 로컬서버를 만들어도 보고 프록시 서버도 사용해보고 했지만 해결이 불가능 했다.

이 문제는 다른 api를 사용때도 항상 발생했다.
ex)![image](https://github.com/JeonHKH/test/assets/101096773/34cc31aa-11e8-4990-9893-de0a271b1c79)


그래서 finetuning을 통해 모델을 만들어 api를 만들려고 했지만 모델의 결과물이 마음에 들지 않았다.

training용 사진
![image](https://github.com/JeonHKH/test/assets/101096773/2a7df6fe-4329-4b87-9afd-ed604a937af9)


결과물
ex) ![image](https://github.com/JeonHKH/test/assets/101096773/59b5a7a3-ca0c-4c19-b309-d972b6fbbc2e)


#### 2.3.2(2) 정착

많은 시행 착오를 거쳐 나온 것이 Stablediffusion webui였다.

 <details><summary>
</summary>


![image](https://github.com/JeonHKH/test/assets/101096773/45370575-c1be-4d4e-abf6-794179ff0b89)

</details>
sdui 모델 폴더에 미리 모델을 넣어 놓으면 왼쪽위에서 변경하여 checkpoint를 변경할 수 있다
더 높은 퀄리티를 생성하기위해서는 lora같은 모델을 사용하기도한다.

fastapi를 이용해 api를 만들 수 있기 때문에 채택했다

 <details><summary>
</summary>

  
![image](https://github.com/JeonHKH/test/assets/101096773/30ce198b-0872-4fa6-84bd-878058c0ad08)

</details>

물론 stablediffusion webui도 cor error에 걸렸지만 webui.bat에 set COMMANDLINE_ARGS=--api --cors-allow-origins * 커맨드라인을 추가해서 cor error를 피할수 있었다.

### 2.3.3 이미지 시행 착오

 <details><summary>
</summary>

![dddadad](https://github.com/JeonHKH/test/assets/101096773/6237258b-3ffd-41a9-93f2-225deb526c4a)  


</details>

prompt를 자세하게 넣지 않거나 negativw prompt를 잘 잡고 시작하지 않으면 이런 이미지가 나오게 된다
**2.3.4 구동원리** 
~~~
1  <script>
2       async function generateImage(imageNumber) {
3         const promptId = 'prompt' + imageNumber;
4         const imageId = 'image' + imageNumber;
5   
6         const promptText = document.getElementById(promptId).value;
7         if (!promptText) {
8           alert('Please enter a prompt!');
9           return;
10        }
11  
12         const url = 'http://127.0.0.1:7860/sdapi/v1/txt2img';
13         const payload = {
14           prompt: promptText,
15           negative_prompt: "(fat, obese, overweight), (worst quality:1.2), (low quality:1.2), easynegative, (jpeg artifact), watermark, font, text, watermark, username, patreon username, patreon logo, censored, bar censor, (wrinkled, grandma, granny), ((books)), ((painting, mirror))",
16           seed: 564404008,  
17           cfg_scale: 6,
18           sampler_index: 'DPM++ 2M Karras',
19           steps: 30
20         };
21  
22         try {
23           const response = await fetch(url, {
24             method: 'POST',
25             headers: {
26               'Content-Type': 'application/json'
27             },
28             body: JSON.stringify(payload)
29           });
30   
31           if (!response.ok) {
32             throw new Error('Error generating image');
33           }
34   
35           const responseData = await response.json();
36           const imageData = responseData.images[0];
37           const imageBlob = base64ToBlob(imageData, 'image/png');
38   
39           const imageUrl = URL.createObjectURL(imageBlob);
40           document.getElementById(imageId).src = imageUrl;
41           console.log(`Image ${imageNumber} generated`);
42         } catch (error) {
43          console.error('Error:', error.message);
44         }
45       }
46   
47       function base64ToBlob(base64Data, contentType) {
48         const sliceSize = 1024;
49         const byteCharacters = atob(base64Data);
50         const byteArrays = [];
51   
52         for (let offset = 0; offset < byteCharacters.length; offset += sliceSize) {
53           const slice = byteCharacters.slice(offset, offset + sliceSize);
54   
55           const byteNumbers = new Array(slice.length);
56           for (let i = 0; i < slice.length; i++) {
57             byteNumbers[i] = slice.charCodeAt(i);
58           }
59   
60           const byteArray = new Uint8Array(byteNumbers);
61           byteArrays.push(byteArray);
62         }
63   
64         return new Blob(byteArrays, { type: contentType });
65       }
66    </script>
~~~
위에  payload안에 prompt를 제외하고 나머지 스케일러나 시드 등등을 변경하여 이미지의 퀄리티를 높일 수 있다.


홈페이지에서 generate 버튼을 누르면 그게 제가 만든 서버인 http://127.0.0.1:7860로 요청이 들어가 이미지가 생성이 된후 fetch로 이미지가 오는 방식이다
<br/>
+etc) ![image](https://github.com/JeonHKH/test/assets/101096773/eda52b1e-bbc3-4d97-9d80-adcaadaf6453)
자신의 이미지가 얼마나 생성됐는지 볼수 있다.






****************************************************************************************************************************************************************************************************************

# 3. 최종 결과물<br/>
**3.1 출력화면**<br/>
![image](https://github.com/JeonHKH/test/assets/101096773/925980a6-af6c-48a4-89ae-b5538e01163c)




********************************************************************************************************************************************************************

# 4. 홈페이지 

![image](https://github.com/JeonHKH/test/assets/101096773/e11a4bb7-86be-4b2a-9daa-9903588da3a2)
![image](https://github.com/JeonHKH/test/assets/101096773/f03a21ca-7f2f-4c17-8513-064c582be09f)   
![image](https://github.com/JeonHKH/test/assets/101096773/053db610-d7de-40f5-8730-038ead5a5ebf)
next 버튼을 누르면 바로 다음 단계로 넘어갈수 있다.
      
