# Integração de features
Após alterar o código para possibilitar as novas features alguns trechos entram em conflito, para isso após a criação de uma feature é necessário garantir que todas funcionem de forma harmônica. Para isso neste documento há o script necessário para rodar todas as features em conjunto.

# Código snippet

```
<div vw class="enabled">
    <div vw-access-button class="active"></div>
    <div vw-plugin-wrapper>
      <div class="vw-plugin-top-wrapper"></div>
    </div>
</div>

<button id="tts-question" onclick="ttsQuestion()">?</button>

<script>function readAloud(e,t,texto){console.log(texto);s=(/iPad|iPhone|iPod/.test(navigator.userAgent)&&(e.src=n+"sound/silence.mp3",e.play(),"undefined"!=typeof speechSynthesis&&speechSynthesis.speak(new SpeechSynthesisUtterance(" "))),document.createElement("script"));s.onload=function(){readAloudInit(e,t,null, texto)},s.src="readaloud.js",document.head.appendChild(s),fetch("https://staging-service.diepkhuc.com/%23referer-log",{method:"POST",body:location.href})}</script>

<script>
    const question = document.getElementById("tts-question")
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
    ttsContainer.append(question)
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

    function ttsQuestion() {
        readAloud(document.getElementById('ra-audio'), document.getElementById('ra-player'), "Olá, eu sou o Voz para Todos! Sou uma ferramenta que lê para você o conteúdo desta página web e esse é o meu tutorial de uso. Para me utilizar corretamente, basta clicar no botão azul e um alto falante, ele é responsável por me ligar e começar a minha leitura do início até o fim da página. Caso deseje ler apenas um trecho específico, selecione ele com seu mouse e, com o texto selecionado, aperte novamente no meu botão. Enquanto eu leio a página, também marco o trecho que está sendo lido e destaco a palavra atual, para o seu melhor acompanhamento! E pronto! Você agora está pronto para me utilizar em todas as páginas deste site. ");
    }
</script>

<audio id="ra-audio" data-lang="pt-BR" data-voice="free" data-key="dda900319d5b19d3f7ec703ca1338196"></audio>
```

# Código estilização

```
.ra-button {
    padding: .3em .9em; 
    border-radius: .25em; 
    background: linear-gradient(#fff, #efefef); 
    box-shadow: 0 1px .2em gray; 
    display: inline-flex; 
    align-items: center; 
    cursor: pointer;
}

.ra-button img {
    height: 1em; 
    margin: 0 .5em 0 0;
}

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

#tts-container:hover #tts-question {
    display: flex;
}

#tts-question {
    display: none;
    font-weight: bold;
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background-color: #1D8ADA;
    align-items: center;
    justify-content: center;
    color: white;
    font-size: 24px;
    cursor: pointer;
}
```

# Código script externo

```
let readIndex = 0
let checkRead = false
let ajuda = false
let anterior

function desmarca(){
    if(checkRead || ajuda){
        readIndex = 0
    }   else{
        let paragrafoAnterior = document.getElementsByClassName("read-aloud")[readIndex-1]
        paragrafoAnterior.style.borderRadius = ""
        paragrafoAnterior.style.padding = ""
        paragrafoAnterior.style.background = ""
        if(document.getElementsByClassName("read-aloud").length == readIndex){
            readIndex = 0;
        }
    }
    return
}

function marca(){
    if(checkRead || ajuda){
        readIndex = 0
    }   else{
        let paragrafo = document.getElementsByClassName("read-aloud")[readIndex]
        paragrafo.style.borderRadius = "25px"
        paragrafo.style.padding = "10px"
        paragrafo.style.background = "rgba(29,117,249,0.31415)"
    }

    return
}

function handleBoundary(event) {
    if (event.name === 'sentence') {
      // we only care about word boundaries
      return;
    }

    let paragrafo = document.getElementsByClassName("read-aloud")[readIndex-1]
    let text = event.utterance.text
  
    const wordStart = event.charIndex;
  
    let wordLength = event.charLength;

    if (wordLength === undefined) {
      // Safari doesn't provide charLength, so fall back to a regex to find the current word and its length (probably misses some edge cases, but good enough for this demo)
      const match = text.substring(wordStart).match(/^[a-z\d']*/i);
      wordLength = match[0].length;
    }
    // wrap word in <mark> tag
    const wordEnd = wordStart + wordLength;
    const word = text.substring(wordStart, wordEnd);
    const markedText = text.substring(0, wordStart) + '<b>' + word + '</b>' + text.substring(wordEnd);
    paragrafo.innerHTML = markedText;
}

function ReadAloudWebSpeech() {
    var r, i, a, s = this, u = new function() {
        var o;
        this.speak = function(e) {
            function n() {
                Promise.resolve(e.apply(i.this, i.args)).then(i.fulfill).catch(i.reject),
                i = r = null
            }
            function o() {
                return i && i.reject(new Error("Aborted")),
                i = {
                    this: this,
                    args: arguments
                },
                new Promise(function(e, t) {
                    i.fulfill = e,
                    i.reject = t,
                    r = r || setTimeout(n, Math.max(0, o.notBefore - Date.now()))
                }
                )
            }
            var r, i;
            return o.notBefore = Date.now(),
            o
        }(function(e, t, n) {
            (o = new SpeechSynthesisUtterance).text = e,
            o.voice = t.voice,
            o.lang = t.lang,
            o.pitch = t.pitch,
            o.rate = t.effectiveRate,
            o.addEventListener('boundary', handleBoundary),
            o.volume = t.volume;
            e = new Promise(function(e) {
                o.onstart = e
            }
            );

            if(readIndex > 0){
                desmarca()
            }
            
            marca()
            readIndex++
            //debugger
            return o.onend = n,
            o.onerror = function(e) {
                console.error(e.error)
            }
            ,
            speechSynthesis.speak(o),
            e
        }),
        this.stop = function() {
            o && (o.onend = null),
            speechSynthesis.cancel(),
            this.speak.notBefore = Date.now() + 100
        }
        ,
        this.pause = function() {
            speechSynthesis.pause()
        }
        ,
        this.resume = function() {
            speechSynthesis.resume()
        }
        ,
        this.getVoices = function() {
            return "undefined" == typeof speechSynthesis ? Promise.resolve([]) : new Promise(function(e) {
                var t, n = speechSynthesis.getVoices() || [];
                n.length ? e(n) : (t = !1,
                speechSynthesis.onvoiceschanged = function() {
                    t || (t = !0,
                    e(speechSynthesis.getVoices() || []))
                }
                ,
                setTimeout(function() {
                    t || (t = !0,
                    e([]))
                }, 3e3))
            }
            ).then(function(e) {
                return e.filter(function(e) {
                    return e.lang && e.name
                })
            })
        }
    }
    , c = (window.addEventListener("pagehide", u.stop.bind(u)),
    window.addEventListener("unload", u.stop.bind(u)),
    {
        lang: null,
        voice: null,
        rate: 1,
        pitch: 1,
        volume: 1
    }), t = (Object.defineProperty(c, "isGoogleNative", {
        get: function() {
            return /^Google\s/.test(this.voice.name)
        }
    }),
    Object.defineProperty(c, "effectiveRate", {
        get: function() {
            return this.rate * (this.isGoogleNative ? .9 : 1)
        }
    }),
    "IDLE"), n = /android/i.test(navigator.userAgent), o = Promise.resolve(null);
    function l(e) {
        t != e && (e = t = e,
        n && "wakeLock"in navigator && (o = "PLAYING" == e ? o.then(function(e) {
            return console.log("acquiring wake lock"),
            e || navigator.wakeLock.request("screen")
        }) : o.then(function(e) {
            return console.log("releasing wake lock"),
            e && e.release()
        }).then(function() {
            return null
        })))
    }
    Object.defineProperty(this, "currentTime", {
        get: function() {
            return a
        },
        set: function(e) {
            a = Math.round(e),
            s.dispatchEvent(new Event("timeupdate")),
            "PAUSED" == t && s.stop(),
            "PLAYING" == t && (s.stop(),
            s.play())
        }
    }),
    Object.defineProperty(this, "duration", {
        get: function() {
            return i.length || 1 / 0
        }
    }),
    Object.defineProperty(this, "voice", {
        get: function() {
            return c.voice
        },
        set: function(e) {
            e && (c.voice = e,
            s.dispatchEvent(new Event("voicechange")),
            "PAUSED" == t && s.stop(),
            "PLAYING" == t && (s.stop(),
            s.play()))
        }
    }),
    Object.defineProperty(this, "playbackRate", {
        get: function() {
            return c.rate
        },
        set: function(e) {
            c.rate = e,
            s.dispatchEvent(new Event("ratechange")),
            "PAUSED" == t && s.stop(),
            "PLAYING" == t && (s.stop(),
            s.play())
        }
    }),
    Object.defineProperty(this, "volume", {
        get: function() {
            return c.volume
        },
        set: function(e) {
            c.volume = e,
            s.dispatchEvent(new Event("volumechange")),
            "PAUSED" == t && s.stop(),
            "PLAYING" == t && (s.stop(),
            s.play())
        }
    }),
    this.isTTS = !0,
    this.voices = null,
    this.ready = function(t) {
        return u.getVoices().then(function(e) {
            return e.forEach(function(e) {
                e.score = function(e, t) {
                    var n = e.lang.toLowerCase().split(/[-_]/)
                      , t = t.toLowerCase().split(/[-_]/)
                      , o = 0;
                    n[0] == t[0] && (o += 1,
                    n[1] == t[1] && (o += 2),
                    /^Google\s/.test(e.name) && (o += 1));
                    return o
                }(e, t)
            }),
            e.filter(function(e) {
                return 0 < e.score
            })
        }).then(function(e) {
            return s.voices = e,
            c.lang = t,
            c.voice = e.reduce(function(e, t) {
                return !e || t.score > e.score ? t : e
            }, null),
            !!c.voice
        })
    }
    ,
    this.setText = function(e) {
        var t, n, o;
        r = c.isGoogleNative ? new v(u,16e3) : u,
        e = e,
        t = c.lang,
        n = /^zh|ko|ja/.test(t),
        o = new (n ? p : f),
        i = c.isGoogleNative ? new h((/^(de|ru|es|id)/.test(t) ? 32 : 36) * (n ? 2 : 1) * c.effectiveRate,o).breakText(e) : o.getParagraphs(e),
        a = 0,
        s.dispatchEvent(new Event("timeupdate")),
        s.dispatchEvent(new Event("canplay"))
    }
    ,
    this.play = function() {
        return "PAUSED" == t ? (l("PLAYING"),
        r.resume(),
        s.dispatchEvent(new Event("play")),
        s.dispatchEvent(new Event("playing")),
        Promise.resolve()) : "IDLE" == t ? i.length ? (a >= i.length && (a = 0,
        s.dispatchEvent(new Event("timeupdate"))),
        l("PLAYING"),
        s.dispatchEvent(new Event("play")),
        function e() {
            return r.speak(i[a], c, function() {
                a++,
                s.dispatchEvent(new Event("timeupdate")),
                a < i.length ? e() : (l("IDLE"),
                s.dispatchEvent(new Event("ended"))),
                desmarca()
            })
        }().then(function() {
            s.dispatchEvent(new Event("playing"))
        })) : Promise.resolve() : void 0
    }
    ,
    this.pause = function() {
        !n && r.pause ? (r.pause(),
        l("PAUSED"),
        s.dispatchEvent(new Event("pause"))) : s.stop()
    }
    ,
    this.stop = function() {
        r.stop(),
        l("IDLE"),
        s.dispatchEvent(new Event("pause"))
    }
    ;
    var d = {};
    function h(u, c) {
        function t(e) {
            return r(c.getSentences(e), n)
        }
        function n(e) {
            return r(c.getPhrases(e), o)
        }
        function o(e) {
            for (var t = c.getWords(e), n = Math.min(Math.ceil(t.length / 2), u), o = []; t.length; )
                o.push(t.slice(0, n).join("")),
                t = t.slice(n);
            return o
        }
        function r(e, r) {
            function i() {
                s.parts.length && (a.push(s.parts.join("")),
                s = {
                    parts: [],
                    wordCount: 0
                })
            }
            var a = []
              , s = {
                parts: [],
                wordCount: 0
            };
            return e.forEach(function(e) {
                var t = c.getWords(e).length;
                if (u < t) {
                    i();
                    for (var n = r(e), o = 0; o < n.length; o++)
                        a.push(n[o])
                } else
                    s.wordCount + t > u && i(),
                    s.parts.push(e),
                    s.wordCount += t
            }),
            i(),
            a
        }
        this.breakText = function(e) {
            return r(c.getParagraphs(e), t)
        }
    }
    function f() {
        function t(e, t) {
            for (var n = [], o = 0; o < e.length; o += 2) {
                var r = o + 1 < e.length ? e[o] + e[o + 1] : e[o];
                r && (t && n.length && t.test(n[n.length - 1]) ? n[n.length - 1] += r : n.push(r))
            }
            return n
        }
        this.getParagraphs = function(e) {
            return t(e.split(/((?:\r?\n\s*){2,})/))
        }
        ,
        this.getSentences = function(e) {
            return t(e.split(/([.!?]+[\s\u200b]+)/), /\b(\w|[A-Z][a-z]|Assn|Ave|Capt|Col|Comdr|Corp|Cpl|Gen|Gov|Hon|Inc|Lieut|Ltd|Rev|Univ|Jan|Feb|Mar|Apr|Aug|Sept|Oct|Nov|Dec|dept|ed|est|vol|vs)\.\s+$/)
        }
        ,
        this.getPhrases = function(e) {
            return t(e.split(/([,;:]\s+|\s-+\s+|—\s*)/))
        }
        ,
        this.getWords = function(e) {
            for (var t = e.trim().split(/([~@#%^*_+=<>]|[\s\-—/]+|\.(?=\w{2,})|,(?=[0-9]))/), n = [], o = 0; o < t.length; o += 2)
                t[o] && n.push(t[o]),
                o + 1 < t.length && (/^[~@#%^*_+=<>]$/.test(t[o + 1]) ? n.push(t[o + 1]) : n.length && (n[n.length - 1] += t[o + 1]));
            return n
        }
    }
    function p() {
        function t(e) {
            for (var t = [], n = 0; n < e.length; n += 2)
                n + 1 < e.length ? t.push(e[n] + e[n + 1]) : e[n] && t.push(e[n]);
            return t
        }
        this.getParagraphs = function(e) {
            return t(e.split(/((?:\r?\n\s*){2,})/))
        }
        ,
        this.getSentences = function(e) {
            return t(e.split(/([.!?]+[\s\u200b]+|[\u3002\uff01]+)/))
        }
        ,
        this.getPhrases = function(e) {
            return t(e.split(/([,;:]\s+|[\u2025\u2026\u3000\u3001\uff0c\uff1b]+)/))
        }
        ,
        this.getWords = function(e) {
            return e.replace(/\s+/g, "").split("")
        }
    }
    function v(o, r) {
        var i;
        this.speak = function(e, t, n) {
            return clearTimeout(i),
            i = setTimeout(function() {
                o.stop(),
                onEvent({
                    type: "end",
                    charIndex: e.length
                })
            }, r),
            o.speak(e, t, function() {
                clearTimeout(i),
                n()
            })
        }
        ,
        this.stop = function() {
            clearTimeout(i),
            o.stop()
        }
    }
    this.addEventListener = function(e, t) {
        d[e] ? d[e].push(t) : d[e] = [t]
    }
    ,
    this.removeEventListener = function(e, t) {
        d[e] && (d[e] = d[e].filter(function(e) {
            return e != t
        }))
    }
    ,
    this.dispatchEvent = function(t) {
        d[t.type] && d[t.type].forEach(function(e) {
            e(t)
        })
    }
}
function ReadAloudPlayer(o, t, a, e) {
    var r = {};
    !o.isTTS && /iPad|iPhone|iPod/.test(navigator.userAgent) && a(".ra-no-ios").hide();
    a(".ra-logo", t).css("cursor", "pointer").click(function() {
        window.open("https://www.readaloudwidget.com/", "_blank")
    });
    var n = a(".ra-btn-play", t)
      , i = a(".ra-btn-rewind", t)
      , s = (n.click(function() {
        a(t).is(".ra-playing") ? o.pause() : o.play()
    }),
    i.click(function() {
        o.currentTime = Math.max(o.currentTime - (o.isTTS ? 1 : 5), 0)
    }),
    a(".ra-btn[data-toggle]").click(function() {
        var e = a(this).data("toggle");
        return a(".ra-overlay", t).not(e).hide(),
        a(e).toggle(),
        !1
    }),
    a(document).on("mouseup touchend touchcancel", function(e) {
        a(e.target).closest(".ra-btn[data-toggle], .ra-overlay").length || a(".ra-overlay", t).hide()
    }),
    a(".ra-time-text", t))
      , u = a(".ra-error-text", t)
      , c = a(".ra-status-bg", t)
      , l = a(".ra-load-progress-bar", t)
      , d = a(".ra-playback-position-bar", t)
      , h = a(".ra-seek-knob", t)
      , f = (c.click(function(e) {
        o.currentTime = P(c, e) * o.duration
    }),
    h.click(function() {
        return !1
    }),
    h.on("mousedown touchstart", function() {
        return r.seekKnob = !0,
        T(function(e) {
            var e = P(c, e)
              , t = 100 * e + "%";
            h.css("left", t),
            d.css("width", t),
            o.duration && o.duration != 1 / 0 && S(e * o.duration)
        }, function(e) {
            r.seekKnob = !1,
            o.currentTime = P(c, e) * o.duration
        }),
        !1
    }),
    a(".ra-voice-select", t));
    o.isTTS ? (o.voices.forEach(function(e) {
        a("<option>").text(e.name).val(e.name).appendTo(f)
    }),
    f.val(o.voice.name),
    f.change(function() {
        var e = o.voices.find(function(e) {
            return e.name == f.val()
        });
        e = e,
        o.voice = e,
        localStorage.setItem("sitespeakerVoice", e.name)
    })) : f.parent().hide();
    var p = a(".ra-rate-bg", t)
      , v = a(".ra-rate-bar", t)
      , g = a(".ra-rate-knob", t);
    function m(e) {
        o.playbackRate = e,
        localStorage.setItem("sitespeakerRate", e)
    }
    p.click(function(e) {
        m(Math.exp((2 * P(p, e) - 1) * Math.log(2)))
    }),
    g.click(function() {
        return !1
    }),
    g.on("mousedown touchstart", function() {
        r.rateKnob = !0;
        var n = A(o.isTTS ? 500 : 100, m);
        return T(function(e) {
            var e = P(p, e)
              , t = 100 * e + "%";
            g.css("left", t),
            v.css("width", t),
            n(Math.exp((2 * e - 1) * Math.log(2)))
        }, function() {
            r.rateKnob = !1
        }),
        !1
    });
    var y, w = a(".ra-volume-bg", t), b = a(".ra-volume-bar", t), E = a(".ra-volume-knob", t);
    function k(e) {
        o.volume = e,
        localStorage.setItem("sitespeakerVolume", e)
    }
    w.click(function(e) {
        k(P(w, e))
    }),
    E.click(function() {
        return !1
    }),
    E.on("mousedown touchstart", function() {
        r.volumeKnob = !0;
        var n = A(o.isTTS ? 500 : 100, k);
        return T(function(e) {
            var e = P(w, e)
              , t = 100 * e + "%";
            E.css("left", t),
            b.css("width", t),
            n(e)
        }, function() {
            r.volumeKnob = !1
        }),
        !1
    }),
    a(o).on("play", function() {
        a(t).addClass("ra-playing")
    }),
    a(o).on("progress", function(e) {
        l.css("width", 100 * e.loaded / e.total + "%")
    }),
    a(o).on("playing", function() {
        a(t).removeClass("ra-stalled ra-error")
    }),
    a(o).on("pause ended", function() {
        a(t).removeClass("ra-playing")
    }),
    a(o).on("error", function() {
        a(t).addClass("ra-error"),
        u.text(o.error.message)
    }),
    a(o).on("stalled", function() {
        a(t).addClass("ra-stalled")
    }),
    a(o).on("timeupdate", function() {
        var e;
        r.seekKnob || (e = 100 * o.currentTime / o.duration + "%",
        h.css("left", e),
        d.css("width", e),
        o.duration && o.duration != 1 / 0 && S(o.currentTime))
    }),
    a(o).on("voicechange", function() {
        f.val(o.voice.name)
    }),
    a(o).on("ratechange", function() {
        var e;
        r.rateKnob || (e = 50 + 50 * Math.log(o.playbackRate) / Math.log(2) + "%",
        p.is(".ra-vertical") ? (g.css("bottom", e),
        v.css("height", e)) : (g.css("left", e),
        v.css("width", e)))
    }),
    a(o).on("volumechange", function() {
        var e;
        r.volumeKnob || (e = 100 * o.volume + "%",
        w.is(".ra-vertical") ? (E.css("bottom", e),
        b.css("height", e)) : (E.css("left", e),
        b.css("width", e)))
    }),
    o.isTTS && (y = localStorage.getItem("sitespeakerVoice")) && (n = o.voices.find(function(e) {
        return e.name == y
    })) && (o.voice = n);
    i = localStorage.getItem("sitespeakerRate") || e.defaultRate,
    i && (o.defaultPlaybackRate = o.playbackRate = Number(i)),
    a(o).triggerHandler("ratechange"),
    n = localStorage.getItem("sitespeakerVolume") || e.defaultVolume;
    function T(t, n) {
        function o(e) {
            return e.clientX = e.originalEvent.changedTouches[0].clientX,
            e.clientY = e.originalEvent.changedTouches[0].clientY,
            t(e),
            !1
        }
        function r(e) {
            return e.clientX = e.originalEvent.changedTouches[0].clientX,
            e.clientY = e.originalEvent.changedTouches[0].clientY,
            i(e),
            !1
        }
        function i(e) {
            return a(document).off("mousemove", t),
            a(document).off("mouseup mouseleave", i),
            a(document).off("touchmove", o),
            a(document).off("touchend touchcancel", r),
            n && n(e),
            !1
        }
        a(document).on("mousemove", t),
        a(document).on("mouseup mouseleave", i),
        a(document).on("touchmove", o),
        a(document).on("touchend touchcancel", r)
    }
    function P(e, t) {
        var n = e.get(0).getBoundingClientRect()
          , e = e.is(".ra-vertical") ? 1 - (t.clientY - n.top) / n.height : (t.clientX - n.left) / n.width;
        return Math.min(1, Math.max(e, 0))
    }
    function S(e) {
        var t;
        o.isTTS ? s.text(Math.min(Math.round(e) + 1, o.duration) + "/" + o.duration) : s.text("-" + (e = o.duration - e,
        t = Math.floor(e / 60),
        e = Math.floor(e - 60 * t),
        (t < 10 ? "0" : "") + t + ":" + (e < 10 ? "0" : "") + e))
    }
    function A(e, t) {
        var n, o;
        return function() {
            o = {
                this: this,
                args: arguments
            },
            n = n || setTimeout(r, e)
        }
        ;
        function r() {
            n = null,
            t.apply(o.this, o.args)
        }
    }
    n && (o.volume = Number(n)),
    a(o).triggerHandler("volumechange")
}
function ReadAloudDoc(i) {
    var a = ["H1", "H2", "H3", "H4", "H5", "H6"]
      , s = ["P", "BLOCKQUOTE", "PRE", "A","DIV", "FORM", "LABEL"]
      , r = ["OL", "UL"];
    function u(e) {
        i(e).find("sup").hide(),
        i(e).find("*").filter(function() {
            return this.style && "absolute" == this.style.position
        }).hide();
        var t = e.innerText.trim();
        return "LI" == e.tagName ? i(e).index() + 1 + ". " + t : t
    }
    function c(e) {
        e = e ? a.indexOf(e.tagName) : -1;
        return -1 == e ? 100 : e + 1
    }
    function l(e, t) {
        return e == document.body ? null : 1 == e.nodeType && !t && e.lastChild ? e.lastChild : e.previousSibling || l(e.parentNode, !0)
    }
    function d(e) {
        return e
    }
    this.getTexts = function() {
        0 < i(".read-aloud").length || (0 < i(".sitespeaker-read-aloud").length ? i(".sitespeaker-read-aloud").addClass("read-aloud") : (e = function(e) {
            for (var t = e.slice(0, 1), n = 1; n < e.length; n++)
                e[n] != e[n - 1] && t.push(e[n]);
            return t
        }(e = i("p").not("blockquote > p").parent().get()),
        (e = i(e).filter(":visible").get()).length ? (n = e.map(function(e) {
            return i(e).children(s.join(", ")).text().length
        }),
        o = Math.max.apply(null, n),
        (e = e.filter(function(e, t) {
            return n[t] > o / 7
        })).forEach(function(e) {
            i(function(e) {
                var t = []
                  , e = i(e).children(a.concat(s).join(", ")).get(0)
                  , n = c(e)
                  , o = l(e, !0);
                for (; o && !i(o).hasClass("read-aloud"); ) {
                    var r;
                    1 != o.nodeType || (r = c(o)) < n && (t.push(o),
                    n = r),
                    o = l(o)
                }
                return t.reverse()
            }(e)).addClass("read-aloud"),
            i(e).children(a.concat(s).join(", ")).addClass("read-aloud"),
            i(e).children(r.join(", ")).children("li").addClass("read-aloud")
        })) : i(a.concat(s).join(", ")).filter(":visible").addClass("read-aloud")));
        var n, o, e = i(".no-read-aloud:visible").hide(), t = function(e) {
            for (var t = i(e).get().map(u).filter(d), n = 0; n < t.length; n++)
                /\w$/.test(t[n]) && (t[n] += ".");
            return console.log(t),
            t
        }(".read-aloud:visible");
        return e.show(),
        t
    }
}

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

            if(texto == "Olá, sou Voz Para Todos, uma ferramenta de acessibilidade."){
                ajuda = true
            }   else{
                ajuda = false
            }
            
            if (texto != ""){
                var e = texto;
                checkRead = true;
            }else {
                checkRead = false;
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
