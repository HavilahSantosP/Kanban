<template>
    <html lang="pt-br">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <link rel="stylesheet" href="Style.css">
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
                <a href="Site2.html"><button>CADASTRAR</button></a>
            </div>
            <div class="texto">
                Já tem uma Conta? <a href="App.vue" class="tipoS">Fazer Login</a>
            </div>
        </div>
        <div class="imagens">
            <div class="imagem1">
                <img src="../src/assets/Tela1.png" alt="">
            </div>
            <div class="imagem2">
                <img src="../src/assets/Tela1.png" alt="">
            </div>
            <div class="imagem3">
                <img src="../src/assets/Tela1.png" alt="">
            </div>
            <div class="imagem4">
                <img src="../src/assets/Tela1.png" alt="">
            </div>
            <div class="imagem5">
                <img src="../src/assets/Tela1.png" alt="">
            </div>
            <div class="imagem6">
                <img src="../src/assets/Tela1.png" alt="">
            </div>
        </div>    
    </body>
    </html>
</template>

<style scoped>
    body{
        background-color: rgb(33, 33, 33);
        font-family: 'Courier New', Courier, monospace;
        font-style: italic;
        height: 100%;
        width: 100%;
        zoom: 100%;
        overflow: hidden;
        color: white;
        text-align: center;
        
    }
    .inicio{
        display: flex;
        justify-content: center;
    }
    .titulo{
        font-size: 20px;
        padding: 6%;
        z-index: 1;

        

    }
    .boxt{
        z-index: 1;
    }
    .boxt h2{
        font-size: 30px ;
        z-index: 1;

    }
    .buton{
        width: 80vh;
        height: 8vh;
        border-style: none;
        border-radius: 30px;
        font-size: 26px;
        font-family:'Times New Roman', Times, serif;
        z-index: 1;

        
    }
    .buton button{
        z-index: 1;
    }
    .botao1 button{
        font-size: 22px;
        position: relative;
        top: 5vh;
        border-radius: 30px;
        height: 8vh;
        width: 26vh;
        font-family: 'Courier New', Courier, monospace;
        font-style: italic;
        z-index: 1;

    }
    .botao1 button:hover{
        color: rgb(255, 255, 255);
        cursor: pointer;
        border-color: rgb(255, 255, 255);
        background-color: black;
        transition: color 0.5s;

    }
    .texto{
        position: relative;
        top: 6vh; 
        color: white;
        z-index: 1;


    }
    .tipoS{
        text-decoration: underline;
    }   
    .texto a:hover{
        transition: 0.5s;
        color: aqua;
    }
    .imagens img{
        position: relative;
        right: 30vh;
        bottom: 90vh;
        z-index: -1;
        width: 185vh;
    
    }
    .imagem1 img{
        animation: go-back 3s infinite alternate-reverse; 
    }
    .imagem3 img{
        animation: go-back 3s infinite alternate-reverse; 
    }
    .imagem5 img{
        animation: go-back 3s infinite alternate-reverse; 
    }
    @keyframes go-back {
        from {
        transform: translatex(-60px);
        }
        to {
        transform: translatex(60px);
        }
    }
    .imagem2 img{
        animation: go-out 3s infinite alternate-reverse; 
    }
    .imagem4 img{
        animation: go-out 3s infinite alternate-reverse; 
    }
    .imagem6 img{
        animation: go-out 3s infinite alternate-reverse; 
    }
    @keyframes go-out {
        from {
        transform: translatex(60px);
        }
        to {
        transform: translatex(-60px);
        }
    }
</style>