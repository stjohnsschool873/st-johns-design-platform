# st-johns-design-platform
A simple and interactive A4 School Digital Poster Creator where students can design posters using ready-made templates or a blank canvas. Add text, upload images, customize colours, and download posters as PNG. No AI required.
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>St John's School | Poster Creator</title>
<style>
*{box-sizing:border-box}
body{
 margin:0;font-family:Arial,sans-serif;
 background:#f1f3ff;color:#202044;
}
header{
 background:linear-gradient(120deg,#5425d6,#ed3b9b);
 color:white;text-align:center;padding:22px 10px;
}
header h1{margin:0;font-size:25px}
header p{margin:8px 0 0}
.container{max-width:1000px;margin:20px auto;padding:15px}
.editor{
 display:grid;grid-template-columns:1fr 1fr;
 gap:20px;
}
.panel,.preview{
 background:white;padding:18px;border-radius:16px;
 box-shadow:0 5px 20px #00000012;
}
label{display:block;font-weight:bold;margin:14px 0 6px}
input,select{
 width:100%;padding:12px;border:1px solid #ddd;
 border-radius:9px;font-size:15px;
}
canvas{
 width:100%;height:auto;border-radius:8px;
 background:white;
}
button{
 border:0;padding:13px;border-radius:10px;
 color:white;font-weight:bold;font-size:15px;
 cursor:pointer;margin-top:12px;width:100%;
 background:linear-gradient(100deg,#6335dc,#ed399b);
}
button.download{background:#149c69}
h2{font-size:19px}
@media(max-width:700px){
 .editor{grid-template-columns:1fr}
}
</style>
</head>
<body>

<header>
<h1>ST. JOHN'S SCHOOL</h1>
<p>Creative Poster Design Studio 🎨</p>
</header>

<div class="container">
<div class="editor">

<section class="panel">
<h2>🎨 Create Your Poster</h2>

<label>Choose Template</label>
<select id="template">
<option value="gradient">Modern Gradient</option>
<option value="pink">Pink Creative</option>
<option value="blue">Blue Professional</option>
<option value="green">Nature Green</option>
<option value="purple">Purple Creative</option>
</select>

<label>Poster Heading</label>
<input id="heading" value="SAVE OUR PLANET">

<label>Subtitle</label>
<input id="subtitle" value="Small Actions Make A Big Difference">

<label>Your Message</label>
<input id="message" value="Protect Nature • Save Earth">

<label>Your Name</label>
<input id="student" placeholder="Enter your name">

<label>Class</label>
<input id="className" placeholder="Enter your class">

<label>Upload Photo</label>
<input type="file" id="photo" accept="image/*">

<label>Custom Background Colour</label>
<input type="color" id="customColor" value="#6633cc">

<button onclick="drawPoster()">Update Poster ✨</button>
<button class="download" onclick="downloadPoster()">Download Poster ↓</button>
</section>

<section class="preview">
<h2>👀 Poster Preview</h2>
<canvas id="poster" width="600" height="800"></canvas>
<p style="text-align:center;color:#777">
Your poster will appear here.
</p>
</section>

</div>
</div>

<script>
const canvas=document.getElementById("poster");
const ctx=canvas.getContext("2d");
let uploadedImage=null;

const $=id=>document.getElementById(id);

$("photo").addEventListener("change",e=>{
 const file=e.target.files[0];
 if(!file)return;
 const img=new Image();
 img.onload=()=>{
  uploadedImage=img;
  drawPoster();
 };
 img.src=URL.createObjectURL(file);
});

function drawPoster(){
 const template=$("template").value;
 const colors={
  gradient:["#4220a8","#ed399b"],
  pink:["#ff4e91","#ffb3cf"],
  blue:["#0755a5","#43c7ee"],
  green:["#075c42","#9cdb74"],
  purple:["#421a91","#b36bfa"]
 };

 let [c1,c2]=colors[template];
 const custom=$("customColor").value;
 if(template==="gradient")c1=custom;

 let bg=ctx.createLinearGradient(0,0,600,800);
 bg.addColorStop(0,c1);
 bg.addColorStop(1,c2);
 ctx.fillStyle=bg;
 ctx.fillRect(0,0,600,800);

 ctx.fillStyle="rgba(255,255,255,.13)";
 ctx.beginPath();
 ctx.arc(530,90,150,0,Math.PI*2);
 ctx.fill();

 ctx.fillStyle="white";
 ctx.textAlign="center";

 ctx.font="bold 23px Arial";
 ctx.fillText("ST. JOHN'S SCHOOL",300,65);

 ctx.fillStyle="#ffffff";
 ctx.fillRect(65,100,470,3);

 ctx.font="bold 43px Arial";
 wrapText($("heading").value.toUpperCase(),300,175,480,52);

 ctx.font="21px Arial";
 wrapText($("subtitle").value,300,280,460,32);

 if(uploadedImage){
  const maxW=420,maxH=280;
  let scale=Math.min(maxW/uploadedImage.width,maxH/uploadedImage.height);
  let w=uploadedImage.width*scale;
  let h=uploadedImage.height*scale;
  ctx.save();
  ctx.beginPath();
  ctx.roundRect(90,340,420,280,20);
  ctx.clip();
  ctx.drawImage(uploadedImage,(600-w)/2,340+(280-h)/2,w,h);
  ctx.restore();
 }else{
  ctx.font="100px Arial";
  ctx.fillText("🌍",300,485);
 }

 ctx.font="bold 23px Arial";
 wrapText($("message").value,300,680,480,30);

 ctx.font="18px Arial";
 ctx.fillText($("student").value,300,740);

 ctx.font="16px Arial";
 ctx.fillText($("className").value,300,770);
}

function wrapText(text,x,y,maxWidth,lineHeight){
 const words=text.split(" ");
 let line="";
 for(let n=0;n<words.length;n++){
  let test=line+words[n]+" ";
  if(ctx.measureText(test).width>maxWidth && n>0){
   ctx.fillText(line,x,y);
   line=words[n]+" ";
   y+=lineHeight;
  }else line=test;
 }
 ctx.fillText(line,x,y);
}

function downloadPoster(){
 drawPoster();
 const link=document.createElement("a");
 link.download="StJohns-Poster.png";
 link.href=canvas.toDataURL("image/png");
 link.click();
}

drawPoster();
</script>
</body>
</html>

