[store_713_metrics.html](https://github.com/user-attachments/files/27379736/store_713_metrics.html)
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0">
<title>Store 713 — Metrics</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<style>
*{box-sizing:border-box;margin:0;padding:0;-webkit-tap-highlight-color:transparent;}
body{font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',sans-serif;background:#f0f0ee;color:#1a1a1a;min-height:100vh;font-size:15px;}
.header{background:#378ADD;color:#fff;padding:16px 20px 14px;}
.header h1{font-size:18px;font-weight:700;}
.header p{font-size:12px;opacity:0.85;margin-top:2px;}
.tabs{display:flex;background:#fff;border-bottom:1px solid #e0e0e0;position:sticky;top:0;z-index:10;}
.tab{flex:1;padding:12px 4px;font-size:12px;font-weight:600;text-align:center;color:#888;border:none;background:transparent;cursor:pointer;border-bottom:2px solid transparent;}
.tab.active{color:#378ADD;border-bottom-color:#378ADD;}
.view{display:none;padding:16px;}.view.active{display:block;}
.week-scroll{display:flex;gap:8px;overflow-x:auto;padding-bottom:4px;margin-bottom:16px;scrollbar-width:none;}
.week-scroll::-webkit-scrollbar{display:none;}
.wnbtn{flex-shrink:0;font-size:12px;font-weight:600;padding:6px 14px;border-radius:20px;border:1.5px solid #ddd;background:#fff;color:#666;cursor:pointer;}
.wnbtn.active{background:#378ADD;color:#fff;border-color:#378ADD;}
.card{background:#fff;border-radius:14px;padding:16px;margin-bottom:12px;box-shadow:0 1px 3px rgba(0,0,0,0.06);}
.metric-row{display:flex;align-items:center;justify-content:space-between;padding:11px 0;border-bottom:1px solid #f0f0f0;}
.metric-row:last-child{border-bottom:none;}
.metric-left{flex:1;}
.metric-name{font-size:14px;font-weight:500;color:#222;}
.metric-goal{font-size:11px;color:#aaa;margin-top:2px;}
.badge{font-size:13px;font-weight:700;padding:5px 14px;border-radius:20px;min-width:60px;text-align:center;}
.green{background:#eaf3de;color:#3b6d11;}
.red{background:#fcebeb;color:#a32d2d;}
.yellow{background:#fef3cd;color:#856404;}
.legend{display:flex;gap:12px;flex-wrap:wrap;margin-top:12px;}
.legend span{font-size:11px;color:#888;display:flex;align-items:center;gap:5px;}
.dot{width:9px;height:9px;border-radius:3px;display:inline-block;}
.chart-hdr{display:flex;align-items:flex-start;justify-content:space-between;margin-bottom:4px;}
.chart-title{font-size:14px;font-weight:600;color:#1a1a1a;flex:1;}
.gpill{font-size:10px;color:#666;padding:2px 8px;border:1px solid #e0e0e0;border-radius:10px;background:#f9f9f9;white-space:nowrap;margin-left:8px;}
.stats{display:flex;flex-wrap:wrap;gap:10px;margin:8px 0 12px;}
.stat{font-size:11px;color:#888;}.stat b{font-weight:600;color:#333;}
.stat.up b{color:#3b6d11;}.stat.dn b{color:#a32d2d;}
.sum-row{display:flex;justify-content:space-between;align-items:center;padding:11px 0;border-bottom:1px solid #f0f0f0;gap:8px;}
.sum-row:last-child{border-bottom:none;}
.sum-name{font-size:13px;font-weight:500;color:#222;flex:1;}
.sum-right{display:flex;flex-direction:column;align-items:flex-end;gap:3px;}
.sum-avg{font-size:13px;font-weight:700;color:#1a1a1a;}
.sum-meta{font-size:11px;color:#aaa;}
.sum-trend{font-size:11px;font-weight:600;}
.sum-trend.up{color:#3b6d11;}.sum-trend.dn{color:#a32d2d;}
.section-label{font-size:11px;font-weight:700;color:#aaa;text-transform:uppercase;letter-spacing:0.06em;margin-bottom:10px;}
</style>
</head>
<body>
<div class="header">
  <h1>Store 713</h1>
  <p>Pharmacy metrics &nbsp;·&nbsp; 11 weeks &nbsp;·&nbsp; W11 = latest</p>
</div>
<div class="tabs">
  <button class="tab active" onclick="setView('scorecard')">Scorecard</button>
  <button class="tab" onclick="setView('trends')">Trends</button>
  <button class="tab" onclick="setView('summary')">Summary</button>
</div>
<div id="view-scorecard" class="view active">
  <div class="week-scroll" id="week-scroll"></div>
  <div class="card" id="scorecard"></div>
  <div class="legend">
    <span><span class="dot" style="background:#eaf3de;border:1px solid #c0dd97;"></span>At goal</span>
    <span><span class="dot" style="background:#fef3cd;border:1px solid #fac775;"></span>Near goal</span>
    <span><span class="dot" style="background:#fcebeb;border:1px solid #f7c1c1;"></span>Below goal</span>
  </div>
</div>
<div id="view-trends" class="view" style="display:none;"><div id="trends-content"></div></div>
<div id="view-summary" class="view" style="display:none;">
  <div class="section-label">11-week performance overview</div>
  <div class="card" id="summary-content"></div>
</div>
<script>
const STORE='713',COLOR='#378ADD';
const weeks=['W1','W2','W3','W4','W5','W6','W7','W8','W9','W10','W11'];
const metrics=[
  {key:'redBasket',label:'Red basket',goal:75,goalLabel:'Goal >75%',unit:'%',dir:'up',data:[66,73.6,64,71,74.5,71.8,75,71.8,77.4,82,85]},
  {key:'centralVial',label:'Central vial',goal:70,goalLabel:'Goal >70%',unit:'%',dir:'up',data:[67,67,72,69.2,74.5,71.5,77,72.3,72.3,72.3,76]},
  {key:'rxOsat',label:'Rx OSAT',goal:80,goalLabel:'Goal >80%',unit:'%',dir:'up',data:[100,66.7,66.7,80,84.1,100,86,86,92.9,100,80]},
  {key:'oon',label:'OON transfers',goal:0,goalLabel:'Goal = 0',unit:'',dir:'down',data:[2,8,9,5,4,7,13,3,8,9,10]},
  {key:'callHold',label:'Calls on hold',goal:2,goalLabel:'Goal <2%',unit:'%',dir:'down',data:[0.9,0.3,0.4,0.3,2.5,0.4,1.1,0.8,0.9,0.4,0.7]}
];
let currentWeek=10;const ci={};let tB=false,sB=false;
function gc(m,v){if(m.dir==='up'){if(v>=m.goal)return'green';if(v>=m.goal*0.95)return'yellow';return'red';}else{if(v<=m.goal)return'green';if(v<=m.goal+1)return'yellow';return'red';}}
function fmt(m,v){return m.key==='oon'?v:v+'%';}
function avg(a){return a.reduce((x,y)=>x+y,0)/a.length;}
function td(m,a){const d=a[a.length-1]-a[0];return m.dir==='up'?(d>=0?'up':'dn'):(d<=0?'up':'dn');}
function tl(m,a){const d=a[a.length-1]-a[0];return(d>=0?'+':'-')+Math.abs(d).toFixed(1)+m.unit+' vs W1';}
function buildWeekNav(){const el=document.getElementById('week-scroll');el.innerHTML=weeks.map((w,i)=>`<button class="wnbtn${i===currentWeek?' active':''}" onclick="setWeek(${i})">${w}</button>`).join('');}
function setWeek(w){currentWeek=w;document.querySelectorAll('.wnbtn').forEach((b,i)=>b.classList.toggle('active',i===w));renderScorecard();}
function renderScorecard(){document.getElementById('scorecard').innerHTML=metrics.map(m=>{const v=m.data[currentWeek];return`<div class="metric-row"><div class="metric-left"><div class="metric-name">${m.label}</div><div class="metric-goal">${m.goalLabel}</div></div><span class="badge ${gc(m,v)}">${fmt(m,v)}</span></div>`;}).join('');}
function buildTrends(){const el=document.getElementById('trends-content');el.innerHTML='';metrics.forEach((m,i)=>{const a=m.data,av=avg(a).toFixed(1),lat=a[a.length-1],wOn=a.filter(v=>gc(m,v)==='green').length,good=gc(m,lat)==='green';const card=document.createElement('div');card.className='card';card.innerHTML=`<div class="chart-hdr"><span class="chart-title">${m.label}</span><span class="gpill">${m.goalLabel}</span></div><div class="stats"><span class="stat">Latest: <b style="color:${good?'#3b6d11':'#a32d2d'}">${lat}${m.unit}</b></span><span class="stat">Avg: <b>${av}${m.unit}</b></span><span class="stat">On goal: <b>${wOn}/11</b></span><span class="stat ${td(m,a)}">Trend: <b>${tl(m,a)}</b></span></div><div style="position:relative;height:160px;"><canvas id="c${i}"></canvas></div>`;el.appendChild(card);setTimeout(()=>{const ctx=document.getElementById('c'+i);if(!ctx)return;if(ci[i])ci[i].destroy();ci[i]=new Chart(ctx,{type:'line',data:{labels:weeks,datasets:[{data:a,borderColor:COLOR,backgroundColor:COLOR+'18',borderWidth:2.5,pointRadius:4,pointBackgroundColor:a.map(v=>gc(m,v)==='green'?'#3b6d11':'#a32d2d'),pointBorderColor:a.map(v=>gc(m,v)==='green'?'#3b6d11':'#a32d2d'),tension:0.3,fill:true},{data:Array(11).fill(m.goal),borderColor:'#ccc',borderWidth:1.5,borderDash:[4,4],pointRadius:0,fill:false}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false},tooltip:{callbacks:{label:c=>c.datasetIndex===1?'Goal: '+m.goal+m.unit:'Value: '+c.parsed.y+m.unit}}},scales:{x:{grid:{color:'rgba(0,0,0,0.04)'},ticks:{font:{size:10},color:'#aaa',autoSkip:false,maxRotation:0}},y:{grid:{color:'rgba(0,0,0,0.04)'},ticks:{font:{size:10},color:'#aaa',callback:v=>v+m.unit}}}}});},60);});tB=true;}
function buildSummary(){document.getElementById('summary-content').innerHTML=metrics.map(m=>{const a=m.data,av=avg(a).toFixed(1),wOn=a.filter(v=>gc(m,v)==='green').length,dir=td(m,a),lab=tl(m,a);return`<div class="sum-row"><div class="sum-name">${m.label}</div><div class="sum-right"><div class="sum-avg">${av}${m.unit}</div><div class="sum-meta">${wOn}/11 weeks on goal</div><div class="sum-trend ${dir}">${lab}</div></div></div>`;}).join('');sB=true;}
function setView(v){document.querySelectorAll('.tab').forEach((t,i)=>t.classList.toggle('active',['scorecard','trends','summary'][i]===v));document.querySelectorAll('.view').forEach(el=>{el.style.display='none';el.classList.remove('active');});const el=document.getElementById('view-'+v);el.style.display='block';el.classList.add('active');if(v==='trends'&&!tB)buildTrends();if(v==='summary'&&!sB)buildSummary();}
buildWeekNav();renderScorecard();
</script>
</body>
</html>
