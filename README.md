<!doctype html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>生日快乐！</title>
  <style>
    body{
      margin:0;
      min-height:100vh;
      display:flex;
      align-items:center;
      justify-content:center;
      font-family:-apple-system,BlinkMacSystemFont,"PingFang SC","Microsoft YaHei",sans-serif;
      background: radial-gradient(circle at top, #ffe7f1, #e7f0ff 55%, #f7fff1);
      overflow:hidden;
    }
    .card{
      width:min(520px, 90vw);
      background:rgba(255,255,255,.75);
      border:1px solid rgba(255,255,255,.7);
      border-radius:22px;
      padding:26px 22px;
      box-shadow: 0 20px 60px rgba(0,0,0,.10);
      backdrop-filter: blur(10px);
      text-align:center;
    }
    h1{ margin:0 0 10px; font-size:34px; letter-spacing:1px; }
    .name{ font-weight:800; }
    p{ margin:10px 0; font-size:16px; line-height:1.7; }
    .btns{ margin-top:16px; display:flex; gap:10px; justify-content:center; flex-wrap:wrap; }
    button{
      border:0; border-radius:14px; padding:10px 14px;
      font-size:15px; cursor:pointer;
      box-shadow: 0 10px 20px rgba(0,0,0,.10);
    }
    .primary{ background:#111; color:#fff; }
    .ghost{ background:#fff; color:#111; }
    .tip{ opacity:.75; font-size:13px; margin-top:12px; }
    .confetti{
      position:absolute; inset:-10vh -10vw;
      pointer-events:none;
    }
  </style>
</head>
<body>
  <canvas class="confetti" id="c"></canvas>

  <div class="card">
    <h1>🎂 生日快乐，<span class="name" id="who">我的兄弟！</h1>

    <p id="msg">
      认识这么久了，我们吵过、哭过，也都和好了。<br/>
      你一直是我很重要的人。希望你新的一岁，开心多一点，烦恼少一点，<br/>
      也一直做你自己——我永远站你这边。
    </p>

    <div class="btns">
      <button class="primary" id="boom">点我放烟花</button>
      <button class="ghost" id="copy">复制祝福文字</button>
    </div>

    <div class="tip">（Alex）</div>
  </div>

  <script>
    // ====== 你只改这里两行就行 ======
    const friendName = "劳棒）";
    const message = `认识这么久了，我们吵过、哭过，也都和好了。
你一直是我很重要的人。希望你新的一岁，开心多一点，烦恼少一点，
也一直做你自己——我永远站你这边。`;
    // =================================

    document.getElementById("who").textContent = friendName;
    document.getElementById("msg").innerHTML = message.replace(/\n/g,"<br/>");

    // confetti
    const canvas = document.getElementById("c");
    const ctx = canvas.getContext("2d");
    let W,H,parts=[];
    function resize(){ W=canvas.width=window.innerWidth; H=canvas.height=window.innerHeight; }
    window.addEventListener("resize", resize); resize();

    function spawn(n=120){
      for(let i=0;i<n;i++){
        parts.push({
          x: Math.random()*W,
          y: -20 - Math.random()*H*0.2,
          vx: (Math.random()-0.5)*3,
          vy: 2 + Math.random()*4,
          r: 2 + Math.random()*4,
          rot: Math.random()*Math.PI,
          vr: (Math.random()-0.5)*0.2,
          life: 200 + Math.random()*120
        });
      }
    }
    function tick(){
      ctx.clearRect(0,0,W,H);
      parts = parts.filter(p => p.life-- > 0 && p.y < H+60);
      for(const p of parts){
        p.x += p.vx; p.y += p.vy; p.rot += p.vr;
        ctx.save();
        ctx.translate(p.x,p.y);
        ctx.rotate(p.rot);
        ctx.fillRect(-p.r, -p.r, p.r*2, p.r*2);
        ctx.restore();
      }
      requestAnimationFrame(tick);
    }
    // 让彩纸更“彩”
    const colors = ["#ff4d6d","#ffd166","#06d6a0","#118ab2","#9b5de5"];
    const oldFillRect = ctx.fillRect.bind(ctx);
    ctx.fillRect = function(x,y,w,h){
      ctx.fillStyle = colors[Math.floor(Math.random()*colors.length)];
      oldFillRect(x,y,w,h);
    };

    tick();
    document.getElementById("boom").onclick = ()=>spawn(180);

    document.getElementById("copy").onclick = async ()=>{
      try{
        const txt = `生日快乐，${friendName}！\n\n${message}`;
        await navigator.clipboard.writeText(txt);
        alert("已复制到剪贴板✅");
      }catch(e){
        alert("复制失败：可能是浏览器限制");
      }
    };

    // 进来先来一波
    spawn(140);
  </script>
</body>
</html>
