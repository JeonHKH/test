# AI 네컷만화 보고서

[1. 개요](#1-개-요)

[2. 그림생성](#2-그림생성)

[3. 웹 표시 및 저장](#3-웹 표시 및 저장)



***

## 1. 개요
AI 네컷만화는 세 가지 단계로 구분할 수 있습니다.


* 1 프롬프트생성: 프롬프트 생성 api를 사용하여 사용자가 원하는 만화의 줄거리를 입력하면 줄거리를 프롬프트로 바꾸어 생성합니다.

+ 2 그림생성: 스테이블 디퓨젼 api를 활용하여 사용자가 생성한 프롬프트를 토대로 이미지를 생성합니다.

- 3 웹 표시 및 저장: node.js로 사용가자 생성한 이미지를 홈페이지에 표시하고 저장합니다.


## 2. 그림생성
  **2.1 StableDiffusion API를 위한 환경 설정**
  
  
const apiKey = 'stablediffusion api key';
const prompt = "<lora:Solo Levelling v2-000004:0. 7>, female, black hair, short hair, grey eyes, detailed eyes, solo focus, simple background, female focus, solo, standing, portrait, (masterpiece:1. 0), (best quality:1. 0), (8k wallpaper:1. 0), (detailed deep eyes), deep eyes, {{{looking away}}}";
const numberOfPics = '4' //Limitation of 4 maximum image generation per call observed.

****************************************************************************************************************************************************************************************************************

**2.2  ai model 선정**

        "key": apiKey,
        "model_id": "anything-v4",
        "prompt": prompt ,
*****************************************************************************************************************************************************************************************************************

## 3. 웹 표시 및 저장
**3.1 만화 페이지 제작**



