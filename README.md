# Lingofy-
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Lingofy – Language Made Easy</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
:root{
  --bg:#ffffff;
  --text:#000;
  --card:#f2f2f2;
}
.dark{
  --bg:#121212;
  --text:#fff;
  --card:#1f1f1f;
}
body{
  margin:0;
  font-family:Arial,sans-serif;
  background:var(--bg);
  color:var(--text);
}
.container{
  max-width:540px;
  margin:25px auto;
  background:var(--card);
  padding:20px;
  border-radius:12px;
}
.logo{
  text-align:center;
  font-size:28px;
  font-weight:bold;
}
.tagline{
  text-align:center;
  font-size:14px;
  margin-bottom:10px;
}
textarea{
  width:100%;
  height:120px;
  padding:10px;
  font-size:16px;
}
button{
  width:100%;
  padding:12px;
  margin-top:8px;
  font-size:16px;
  border:none;
  border-radius:6px;
  cursor:pointer;
}
.translate{background:#28a745;color:#fff;}
.copy{background:#007bff;color:#fff;}
.voice{background:#6f42c1;color:#fff;}
.darkbtn{background:#000;color:#fff;}
.grammar{background:#fd7e14;color:#fff;}
.output{
  margin-top:10px;
  padding:10px;
  min-height:60px;
  background:#fff;
  color:#000;
}
.history{
  margin-top:15px;
  font-size:14px;
}
footer{
  text-align:center;
  margin-top:15px;
  font-size:13px;
}
</style>
</head>

<body id="body">

<div class="container">
  <div class="logo">Lingofy</div>
  <div class="tagline">Language Made Easy</div>

  <textarea id="inputText" placeholder="Type Hindi or English..."></textarea>

  <button class="voice" onclick="startVoice()">🎤 Voice Input</button>
  <button class="translate" onclick="translateText()">Auto Translate</button>
  <button class="grammar" onclick="checkGrammar()">Grammar Check (English)</button>
  <button class="copy" onclick="copyText()">Copy Result</button>
  <button class="darkbtn" onclick="toggleDark()">Dark Mode</button>

  <div class="output" id="outputText">Result will appear here...</div>

  <div class="history">
    <b>History:</b>
    <ul id="historyList"></ul>
  </div>

  <footer>© 2026 Lingofy</footer>
</div>

<script>
function detectLanguage(text){
  return /[\u0900-\u097F]/.test(text) ? "hi" : "en";
}

async function translateText(){
  let text=inputText.value;
  if(!text) return alert("Text लिखिए");

  let fromLang = detectLanguage(text);
  let toLang = fromLang==="hi" ? "en" : "hi";

  let url=`https://api.mymemory.translated.net/get?q=${encodeURIComponent(text)}&langpair=${fromLang}|${toLang}`;
  let res=await fetch(url);
  let data=await res.json();

  outputText.innerText=data.responseData.translatedText;
  addHistory(text,data.responseData.translatedText);
}

async function checkGrammar(){
  let text=inputText.value;
  if(!text) return alert("English text लिखिए");

  let lang=detectLanguage(text);
  if(lang!=="en"){
    alert("Grammar check सिर्फ English के लिए है");
    return;
  }

  let url=`https://api.languagetool.org/v2/check`;
  let formData=new FormData();
  formData.append("text",text);
  formData.append("language","en-US");

  let res=await fetch(url,{method:"POST",body:formData});
  let data=await res.json();

  if(data.matches.length===0){
    outputText.innerText="✅ No grammar mistakes found";
  }else{
    outputText.innerText="❌ Grammar issues found: "+data.matches.length;
  }
}

function copyText(){
  navigator.clipboard.writeText(outputText.innerText);
  alert("Copied!");
}

function toggleDark(){
  body.classList.toggle("dark");
}

function addHistory(input,output){
  let li=document.createElement("li");
  li.innerText=input+" → "+output;
  historyList.prepend(li);
}

function startVoice(){
  if(!('webkitSpeechRecognition'in window)){
    alert("Voice not supported");
    return;
  }
  let rec=new webkitSpeechRecognition();
  rec.lang="en-IN";
  rec.onresult=e=>{
    inputText.value=e.results[0][0].transcript;
  };
  rec.start();
}
</script>

</body>
</html>
