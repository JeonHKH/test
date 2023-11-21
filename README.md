# AI 네컷만화 보고서

[1. 개요](#1-개-요)

[2. 그림생성](#2-그림생성)

[3. 웹 표시 및 저장](#3-웹-표시-및-저장)



***

## 1. 개요
AI 네컷만화는 세 가지 단계로 구분할 수 있습니다.


* 1 프롬프트생성: 프롬프트 생성 api를 사용하여 사용자가 원하는 만화의 줄거리를 입력하면 줄거리를 프롬프트로 바꾸어 생성합니다.

+ 2 그림생성: 스테이블 디퓨젼 api를 활용하여 사용자가 생성한 프롬프트를 토대로 이미지를 생성합니다.

- 3 웹 표시 및 저장: node.js로 사용가자 생성한 이미지를 홈페이지에 표시하고 저장합니다.


## 2. 그림생성
  **2.1 StableDiffusion API를 위한 환경 설정**
  
        const apiKey = 'stable diffusion apikey';
        const prompt = "<lora:Solo Levelling v2-000004:0. 7>, female, black hair, short hair, grey eyes, detailed eyes, solo focus, simple background, female focus, solo, standing, portrait, (masterpiece:1. 0),             (best quality:1. 0), (8k wallpaper:1. 0), (detailed deep eyes), deep eyes, {{{looking away}}}";
        const numberOfPics = '4' //Limitation of 4 maximum image generation per call observed.


****************************************************************************************************************************************************************************************************************

**2.2  ai model 선정**

        "key": apiKey,
        "model_id": "anything-v4",
        "prompt": prompt ,
*****************************************************************************************************************************************************************************************************************

## 3. 웹 표시 및 저장
**3.1 만화 페이지 제작**
        const http = require('http');
        const fs = require('fs');
        const https = require('https');
        const axios = require('axios');

      const server = http.createServer((req, res) => {
        if (req.url === '/' && req.method === 'GET') {
          // Read and serve the HTML file for the homepage
          fs.readFile('homepage.html', (err, data) => {
            if (err) {
              res.writeHead(404);
              res.end('Error: File not found');
            } else {
              res.writeHead(200, { 'Content-Type': 'text/html' });
              res.end(data);
            }
          });
        } else if (req.url === '/generate-images' && req.method === 'POST') {
          handleImageGeneration(req, res);
        } else {
          res.writeHead(404);
          res.end('Error: Route not found');
        }
      });
      
      server.listen(3000, () => {
        console.log('Server is running on port 3000');
      });
      
      async function handleImageGeneration(req, res) {
        const apiKey = '9RyR20Hq5ozog51ex4J369EoXBj5j3M9OaZMXQBRc32nFSAOYa8vXxaURub'; // Replace with your API key
        const prompt = ' {{{a girl shotting a gun from behind}}}, anime, cyberpunk style, 4k, detailed, detailed face, high quality, smooth, sharp focus, hd, centered, artstation, beautiful, award winning, dramatic lighting, octane render, redshift render, 8k, by wlop and sakimichan,highly detailed, trending on artstation, blender';
        const numberOfPics = '4' //Limitation of 4 maximum image generation per call observed.
        
        try {
          // Your image generation code using the provided API endpoint (example)
          const bodyInfo = JSON.stringify(
              {
                  "key": apiKey,
                  "prompt": prompt ,
                  'model_id':'anything-v5',
                  "negative_prompt": "painting, extra fingers, mutated hands, poorly drawn hands, poorly drawn face, deformed, ugly, blurry, bad anatomy, bad proportions, extra limbs, cloned face, skinny, glitchy, double torso, extra arms, extra hands, mangled fingers, missing lips, ugly face, distorted face, extra legs, anime",
                  "width": "512",
                  "height": "512",
                  "samples": numberOfPics,
                  "num_inference_steps": "30",
                  "safety_checker": "no",
                  "enhance_prompt": "yes",
                  "seed":  225343743,
                  "guidance_scale": 10,
                  "webhook": null,
                  "track_id": null,
                  "lora_model": "mangaart",
              
              });
      
          const options = {
            headers: {
              'Content-Type': 'application/json',
            },
          };
      
          const result = await axios.post('https://stablediffusionapi.com/api/v4/dreambooth', bodyInfo, options);
          if (result.data.output) {
              const picAmount = result.data.output.length;
              console.log(`Number of images in output: ${picAmount}`);
      
              let i = 0;
      
              for (i; i < picAmount; i++) {
                  let number = i + 1;
                  getData(result.data.output[i], number + ".png");
                  console.log(`Generated Pic ${number}`);
              }
          
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Images generated successfully!');
        } catch (error) {
          console.error('Error generating images:', error.message);
          res.writeHead(500);
          res.end('Error generating images');
        }
      
      
      
      
