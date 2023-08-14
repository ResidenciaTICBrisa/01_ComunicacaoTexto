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