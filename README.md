<html lang="sv">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mister York — District Dashboard</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
<style>
*{margin:0;padding:0;box-sizing:border-box;}
:root{--bg:#0f0f0f;--card:#1a1a1a;--border:#2a2a2a;--borderL:#3a3a3a;--text:#f5f5f5;--muted:#a0a0a0;--dim:#6b6b6b;--accent:#C41E3A;--accentD:#8B1528;--accentL:#E8354F;--green:#22C55E;--red:#EF4444;--amber:#F59E0B;--amberD:#92400e;--purple:#A855F7;--blue:#3B82F6;}
body{background:var(--bg);color:var(--text);font-family:'Plus Jakarta Sans',-apple-system,sans-serif;min-height:100vh;}
.mono{font-family:'Plus Jakarta Sans',sans-serif;font-weight:600;}
.header{background:var(--accent);padding:20px 32px;box-shadow:0 4px 20px rgba(196,30,58,0.3);}
.header-inner{display:flex;justify-content:space-between;align-items:center;flex-wrap:wrap;gap:16px;}
h1{font-size:24px;font-weight:800;color:#fff;letter-spacing:-0.5px;}
.subtitle{font-size:12px;color:rgba(255,255,255,0.7);margin-top:2px;font-weight:500;}
.controls{display:flex;gap:8px;align-items:center;flex-wrap:wrap;}
select{background:rgba(255,255,255,0.15);color:#fff;border:1px solid rgba(255,255,255,0.25);border-radius:8px;padding:8px 12px;font-size:13px;cursor:pointer;font-family:'Plus Jakarta Sans',sans-serif;backdrop-filter:blur(4px);}
select option{background:#1a1a1a;color:#f5f5f5;}
.upload-btn{padding:8px 16px;border-radius:8px;cursor:pointer;font-size:13px;font-weight:600;display:inline-block;background:rgba(255,255,255,0.15);border:1px solid rgba(255,255,255,0.3);color:#fff;backdrop-filter:blur(4px);transition:all 0.2s;}
.upload-btn:hover{background:rgba(255,255,255,0.25);}
.upload-btn input{display:none;}
.main{padding:24px 32px;max-width:1400px;margin:0 auto;}
.dropzone{border:2px dashed var(--border);border-radius:12px;padding:24px;text-align:center;margin-bottom:24px;transition:all 0.2s;}
.dropzone.over{border-color:var(--accent);background:rgba(196,30,58,0.07);}
.tabs{display:flex;gap:6px;margin-bottom:24px;flex-wrap:wrap;}
.tab{padding:9px 22px;border-radius:8px;border:none;background:var(--card);color:var(--muted);font-size:13px;font-weight:600;cursor:pointer;transition:all 0.2s;font-family:'Plus Jakarta Sans',sans-serif;}
.tab:hover{color:var(--text);background:#252525;}
.tab.active{background:var(--accent);color:#fff;box-shadow:0 2px 12px rgba(196,30,58,0.3);}
.kpis{display:flex;gap:14px;margin-bottom:24px;flex-wrap:wrap;}
.kpi{background:var(--card);border:1px solid var(--border);border-radius:14px;padding:20px 24px;flex:1 1 180px;min-width:160px;position:relative;overflow:hidden;}
.kpi-bar{position:absolute;top:0;left:0;right:0;height:3px;}
.kpi-label{font-size:11px;color:var(--dim);text-transform:uppercase;letter-spacing:1.2px;margin-bottom:8px;font-weight:600;}
.kpi-value{font-size:26px;font-weight:800;line-height:1.1;letter-spacing:-0.5px;}
.kpi-sub{font-size:12px;color:var(--muted);margin-top:6px;font-weight:500;}
.grid{display:grid;gap:18px;}.grid-2{grid-template-columns:1fr 1fr;}
.card{background:var(--card);border:1px solid var(--border);border-radius:14px;padding:24px;}
.card-full{grid-column:1 / -1;}
.card h3{font-size:12px;color:var(--muted);letter-spacing:1.2px;margin-bottom:16px;text-transform:uppercase;font-weight:700;}
.chart-wrap{position:relative;width:100%;height:280px;}
.chart-wrap.tall{height:380px;}
.chart-wrap.short{height:220px;}
table{width:100%;border-collapse:collapse;font-size:13px;}
th{padding:10px 12px;text-align:left;color:var(--dim);font-size:11px;text-transform:uppercase;letter-spacing:1px;border-bottom:2px solid var(--accent);font-weight:700;}
td{padding:12px;font-weight:500;}tr+tr{border-top:1px solid var(--border);}
.stat-row{display:flex;justify-content:space-between;align-items:center;padding:12px 16px;border-radius:10px;background:var(--bg);border:1px solid var(--border);margin-bottom:10px;}
.comment-card{background:var(--bg);border-left:3px solid var(--accent);border-radius:0 8px 8px 0;padding:12px 16px;margin-bottom:8px;}
.comment-date{font-size:11px;color:var(--accent);margin-bottom:4px;font-weight:700;}
.comment-text{font-size:13px;color:var(--muted);line-height:1.6;font-weight:500;}
.footer{text-align:center;margin-top:32px;padding-bottom:24px;font-size:11px;color:var(--dim);font-weight:500;}
.error{color:var(--red);margin-top:8px;font-size:13px;}
@media(max-width:800px){.grid-2{grid-template-columns:1fr;}}
::-webkit-scrollbar{width:8px;height:8px;}
::-webkit-scrollbar-track{background:#0f0f0f;}
::-webkit-scrollbar-thumb{background:#3a3a3a;border-radius:4px;}
</style>
</head>
<body>
<div class="header"><div class="header-inner">
  <div><h1>MISTER YORK</h1><div class="subtitle">District Dashboard <span id="demoTag"></span></div></div>
  <div class="controls">
    <select id="restFilter" class="mono" onchange="render()"><option value="all">Alla restauranger</option></select>
    <label class="upload-btn mono">&uarr; Ladda upp Loggbok CSV<input type="file" accept=".csv,.tsv,.txt" onchange="loadFile(this)"></label>
  </div>
</div></div>
<div class="main">
  <div id="dropzone" class="dropzone" ondragover="event.preventDefault();this.classList.add('over')" ondragleave="this.classList.remove('over')" ondrop="event.preventDefault();this.classList.remove('over');dropFile(event)">
    <div class="mono" style="font-size:14px;color:var(--muted)">&#128194; Dra och sl&auml;pp din Loggbok CSV h&auml;r</div>
    <div style="font-size:11px;color:var(--dim);margin-top:6px">Exportera fr&aring;n Google Sheets: Arkiv &rarr; Ladda ned &rarr; CSV</div>
    <div id="uploadError" class="error"></div>
  </div>
  <div class="tabs" id="tabs"></div>
  <div class="kpis" id="kpis"></div>
  <div id="content"></div>
  <div class="footer mono" id="footer"></div>
</div>
<script>
var CC=["#C41E3A","#22C55E","#3B82F6","#F59E0B","#A855F7","#EC4899","#06B6D4"];
var VIEWS=["overview","compare","trends","forecast","tickettime","comments"];
var VIEW_LABELS=["&Ouml;versikt","J&auml;mf&ouml;r restauranger","Trender","Prognos vs Utfall","Ticket Time","Kommentarer"];
var currentView="overview";
var charts={};
var DATA={restaurants:[],days:[],daily:[],perRest:{},month:"",year:""};

var fmt=function(n){return n!=null?Math.round(n).toLocaleString("sv-SE"):"—";};
var fmtK=function(n){return n>=1000?(n/1000).toFixed(1)+"k":fmt(n);};
var fmtPct=function(n){return n!=null?n.toFixed(1)+"%":"—";};

function parseSEK(s){
  if(!s||s==="-"||s==="#DIV/0!")return 0;
  s=String(s).replace(/"/g,"").replace(/ kr/g,"").replace(/\s/g,"").replace(/\u00a0/g,"");
  s=s.replace(/,/g,"");
  return parseFloat(s)||0;
}

function parseLoggbok(text){
  var lines=text.replace(/\r/g,"").split("\n");
  var data={restaurants:[],days:[],daily:[],perRest:{},month:"",year:"",comments:[]};

  // Find the header row with DATUM
  var headerIdx=-1;
  for(var i=0;i<Math.min(lines.length,20);i++){
    if(lines[i].indexOf("DATUM")>=0||lines[i].indexOf("Datum")>=0){headerIdx=i;break;}
  }
  if(headerIdx<0){return null;}

  // Parse daily rows (district totals)
  for(var i=headerIdx+1;i<lines.length;i++){
    var row=parseCSVRow(lines[i]);
    var dateStr=row[2];
    if(!dateStr||dateStr.indexOf("/")< 0)continue;
    var sales=parseSEK(row[12]);
    if(sales===0&&parseSEK(row[5])===0)continue;
    var parts=dateStr.split("/");
    if(!data.month)data.month=parts[0];
    if(!data.year)data.year=parts[2];
    var dayNum=parseInt(parts[1]);
    if(isNaN(dayNum))continue;

    var comment="";
    for(var ci=37;ci<row.length;ci++){
      if(row[ci]&&row[ci].trim().length>3){comment=row[ci].trim();break;}
    }

    data.daily.push({
      day:dayNum,
      date:dateStr,
      weekday:row[3]||"",
      fcSales:parseSEK(row[5]),
      fcLaborHours:parseSEK(row[6]),
      fcLaborCost:parseSEK(row[7]),
      fcLaborPct:parseSEK(row[8]),
      fcWaste:parseSEK(row[9]),
      sales:sales,
      absenceHours:parseSEK(row[13]),
      laborHours:parseSEK(row[14]),
      laborCost:parseSEK(row[15]),
      laborPct:parseSEK(row[16]),
      wasteCost:parseSEK(row[17]),
      wastePct:parseSEK(row[18]),
      status:row[19]||"",
      ticketTime:parseSEK(row[33]),
      comment:comment
    });
    if(comment)data.comments.push({day:dayNum,date:dateStr,weekday:row[3],text:comment});
  }
  data.days=data.daily.map(function(d){return d.day;});

  // Find per-restaurant grids
  var sections=["Sales","Actual labor cost","Waste","Productive hours used","Absence hours","Ticket Time (sec)"];
  var keys=["sales","laborCost","waste","hours","absenceHours","ticketTime"];

  for(var si=0;si<sections.length;si++){
    var sectionName=sections[si];
    var key=keys[si];
    var startIdx=-1;
    for(var i=0;i<lines.length;i++){
      var row=parseCSVRow(lines[i]);
      // Look for the section header in column 28
      if(row[28]&&row[28].trim().toLowerCase()===sectionName.toLowerCase()){startIdx=i;break;}
      // Also try partial match
      if(row[28]&&row[28].trim().toLowerCase().indexOf(sectionName.toLowerCase().substring(0,6))===0){startIdx=i;break;}
    }
    if(startIdx<0)continue;

    // Read restaurant rows after section header
    var foundCount=0;
    for(var i=startIdx+1;i<Math.min(startIdx+20,lines.length);i++){
      var row=parseCSVRow(lines[i]);
      var name=(row[28]||"").trim();
      if(!name||name===""){if(foundCount>0)break;continue;}
      if(name.indexOf("Total")===0)break;
      // Skip other section headers
      if(name==="Sales"||name==="Actual labor cost"||name==="Waste"||name==="Productive hours used"||name==="Productive hours"||name==="Absence hours"||name==="Internal processes"||name==="Grant support"||name==="Optiqo Cleaning"||name==="Google review"||name==="Ticket Time (sec)"||name==="Total ticket time")break;

      foundCount++;
      if(data.restaurants.indexOf(name)<0)data.restaurants.push(name);
      if(!data.perRest[name])data.perRest[name]={};
      if(!data.perRest[name][key])data.perRest[name][key]={};

      for(var d=1;d<=31;d++){
        var val=parseSEK(row[28+d]);
        if(val)data.perRest[name][key][d]=val;
      }
    }
  }

  return data;
}

function parseCSVRow(line){
  var result=[];var current="";var inQuotes=false;
  for(var i=0;i<line.length;i++){
    var c=line[i];
    if(c==='"'){inQuotes=!inQuotes;}
    else if(c===","&&!inQuotes){result.push(current);current="";}
    else{current+=c;}
  }
  result.push(current);
  return result;
}

function loadFile(input){
  var file=input.files[0];if(!file)return;
  var reader=new FileReader();
  reader.onload=function(e){
    var parsed=parseLoggbok(e.target.result);
    if(!parsed||!parsed.daily.length){document.getElementById("uploadError").textContent="Kunde inte läsa filen. Kontrollera att det är en Loggbok-export.";return;}
    DATA=parsed;document.getElementById("dropzone").style.display="none";render();
  };reader.readAsText(file);
}
function dropFile(e){var file=e.dataTransfer.files[0];if(!file)return;
  var reader=new FileReader();reader.onload=function(ev){
    var parsed=parseLoggbok(ev.target.result);
    if(!parsed||!parsed.daily.length){document.getElementById("uploadError").textContent="Kunde inte läsa filen.";return;}
    DATA=parsed;document.getElementById("dropzone").style.display="none";render();
  };reader.readAsText(file);}

function destroyCharts(){for(var k in charts){try{charts[k].destroy();}catch(e){}}charts={};}
function makeChart(id,config){var canvas=document.getElementById(id);if(!canvas)return;charts[id]=new Chart(canvas.getContext("2d"),config);}
var baseOpts={responsive:true,maintainAspectRatio:false,plugins:{legend:{labels:{color:"#a0a0a0",font:{size:11,family:"'Plus Jakarta Sans',sans-serif",weight:600}}},tooltip:{backgroundColor:"#1a1a1a",borderColor:"#C41E3A",borderWidth:1,titleColor:"#fff",bodyColor:"#a0a0a0",bodyFont:{family:"'Plus Jakarta Sans',sans-serif",size:12},cornerRadius:8,padding:12}},scales:{x:{ticks:{color:"#6b6b6b",font:{size:11}},grid:{color:"rgba(255,255,255,0.04)"}},y:{ticks:{color:"#6b6b6b",font:{size:11}},grid:{color:"rgba(255,255,255,0.04)"}}}};

function getRestData(name,key){
  if(!DATA.perRest[name]||!DATA.perRest[name][key])return[];
  return DATA.days.map(function(d){return DATA.perRest[name][key][d]||0;});
}
function getAllRestSum(key){
  return DATA.days.map(function(d){
    var sum=0;DATA.restaurants.forEach(function(r){if(DATA.perRest[r]&&DATA.perRest[r][key])sum+=(DATA.perRest[r][key][d]||0);});return sum;
  });
}
function totalForRest(name,key){
  if(!DATA.perRest[name]||!DATA.perRest[name][key])return 0;
  var sum=0;for(var d in DATA.perRest[name][key])sum+=DATA.perRest[name][key][d];return sum;
}

function kpiHTML(icon,label,value,sub,grad){
  return'<div class="kpi"><div class="kpi-bar" style="background:'+grad+'"></div><div class="kpi-label mono">'+icon+' '+label+'</div><div class="kpi-value mono" style="color:var(--text)">'+value+'</div>'+(sub?'<div class="kpi-sub">'+sub+'</div>':"")+'</div>';
}

function render(){
  destroyCharts();
  if(!DATA.daily.length){
    document.getElementById("kpis").innerHTML="";document.getElementById("tabs").innerHTML="";document.getElementById("content").innerHTML='<div style="text-align:center;color:var(--dim);padding:60px 0;font-size:15px;font-weight:600">Ladda upp en Loggbok-CSV f&ouml;r att komma ig&aring;ng</div>';
    document.getElementById("footer").textContent="";return;
  }

  var sel=document.getElementById("restFilter");var curVal=sel.value;
  sel.innerHTML='<option value="all">Alla restauranger</option>'+DATA.restaurants.map(function(r){return'<option value="'+r+'">'+r+'</option>';}).join("");
  sel.value=(DATA.restaurants.indexOf(curVal)>=0||curVal==="all")?curVal:"all";

  var totalSales=0,totalLabor=0,totalWaste=0,totalHours=0;
  var selR=sel.value;
  if(selR==="all"){
    DATA.daily.forEach(function(d){totalSales+=d.sales;totalLabor+=d.laborCost;totalWaste+=d.wasteCost;totalHours+=d.laborHours;});
  }else{
    totalSales=totalForRest(selR,"sales");totalLabor=totalForRest(selR,"laborCost");totalWaste=totalForRest(selR,"waste");totalHours=totalForRest(selR,"hours");
  }
  var laborPct=totalSales?totalLabor/totalSales*100:0;
  var wastePct=totalSales?totalWaste/totalSales*100:0;
  var salesPerHour=totalHours?totalSales/totalHours:0;

  var avgTT=0;
  if(selR==="all"){
    var ttDays=DATA.daily.filter(function(d){return d.ticketTime>0;});
    avgTT=ttDays.length?ttDays.reduce(function(a,d){return a+d.ticketTime;},0)/ttDays.length:0;
  }else{
    var ttData=DATA.perRest[selR]&&DATA.perRest[selR].ticketTime?DATA.perRest[selR].ticketTime:{};
    var ttVals=Object.values(ttData).filter(function(v){return v>0;});
    avgTT=ttVals.length?ttVals.reduce(function(a,v){return a+v;},0)/ttVals.length:0;
  }

  var monthLabel=(DATA.month||"")+"/"+(DATA.year||"");
  document.getElementById("demoTag").textContent=" \u00B7 "+monthLabel;

  var ttMin=Math.floor(avgTT/60);var ttSec=Math.round(avgTT%60);
  document.getElementById("kpis").innerHTML=[
    kpiHTML("&#128176;","F&ouml;rs&auml;ljning",fmtK(totalSales)+" kr","","linear-gradient(90deg,var(--accent),transparent)"),
    kpiHTML("&#128101;","Labor %",fmtPct(laborPct),fmt(totalLabor)+" kr","linear-gradient(90deg,"+(laborPct>30?"var(--red)":"var(--green)")+",transparent)"),
    kpiHTML("&#128465;","Waste %",fmtPct(wastePct),fmt(totalWaste)+" kr","linear-gradient(90deg,"+(wastePct>0.7?"var(--red)":"var(--green)")+",transparent)"),
    kpiHTML("&#9201;","Fsg/timme",fmt(salesPerHour)+" kr",fmt(totalHours)+" timmar","linear-gradient(90deg,var(--purple),transparent)"),
    kpiHTML("&#128337;","Avg Ticket Time",ttMin+":"+String(ttSec).padStart(2,"0"),fmt(Math.round(avgTT))+" sek","linear-gradient(90deg,"+(avgTT>360?"var(--red)":"var(--green)")+",transparent)")
  ].join("");

  document.getElementById("tabs").innerHTML=VIEWS.map(function(v,i){return'<button class="tab mono '+(currentView===v?"active":"")+'" onclick="currentView=\''+v+'\';render()">'+VIEW_LABELS[i]+'</button>';}).join("");

  var el=document.getElementById("content");
  if(currentView==="overview")renderOverview(el);
  else if(currentView==="compare")renderCompare(el);
  else if(currentView==="trends")renderTrends(el);
  else if(currentView==="forecast")renderForecast(el);
  else if(currentView==="tickettime")renderTicketTime(el);
  else if(currentView==="comments")renderComments(el);

  document.getElementById("footer").textContent="Mister York \u00B7 District Dashboard \u00B7 "+monthLabel+" \u00B7 "+DATA.restaurants.length+" restauranger \u00B7 "+DATA.daily.length+" dagar";
}

function dayLabels(){return DATA.days.map(function(d){return d;});}

function renderOverview(el){
  var labels=dayLabels();
  var selR=document.getElementById("restFilter").value;
  var salesArr=selR==="all"?DATA.daily.map(function(d){return d.sales;}):getRestData(selR,"sales");
  var laborArr=selR==="all"?DATA.daily.map(function(d){return d.laborCost;}):getRestData(selR,"laborCost");
  var laborPctArr=DATA.daily.map(function(d,i){var s=salesArr[i];return s?(laborArr[i]/s*100):0;});

  el.innerHTML='<div class="grid grid-2"><div class="card"><h3 class="mono">DAGLIG F&Ouml;RS&Auml;LJNING</h3><div class="chart-wrap"><canvas id="o1"></canvas></div></div><div class="card"><h3 class="mono">LABOR % PER DAG</h3><div class="chart-wrap"><canvas id="o2"></canvas></div></div><div class="card card-full"><h3 class="mono">F&Ouml;RS&Auml;LJNING PER RESTAURANG</h3><div class="chart-wrap"><canvas id="o3"></canvas></div></div></div>';

  makeChart("o1",{type:"line",data:{labels:labels,datasets:[{label:"Försäljning",data:salesArr,borderColor:"#C41E3A",backgroundColor:"rgba(196,30,58,0.15)",fill:true,tension:0.3,pointRadius:3}]},options:JSON.parse(JSON.stringify(baseOpts))});

  var o2opts=JSON.parse(JSON.stringify(baseOpts));
  makeChart("o2",{type:"line",data:{labels:labels,datasets:[{label:"Labor %",data:laborPctArr,borderColor:"#A855F7",backgroundColor:"rgba(168,85,247,0.15)",fill:true,tension:0.3,pointRadius:3}]},options:o2opts});

  var ds=DATA.restaurants.map(function(name,i){return{label:name,data:getRestData(name,"sales"),borderColor:CC[i%CC.length],tension:0.3,pointRadius:0};});
  makeChart("o3",{type:"line",data:{labels:labels,datasets:ds},options:JSON.parse(JSON.stringify(baseOpts))});
}

function renderCompare(el){
  var comp=DATA.restaurants.map(function(name,i){
    var s=totalForRest(name,"sales"),l=totalForRest(name,"laborCost"),w=totalForRest(name,"waste"),h=totalForRest(name,"hours");
    return{name:name,sales:s,labor:l,waste:w,hours:h,laborPct:s?+(l/s*100).toFixed(1):0,wastePct:s?+(w/s*100).toFixed(1):0,sph:h?Math.round(s/h):0};
  });

  var rows=comp.map(function(r,i){return'<tr><td style="font-weight:600;color:'+CC[i%CC.length]+'">'+r.name+'</td><td>'+fmt(r.sales)+' kr</td><td style="color:'+(r.laborPct>30?"var(--red)":"var(--green)")+'">'+fmtPct(r.laborPct)+'</td><td style="color:'+(r.wastePct>0.7?"var(--red)":"var(--green)")+'">'+fmtPct(r.wastePct)+'</td><td>'+fmt(r.sph)+' kr</td><td>'+fmt(r.hours)+'</td></tr>';}).join("");

  el.innerHTML='<div class="grid"><div class="card"><h3 class="mono">F&Ouml;RS&Auml;LJNING PER RESTAURANG</h3><div class="chart-wrap" style="height:320px"><canvas id="cmp1"></canvas></div></div><div class="card"><h3 class="mono">LABOR % PER RESTAURANG</h3><div class="chart-wrap" style="height:320px"><canvas id="cmp2"></canvas></div></div><div class="card" style="overflow-x:auto"><h3 class="mono">DETALJERAD J&Auml;MF&Ouml;RELSE</h3><table class="mono"><thead><tr><th>Restaurang</th><th>F&ouml;rs&auml;ljning</th><th>Labor %</th><th>Waste %</th><th>Fsg/timme</th><th>Timmar</th></tr></thead><tbody>'+rows+'</tbody></table></div></div>';

  makeChart("cmp1",{type:"bar",data:{labels:comp.map(function(r){return r.name;}),datasets:[{label:"Försäljning",data:comp.map(function(r){return r.sales;}),backgroundColor:comp.map(function(_,i){return CC[i%CC.length];}),borderRadius:6}]},options:JSON.parse(JSON.stringify(baseOpts))});

  makeChart("cmp2",{type:"bar",data:{labels:comp.map(function(r){return r.name;}),datasets:[{label:"Labor %",data:comp.map(function(r){return r.laborPct;}),backgroundColor:comp.map(function(_,i){return CC[i%CC.length];}),borderRadius:6}]},options:JSON.parse(JSON.stringify(baseOpts))});
}

function renderTrends(el){
  var labels=dayLabels();
  var selR=document.getElementById("restFilter").value;
  var salesArr=selR==="all"?DATA.daily.map(function(d){return d.sales;}):getRestData(selR,"sales");
  var laborArr=selR==="all"?DATA.daily.map(function(d){return d.laborCost;}):getRestData(selR,"laborCost");
  var wasteArr=selR==="all"?DATA.daily.map(function(d){return d.wasteCost;}):getRestData(selR,"waste");
  var hoursArr=selR==="all"?DATA.daily.map(function(d){return d.laborHours;}):getRestData(selR,"hours");

  el.innerHTML='<div class="grid grid-2"><div class="card"><h3 class="mono">DAGLIG F&Ouml;RS&Auml;LJNING</h3><div class="chart-wrap"><canvas id="tr1"></canvas></div></div><div class="card"><h3 class="mono">DAGLIG LABOR COST</h3><div class="chart-wrap"><canvas id="tr2"></canvas></div></div><div class="card"><h3 class="mono">DAGLIG WASTE</h3><div class="chart-wrap"><canvas id="tr3"></canvas></div></div><div class="card"><h3 class="mono">PRODUKTIVA TIMMAR</h3><div class="chart-wrap"><canvas id="tr4"></canvas></div></div></div>';

  makeChart("tr1",{type:"line",data:{labels:labels,datasets:[{label:"Försäljning",data:salesArr,borderColor:"#C41E3A",backgroundColor:"rgba(196,30,58,0.15)",fill:true,tension:0.3,pointRadius:3}]},options:JSON.parse(JSON.stringify(baseOpts))});
  makeChart("tr2",{type:"line",data:{labels:labels,datasets:[{label:"Labor cost",data:laborArr,borderColor:"#A855F7",backgroundColor:"rgba(168,85,247,0.15)",fill:true,tension:0.3,pointRadius:3}]},options:JSON.parse(JSON.stringify(baseOpts))});
  makeChart("tr3",{type:"bar",data:{labels:labels,datasets:[{label:"Waste",data:wasteArr,backgroundColor:"#F59E0B",borderRadius:6}]},options:JSON.parse(JSON.stringify(baseOpts))});
  makeChart("tr4",{type:"line",data:{labels:labels,datasets:[{label:"Timmar",data:hoursArr,borderColor:"#22C55E",backgroundColor:"rgba(34,197,94,0.15)",fill:true,tension:0.3,pointRadius:3}]},options:JSON.parse(JSON.stringify(baseOpts))});
}

function renderForecast(el){
  var labels=dayLabels();
  var fcSales=DATA.daily.map(function(d){return d.fcSales;});
  var actSales=DATA.daily.map(function(d){return d.sales;});
  var fcLabor=DATA.daily.map(function(d){return d.fcLaborCost;});
  var actLabor=DATA.daily.map(function(d){return d.laborCost;});
  var diffSales=DATA.daily.map(function(d){return d.sales-d.fcSales;});

  var totalFcSales=0,totalActSales=0,totalFcLabor=0,totalActLabor=0;
  DATA.daily.forEach(function(d){totalFcSales+=d.fcSales;totalActSales+=d.sales;totalFcLabor+=d.fcLaborCost;totalActLabor+=d.laborCost;});
  var diffTotal=totalActSales-totalFcSales;

  var stats=[
    {l:"Prognos f&ouml;rs&auml;ljning",v:fmt(totalFcSales)+" kr",c:"var(--dim)"},
    {l:"Utfall f&ouml;rs&auml;ljning",v:fmt(totalActSales)+" kr",c:"var(--accent)"},
    {l:"Diff f&ouml;rs&auml;ljning",v:(diffTotal>=0?"+":"")+fmt(diffTotal)+" kr",c:diffTotal>=0?"var(--green)":"var(--red)"},
    {l:"Prognos labor",v:fmt(totalFcLabor)+" kr",c:"var(--dim)"},
    {l:"Utfall labor",v:fmt(totalActLabor)+" kr",c:"var(--purple)"},
    {l:"Diff labor",v:(totalActLabor-totalFcLabor>=0?"+":"")+fmt(totalActLabor-totalFcLabor)+" kr",c:totalActLabor>totalFcLabor?"var(--red)":"var(--green)"}
  ].map(function(s){return'<div class="stat-row"><span style="font-size:13px;color:var(--muted)">'+s.l+'</span><span class="mono" style="font-size:15px;font-weight:600;color:'+s.c+'">'+s.v+'</span></div>';}).join("");

  el.innerHTML='<div class="grid grid-2"><div class="card"><h3 class="mono">PROGNOS VS UTFALL &mdash; F&Ouml;RS&Auml;LJNING</h3><div class="chart-wrap"><canvas id="fc1"></canvas></div></div><div class="card"><h3 class="mono">DAGLIG DIFF (UTFALL - PROGNOS)</h3><div class="chart-wrap"><canvas id="fc2"></canvas></div></div><div class="card"><h3 class="mono">PROGNOS VS UTFALL &mdash; LABOR</h3><div class="chart-wrap"><canvas id="fc3"></canvas></div></div><div class="card"><h3 class="mono">SUMMERING</h3><div style="margin-top:8px">'+stats+'</div></div></div>';

  makeChart("fc1",{type:"line",data:{labels:labels,datasets:[{label:"Prognos",data:fcSales,borderColor:"#6b6b6b",borderDash:[6,4],tension:0.3,pointRadius:0},{label:"Utfall",data:actSales,borderColor:"#C41E3A",tension:0.3,pointRadius:3}]},options:JSON.parse(JSON.stringify(baseOpts))});
  makeChart("fc2",{type:"bar",data:{labels:labels,datasets:[{label:"Diff (kr)",data:diffSales,backgroundColor:diffSales.map(function(v){return v>=0?"#22C55E":"#EF4444";}),borderRadius:6}]},options:JSON.parse(JSON.stringify(baseOpts))});
  makeChart("fc3",{type:"line",data:{labels:labels,datasets:[{label:"Prognos",data:fcLabor,borderColor:"#6b6b6b",borderDash:[6,4],tension:0.3,pointRadius:0},{label:"Utfall",data:actLabor,borderColor:"#A855F7",tension:0.3,pointRadius:3}]},options:JSON.parse(JSON.stringify(baseOpts))});
}

function renderTicketTime(el){
  var labels=dayLabels();
  var selR=document.getElementById("restFilter").value;

  // Daily district avg from daily rows
  var dailyTT=selR==="all"?DATA.daily.map(function(d){return d.ticketTime||0;}):getRestData(selR,"ticketTime");

  // Per-restaurant averages
  var restAvgs=DATA.restaurants.map(function(name,i){
    var vals=[];
    for(var d in (DATA.perRest[name]&&DATA.perRest[name].ticketTime||{})){
      var v=DATA.perRest[name].ticketTime[d];
      if(v>0)vals.push(v);
    }
    var avg=vals.length?vals.reduce(function(a,v){return a+v;},0)/vals.length:0;
    var min=vals.length?Math.min.apply(null,vals):0;
    var max=vals.length?Math.max.apply(null,vals):0;
    return{name:name,avg:avg,min:min,max:max,days:vals.length};
  });

  // Per-restaurant daily lines
  var restDS=DATA.restaurants.map(function(name,i){
    return{label:name,data:getRestData(name,"ticketTime"),borderColor:CC[i%CC.length],tension:0.3,pointRadius:2,borderWidth:2.5};
  });

  // Table rows
  var tableRows=restAvgs.map(function(r,i){
    var avgMin=Math.floor(r.avg/60);var avgSec=Math.round(r.avg%60);
    var minMin=Math.floor(r.min/60);var minSec=Math.round(r.min%60);
    var maxMin=Math.floor(r.max/60);var maxSec=Math.round(r.max%60);
    return'<tr><td style="font-weight:700;color:'+CC[i%CC.length]+'">'+r.name+'</td><td style="font-weight:700;font-size:15px">'+avgMin+':'+String(avgSec).padStart(2,"0")+'</td><td>'+fmt(Math.round(r.avg))+' sek</td><td style="color:var(--green)">'+minMin+':'+String(minSec).padStart(2,"0")+'</td><td style="color:var(--red)">'+maxMin+':'+String(maxSec).padStart(2,"0")+'</td><td>'+r.days+'</td></tr>';
  }).join("");

  el.innerHTML='<div class="grid grid-2">'+
    '<div class="card"><h3 class="mono">DAGLIG TICKET TIME (SEKUNDER)</h3><div class="chart-wrap"><canvas id="tt1"></canvas></div></div>'+
    '<div class="card"><h3 class="mono">SNITT PER RESTAURANG</h3><div class="chart-wrap"><canvas id="tt2"></canvas></div></div>'+
    '<div class="card card-full"><h3 class="mono">TICKET TIME PER RESTAURANG &mdash; DAGLIG TREND</h3><div class="chart-wrap"><canvas id="tt3"></canvas></div></div>'+
    '<div class="card card-full" style="overflow-x:auto"><h3 class="mono">TICKET TIME &Ouml;VERSIKT</h3>'+
    '<table class="mono"><thead><tr><th>Restaurang</th><th>Snitt (mm:ss)</th><th>Snitt (sek)</th><th>B&auml;sta dag</th><th>S&auml;msta dag</th><th>Dagar</th></tr></thead><tbody>'+tableRows+'</tbody></table></div>'+
  '</div>';

  // Daily trend line
  var ttOpts=JSON.parse(JSON.stringify(baseOpts));
  makeChart("tt1",{type:"line",data:{labels:labels,datasets:[{label:"Ticket Time (sek)",data:dailyTT,borderColor:"#C41E3A",backgroundColor:"rgba(196,30,58,0.15)",fill:true,tension:0.3,pointRadius:3,borderWidth:2.5}]},options:ttOpts});

  // Bar chart per restaurant
  makeChart("tt2",{type:"bar",data:{labels:restAvgs.map(function(r){return r.name;}),datasets:[{label:"Snitt (sek)",data:restAvgs.map(function(r){return Math.round(r.avg);}),backgroundColor:restAvgs.map(function(_,i){return CC[i%CC.length];}),borderRadius:6}]},options:JSON.parse(JSON.stringify(baseOpts))});

  // Multi-line per restaurant
  makeChart("tt3",{type:"line",data:{labels:labels,datasets:restDS},options:JSON.parse(JSON.stringify(baseOpts))});
}

function renderComments(el){
  if(!DATA.comments.length){el.innerHTML='<div class="card"><h3 class="mono">KOMMENTARER</h3><p style="color:var(--dim)">Inga kommentarer hittade i loggboken.</p></div>';return;}
  var cards=DATA.comments.map(function(c){
    var d=DATA.daily.find(function(dd){return dd.day===c.day;});
    var diffPct=d&&d.fcSales?((d.sales-d.fcSales)/d.fcSales*100):0;
    var diffTag=diffPct>=0?'<span style="color:var(--green)">+'+diffPct.toFixed(1)+'%</span>':'<span style="color:var(--red)">'+diffPct.toFixed(1)+'%</span>';
    return'<div class="comment-card"><div class="comment-date mono">'+c.date+' ('+c.weekday+') &mdash; Fsg: '+fmt(d?d.sales:0)+' kr '+diffTag+' vs prognos &mdash; LC: '+fmtPct(d?d.laborPct:0)+'</div><div class="comment-text">'+c.text+'</div></div>';
  }).join("");
  el.innerHTML='<div class="card"><h3 class="mono">DAGSRAPPORTER &amp; KOMMENTARER</h3>'+cards+'</div>';
}

// Auto-load the uploaded file
Chart.defaults.font.family="'Plus Jakarta Sans','Segoe UI',sans-serif";
render();

// Try to auto-load if file was pre-loaded
</script>
</body>
</html>
