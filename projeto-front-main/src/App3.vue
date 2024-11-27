<template>
  <html lang="pt-br">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <link rel="stylesheet" href="estilo.css">
      <title>Kanban's</title>
  </head>
  <body>
      <div class="incio">
          <div class="titulo">
              <h1>KANBAN'S</h1>
          </div>
          <div class="boxt">
              <h2>Email</h2>
              <input type="text" class="buton">
              <h2>Senha</h2>
              <input type="text" class="buton">
          </div>
          <div class="botao1">
              <a href="index.html"><button>ENTRAR</button></a>
          </div>
          <div class="texto">
              Não tem uma Conta? <a href="Site.vue" class="tipoS">Criar Conta</a>
          </div>
      </div>
      <div>
          <img src="../src/assets/Screendown.png" alt="Telamovimentada">
      </div>
  </body>
  </html>
</template>

<style scoped>
  body{
      background-color: white;
      font-family: 'Courier New', Courier, monospace;
      font-style: italic;
      display: flex;
      height: 100%;
      width: 100%;
      zoom: 100%;
      overflow: hidden;
  }
  body button{
      font-family: 'Courier New', Courier, monospace;
  }
  .incio{
      height: 100%;
      width: 100%;
  }
  .titulo{
      font-size: 26px;
      position: relative;
      color: white;
      right: 20vh;
      top: 6%;
      padding: 30%;
      

      
  }
  .boxt h2{
      position: relative;
      right: 60%;
      top: 40px;
      color: white;


      
  }
  .buton{
      position: relative;
      right: 70%;
      top: 40px;
      width: 150%;
      height: 8vh;
      border-radius: 30px;
      border-style: none;
      font-size: 28px;
      font-family:'Times New Roman', Times, serif;
      
  }

  .botao1{
      position: relative;
      top: 100px;
      right: 20%;
      width: 200%;
      

  }
  .botao1 button{
      font-size: 30px;
      font-style: italic;
      border-radius: 30px;
      height: 8vh;
      width: 20%;
  }
  .botao1 button:hover{
      color: rgb(255, 255, 255);
      cursor: pointer;
      border-color: rgb(255, 255, 255);
      background-color: black;
      transition: color 0.5s;

  }
  .texto{
      color: white;
      position: relative;
      right:33%;
      top: 12vh;
         
  }
  .tipoS{
      text-decoration: none;
  }
  .texto a:hover{
      transition: 0.5s;
      color: aqua;
  }
  img{
      position: relative;
      height: 200%;
      left: 190%;
      bottom: 250vh;
      animation: go-back 3s infinite alternate-reverse;
      width: 185%;

      
     
  }
  @keyframes go-back {
      from {
        transform: translatey(100px);
      }
      to {
        transform: translatey(0);
      }
  }

</style>