# Traduttore-di-parolacce-
<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Traduttore di Parolacce Buffe</title>
<style>
  body { font-family: Arial; text-align: center; padding: 50px; background: #fefbd8; }
  input, button { font-size: 16px; padding: 10px; margin: 10px; }
  .result { margin-top: 20px; font-weight: bold; color: #2e8b57; font-size: 18px; }
</style>
</head>
<body>
<h1>Traduttore di Parolacce Buffe</h1>
<input type="text" id="wordInput" placeholder="Scrivi qui...">
<button onclick="translateWord()">Traduci!</button>
<div class="result" id="result"></div>

<script>
function translateWord() {
  const word = document.getElementById('wordInput').value.trim();
  if(!word) { document.getElementById('result').innerText = "Scrivi qualcosa!"; return; }
  const funnyWords = ["💥"+word+"💥", "😜"+word+"😜", word.split('').reverse().join('')+"🤪"];
  const randomIndex = Math.floor(Math.random()*funnyWords.length);
  document.getElementById('result').innerText = funnyWords[randomIndex];
}
</script>
</body>
</html>
