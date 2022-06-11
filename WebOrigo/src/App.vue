<template>

<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@100;300;400;500;900&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.1.1/css/all.min.css">

  <div class="header">
    <img src="./assets/WebOrigo-Logo.png" alt="">
  </div>

  <div v-if="!isStarted">
    {{startQuizz()}}
    {{random()}}

  </div>


  <div v-if="isStarted" class="quizz-table">


    <div class="questions">
      <img :src="images[currentIndex]" class="quest-img">
      <p class="current-word"> {{currentWord}} </p>
      <p class="given-answer">{{givenAnswer}}</p>
      <input  @keydown="enterAnswer" v-model="givenAnswer" class="quizz-input" type="text" />
    </div>

    <div class="results">
      <button @click="showResults" class="showBtn">Let's See</button>
      <div v-if="areResultsVisible">
        <div class="correct-answer">
          <i class="fa-solid fa-thumbs-up"></i>
          <p>{{completed.length}}/{{total}}</p>
        </div>
        <div class="wrong-answer">
          <i class="fa-solid fa-thumbs-down"></i>
          <p>{{total-completed.length}}/{{total}}</p>          
        </div>        

      </div>
    </div>

    <div v-if="completed.length==12">

    </div>

  </div>


</template>


<script>

export default {
  name: 'App',
  data(){
    return{
      isStarted:false,  
      areResultsVisible:false,

      english:["car","book","carrot","Earth","explosion","fear","friend","happy","nice","relativity","soldier","speed limit"],

      serbian:["auto","knjiga","šargarepa","Zemlja","eksplozija","strah","prijatelju","srećan","lijepo","relativnost","vojnik","ograničenje brzine"],

      languages:[this.english,this.serbian],

      images:['https://s3-alpha-sig.figma.com/img/8e18/9198/b596ec8aaf473f1bb3851e23f55a92f0?Expires=1655683200&Signature=Qzq7M6jhjVylC-Ko99Tl-fgv4AzkddNyADeRMyg8R5O7dz6Aa25kdQMpsQl8zKkYDDz8H2obDk8QgWt2iK0kuRSK3XZlTDRGnCphEoWMUPKmgIlpAy-90P5~A6hxMcVUAgRkUgf6JRXPL4leWX1nh2OZvAdI7PuBLaWHCXJv74nFhWPa1yyOPwrpogW42uhJ8LPylNQPcEFSRwYhrHUKn0uc0xNrUUVS6~RW-~Mv6XlJmwJS9iQMuTOZDnBWrGJbYe8kO4Ip7BD4ABL5qiXvP79fVZVH7elgJXMhyStZroledoRaoRDDq94Z5BsntNIovpjWViDOjPZYQ93DU-fVlw__&Key-Pair-Id=APKAINTVSUGEWH5XD5UA',
         
         'https://s3-alpha-sig.figma.com/img/635e/24c0/f44761170f05f7b8cec0c164ffe3f7ab?Expires=1655683200&Signature=BIvqGrJR0mivzvnHByWlx9BWLr1fbB-cgt-S8LoCcHbfY~JzkSyr-ZWh~wkfMD0tKCjTOmt0w8VuJ-HVczK71OjN8RO3tDFbHp6eGoXr5yojTKEWyJ0AjmeuGVayFH4JD1XlFrEFLQ9bjlQ2HySXibZMMn2jDbVP7V6B3WVyAvE~GyuJNoKUe9ddaNWpbsJaa1xWzg6pooXLLFCId-G~Br96wqyt2OUAqEfew24DViDnzI9LOwRDbZqFInIRLGvHMqiBpk3nvwDlHfn1fTbEbi1kUgtw-VYo-NyfjlnI8o2vRAK-1W~cD4v09U~Va8GAA6SzB~OJlYDZOPoPIEep3g__&Key-Pair-Id=APKAINTVSUGEWH5XD5UA',

         'https://s3-alpha-sig.figma.com/img/c09f/748d/268d99ea6c0977fbecd771db9f691c3e?Expires=1655683200&Signature=dsSZMDrCFQoWOncNeGkjcbXg3V1WnxVlGvT7onetugggMSjtYRbSZDvpZ0m~amj5qtizut5g3nNkfIjn9thzGxjDVNn4XxKI~Q8LxUn9fAcLFnG1LZrZZW~FQDEnHoWg9XbSZzrXWxSPCk-YC1DNvZO2LZQM5VgvTjmC974jhEyfT5prFf2BhXkrl4FyyUM3Bs67whwZlyHMuXVajKqPYdFVraKd0Jx4WCRhd8SLIM-vRrsc0UmzJfmmrJeJwxxaeWxzCFtYaG1Soiq4CXKMdZEW5qSYKHBdgivY9XKuc7TbeaU2Qn1OQ98NRqEioJjU-lbSqc4gfCIntxnReLj2dA__&Key-Pair-Id=APKAINTVSUGEWH5XD5UA',

         'https://s3-alpha-sig.figma.com/img/9b88/f050/d77f5b07ac36cc4e4d17fb5f31806f2c?Expires=1655683200&Signature=HNAyVUbZQB70WTFFpmN0NVOWbBcfGnlFJ-kXWOk598GNPQ8pZKn23C0qV0rT-repDFDXCJ43WnfLz0VtCJ-nb~~NsFJBOouw4sCxZrToB7cV2MVkTTKjeHe7oED5V4JSuLuTxrjXVTbiiZTt7AIrDt27lBKOCKAWp75~h5QX~NNvDuaoTzcs5wV5SCYgy9xAS7namBF6fNM3xR05pwGMaqIvMFVIS3sbZ-9d~dSkYNDBLI6LKJsBNmBpUG44QkIQLajIZf6fI4VEEsL78HElwciA5zBATPJgFr0Q~lf0l3~w-jrR~rHNmqBvr7CK7D9ir40zluxt91myppkdWr7pwQ__&Key-Pair-Id=APKAINTVSUGEWH5XD5UA',
      
         'https://s3-alpha-sig.figma.com/img/25cb/0b30/9e70d171faf4dd65df375f6965a014e8?Expires=1655683200&Signature=KeIp9~vx1At3YMXNXbq-uBaMynBbocQX11YlsdzecYtoPXjS8haJkvquG3W5QA8sKAhvuFCh~um6z4pEhaARnCaKaiNq4FFFPVKJBin1dyr~SExzss46KSNcF6Y43c0rKA9k8QLJvETl0ecsgejQ7nSfjJXgyMoEHb9uYvHHBEKa4U9F2XTKViH8CWzv1f8XW~Xps2kfbo4T0rrsuUh9II2mS3pClxiscAGBzzVZcG1Pynm1FOOwozxsMnC7UEs~JqSFLsZin-UNvwUDGQJDlfZ1unc7QZocDaTTqE3WanWyffyoRbutWs0QPwYu8qhIubIjjIzOaan1cjx9jtVd8w__&Key-Pair-Id=APKAINTVSUGEWH5XD5UA',
        
          'https://s3-alpha-sig.figma.com/img/423f/b29d/f9abc1016fe45822c7a14f624eafd6c2?Expires=1655683200&Signature=UncReoOnJvjvykBbrv6a7IWuhP1jwdNKOYa8v9zzveL6y7ZiEMszltWOoXoQU78YRMlsFToNXL7bY-knqoFE6GSwNVyj9z5yPVJI7N87rkGTnky1PsNtYdGuNxIL43WXjdYBRG60Go-99lMzXgSSZPMoGTzISSXT-NTVDD9~-Bc7rmRYp3CV62W8Yx6ubg4zTzao287pS0dMvU9H3pZnNdXOvLQtVgokOnSmwOGXT6JzxM53hCkc-J15DzECcRYYV3wyBuu4MSufgF8158GA6GJRP1ArU2IFXDm3uKkcn68Qgzf27kR4atmDYxa5NkBawNU0X49n3cSU9QzL6WDlNQ__&Key-Pair-Id=APKAINTVSUGEWH5XD5UA',
          
          'https://s3-alpha-sig.figma.com/img/df74/0864/b039d5e3bc983722eb3c24062883dba7?Expires=1655683200&Signature=OPFNbh3MDqS2JhiCmA2~OZ1UtfWDHkHn9Ahix0RBz7tY~kham6U0VqKnhz5YNjai4ies1bS76d0ctby0qtTbnRA8jtC~8Rr~tjcNgMUn9kSmzCZFvL3-f5J0GKTaISYWjHqF2NkJcWwlSomyzQIyicxfoFBac2lgqleTPPWPUKhODrpvcfsx25geC4Leg506KJtVCdHHyunBXaKGqHJBYmcbXIevI72T2kMrUNACRgPOLOtjkOlRpfK0DB9o~uZ-rL1KMpuSw-2tj1g2PvWTXf7doYxLRb-cMz00QxLERwfLKn9jbyocPXJKy2mBsYFPyVzyY3gdX9l610m7GlU42w__&Key-Pair-Id=APKAINTVSUGEWH5XD5UA',
      
          'https://s3-alpha-sig.figma.com/img/2983/484c/02f1f51da2aaae1b0f190e4b632adfa8?Expires=1655683200&Signature=FWjgpyDh~mFLnbzDwQ5u7YmFqoWl-NwweByZegMp8mrFstjVU4v3IHmIwJCedHAulXyxpTq80vdNJnHf3ZfUB3QKGu9Mt0AW~gdAFsj-UXokcKLd4WKA0h0CyPK6wuNvj0vttdat18haQUp6v77iWcW3WDVi8owhcs5pOEsnMRN3HyvoU9nFKZ7T6jW5ABJvN5LCcLnlD0~wiORpFNY4ETRgQ58wRxDK~LqfETjreVfFlGTFKAVibWN~WvRvm1fS0pxkJCYJ1g2iB3UVMDHlgl21e-62dDsvSGLUO4XarMn6VCYjuBy~1RSFb6-~UAH2yYCWPpxml-KX~yNArLVOUg__&Key-Pair-Id=APKAINTVSUGEWH5XD5UA',  

          'https://s3-alpha-sig.figma.com/img/63bc/75b3/8447a61b88ed25bd53aa7c14faeb3d2e?Expires=1655683200&Signature=O5czRyIU~GJlvN8chAM2moyZCREblLC2PZN5RODim9fRBEhGEFDRP-KDLWusZagbupp-KmdaC9~nctCW7iNOi6-hsj6eiHgDT-vVDMOSSu-gpimcb3GDwQUkzMwIJhFAVzufRi4sEN9KpU7pB4BGZ1wSy3-sVC4vyyeZcVBtE1lOKPGTWoXw-iEszUKpOKvYAIBsrjqOUuCFBJNbD3A-sdDcnsqMzuw2XeVbXb4XRB1WBCieq2~-yLfONNn0o1ZswCFeekV4r1MiPui-tVpto4yspiSvuoFWWxEbdsebpdXGooEkNziXKhmeSqBU-RiAKkrKkZ0QbSVK4a10KPvHhQ__&Key-Pair-Id=APKAINTVSUGEWH5XD5UA',
       
          'https://s3-alpha-sig.figma.com/img/a617/2683/e30b1f7879b08f994fbcda8a22d172f4?Expires=1655683200&Signature=O4FDXabaU17fu3~to3qAmBIOc11hYIKp9X8tcwQo52BpMZL07H167murPmhlb1CIldfqNAuGL37~YYtaxGAH4sgTgBg2AXY8oZV4KQEgsPBk9arowsfJX5YwN3Qe3VPXmPL~gFsjm1h9htRsc-Y3taHGqJG1xqMh9vv3mJE6G7x8S4WiDPO1cBuuSktUvQ59xmlkmWY5QD83vgsiBVXgSGhEfexPynltX475XWvTWTqPoSD1-eLoU1SqyJPG3Ta7DHyGzggyOkxRuOtkeyJw1F7ZLOtl~bmSlAD-OLhQ1vm6kher7MbZybdZjQ6ytLY0rdXrikt3LLOq86dPfl~AUA__&Key-Pair-Id=APKAINTVSUGEWH5XD5UA',

          'https://s3-alpha-sig.figma.com/img/e6c4/e378/dda598c32858068ec7cebc8c26f9df6c?Expires=1655683200&Signature=PMuZ5TnqGrp4BhT3ELmwyqJWJ2UtmhD67jZuDtuLOCyx-4pBX5bMtVedaQMiMSKFnCYTDj3213cbVm~Au0-fce3MwngnaxjRSUEoum5r4RF0S7pVF7vrA6TE1YJp6w72p5nscgM4AJVxK~1RmGH-~YBgJd~h~NOmZBMNyLPjTmfHt9AEfGJ2QxFv1qrkgtHiLlo9o2nsOXtR9tM3-jQM4cuSyZPPDlqgRkpQlAhPaLneiqXepwbmTpdKXdz~~o-cXfWCrqkumibpE94am6hSfxQsMC8dRMQpEgvDrKIiID3wEi-wYGGXYqQIiBya3UixNvBWdJIdbB36chNAd2j8oQ__&Key-Pair-Id=APKAINTVSUGEWH5XD5UA',

          'https://s3-alpha-sig.figma.com/img/abad/8b66/af5c7ba0826d7cceb5d5b6782349fa7c?Expires=1655683200&Signature=MfzbamNr0A0i71WeaWaGpc~AsLZfUJ7iAqoQM5tKXeWTruD1uDHeEBoOq-AzPsiXY~ttftWDJZrrLFHEBf-JbwJzaS3B5qwkmcd3Z2usWya0cG7yOun83eOcCibsiQ06p7zgnNrs04tHBvs0Phzc9dk1YYfPDp2Z4bcenX7IZcufjA~5uNN1uv2R6XZkEx7I8u-YEYCAZ-ChHoWDB5yuyvBO-bx2fa9RE~XnEOO6nWVz~Gcen2Olo3Pc5xG1dNMcUP2hIlU5-mlUAqduh085Zile6cIebj7uig~YcMYWZ9TkbV2cFzezicBjymxF-O9Gwz9tkX-S3lCB~9QH-C7FyA__&Key-Pair-Id=APKAINTVSUGEWH5XD5UA'
      
      
      ],

      currentLanguage : null,  // Math.floor(Math.random()*(2)+0)
      currentIndex : null, //Math.floor(Math.random() * (11 - 0 + 1) + 0)
      currentWord : "",
      currentAnswer : "",
      givenAnswer : "",
      completed : [],
      total : 0
    }
  },
  methods:{
    startQuizz(){
      this.isStarted = true
    },

    enterAnswer(e){
      if (e.keyCode === 13){
        this.checkAnswer()
        this.total++
        this.givenAnswer = ""
        this.random()
      }
    },

    random(){
 
      let index;
      this.currentLanguage = Math.floor(Math.random()*(2) + 0)
      index = Math.floor(Math.random() * (11 - 0 + 1) + 0)
  
      while(this.completed.includes(index)){
        index = Math.floor(Math.random() * (11 - 0 + 1) + 0)
      } 

      if(this.currentLanguage==0){
        this.currentWord=this.english[index]
        this.currentAnswer = this.serbian[index]
      }
      if(this.currentLanguage==1){
        this.currentWord=this.serbian[index]
        this.currentAnswer = this.english[index]
      }
      this.currentIndex = index
  
    },

    checkAnswer(){
      if(this.givenAnswer===this.currentAnswer){
        this.completed.push(this.currentIndex)
        console.log(this.completed.length)

        if(this.completed.length==12){
          this.total = 0
          this.completed = []      
          this.isStarted == false          
        }
      }
    },

    showResults(){
      this.areResultsVisible = !this.areResultsVisible
    },


  }
  
  }
</script>


<style>

  body{
    background-color:#FF6700;
  }

  .header{
    width: 500px;
    height: 72px;
    position:relative;

    left: 710px;
    top: 50px;      
    
  }


  .quizz-table{
    background-color: #FFFFFF;
    width: 1280px;
    height: 827px;
    position: relative;
    top: 95px;
    left: 320px;
    border-radius: 10px;
  }

  .quest-img{
    width: 700px;
    height: 464px;
    position: relative;
    top: 25px;
    left: 300px;
    border-radius: 10px;
    offset: -6px 6px rgba(255,103,0,0.12);    
  }


  .quizz-input{
    width: 700px;
    height: 50px;
    position: relative;
    top: 25px;
    left: 300px;
    border-radius: 10px;    
    color:#FFFFFF;
    box-shadow: 10px 5px 5px #FF6700 0.3;
  }

  .given-answer{
    width: 66px;
    height: 28px;
    font-family: 'Roboto', sans-serif;
    font-weight: 600;
    font-size: 24px;
    line-height: 28.13px;
    color: rgba(34, 34, 34, 1);
    position: relative;
    top: 22px;
    left: 520px;    
  }


  .showBtn{
    width: 324px;
    height: 60px;
    font-family: 'Roboto', sans-serif;
    font-size: 24px;
    font-weight: 900;
    line-height: 28.13px;
    color: rgba(255, 255, 255, 1);
    background-color: rgba(255, 103, 0, 1);
    border-radius: 10px;
    position: relative;
    top: 44px;
    left: 500px;
  }

  .current-word{
    width: 66px;
    height: 28px;
    font-family: 'Roboto', sans-serif;
    font-weight: 600;
    font-size: 24px;
    line-height: 28.13px;
    color: rgba(34, 34, 34, 1);
    position: relative;
    top: 22px;
    left: 620px;    
  }


  .correct-answer{
    font-family: 'Roboto', sans-serif;
    font-weight: 400;
    font-size: 20px;
    color: rgba(255, 103, 0, 1);
    position: relative;
    top: 28px;
    left: 460px;  
  }

  .wrong-answer{
    font-family: 'Roboto', sans-serif;
    font-weight: 400;
    font-size: 20px;
    color: rgba(255, 103, 0, 1);
    position: relative;
    bottom: 60px;
    left: 840px;  

  }


  .title-left{
    font-weight: 100;
  }
  .title-right{
    font-weight: 700;
  }

  button{
    background-color:blueviolet
  }
  


    @media screen and (max-width:1280px){
      .header{
        position: relative;
        left: 430px;
        top: 50px}  
        
      .quizz-table{
        width: 1180px;
        height: 600px;        
        position: relative;
        top: 95px;
        left: 70px;        
      }  

      .quest-img{
        width: 400px;
        height: 265.14px;
        position: relative;
        top: 30px;
        left: 400px;
      }

      .quizz-input{
        width: 500px;
        height: 40px;
        position: relative;
        top: 7px;
        left: 350px;
      }

      .current-word{
        position: relative;
        top: 18px;   
        right: 100px;             
      }
  
      .given-answer{
        position: relative;
        top: 8px;
        left: 500px;
      }

      .showBtn{
        width: 250px;
        height: 50px;
        position: relative;
        top: 15px;
        left: 490px;        
      }

      .wrong-answer{
        position: relative;
        bottom: 60px;
        left: 750px;          
      }

      
    };


    @media screen and (max-width:380px){

      body{
        border-radius: 40px;
      }

      .header{
        width: 348px;
        height: 50px;         
        position: relative;
        left: 40px;
        top: 43px}  
      
      .quizz-table{
        border-radius: 40px;
        width: 348px;
        height: 454px;        
        position: relative;
        top: 95px;
        left: 115px;        
      } 

      .quest-img{
        width: 308px;
        height: 205px;
        position: relative;
        top: 50px;
        left: 22px;
      }

      .quizz-input{
        width: 208px;
        height: 36px;
        position: relative;
        top: 10px;
        left: 75px;
      }

      .showBtn{
        width: 208px;
        height: 40px;
        position: relative;
        top: 45px;
        left: 75px;        
      }

      .current-word{
        position: relative;
        top: 40px;   
        left: 140px;   
        font-size: 16px;                  
      }
  
      .given-answer{
        position: relative;
        top: 8px;
        left: 20px;
        font-size: 16px;
      }

      .showBtn{
        width: 250px;
        height: 50px;
        position: relative;
        top: 15px;
        left: 60px;        
      }

      .wrong-answer{
        position: relative;
        top: 5px;
        left: 70px;          
      }      
        


    };










</style>
