<html lang="sv">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mister York — District Dashboard</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
<style>
*{margin:0;padding:0;box-sizing:border-box;}
:root{--bg:#0f0f0f;--card:#1a1a1a;--border:#2a2a2a;--borderL:#3a3a3a;--text:#f5f5f5;--muted:#a0a0a0;--dim:#6b6b6b;--accent:#C41E3A;--accentD:#8B1528;--accentL:#E8354F;--green:#22C55E;--red:#EF4444;--amber:#F59E0B;--purple:#A855F7;--blue:#3B82F6;}
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
.multiselect{position:relative;display:inline-block;}
.multiselect-btn{background:rgba(255,255,255,0.15);color:#fff;border:1px solid rgba(255,255,255,0.25);border-radius:8px;padding:8px 32px 8px 12px;font-size:13px;cursor:pointer;font-family:'Plus Jakarta Sans',sans-serif;font-weight:600;backdrop-filter:blur(4px);white-space:nowrap;min-width:180px;text-align:left;position:relative;}
.multiselect-btn::after{content:"";position:absolute;right:12px;top:50%;transform:translateY(-50%);border-left:4px solid transparent;border-right:4px solid transparent;border-top:5px solid rgba(255,255,255,0.7);}
.multiselect-btn:hover{background:rgba(255,255,255,0.25);}
.multiselect-panel{display:none;position:absolute;top:calc(100% + 4px);left:0;background:#1a1a1a;border:1px solid #3a3a3a;border-radius:10px;min-width:220px;z-index:100;box-shadow:0 8px 30px rgba(0,0,0,0.5);padding:6px 0;max-height:320px;overflow-y:auto;}
.multiselect-panel.open{display:block;}
.multiselect-item{display:flex;align-items:center;gap:10px;padding:9px 16px;cursor:pointer;transition:background 0.15s;font-size:13px;font-weight:500;color:#a0a0a0;}
.multiselect-item:hover{background:#252525;}
.multiselect-item.checked{color:#f5f5f5;}
.multiselect-item .cb{width:18px;height:18px;border-radius:5px;border:2px solid #3a3a3a;display:flex;align-items:center;justify-content:center;flex-shrink:0;transition:all 0.15s;}
.multiselect-item.checked .cb{background:var(--accent);border-color:var(--accent);}
.multiselect-item.checked .cb::after{content:"";display:block;width:5px;height:9px;border:solid #fff;border-width:0 2px 2px 0;transform:rotate(45deg);margin-top:-2px;}
.multiselect-divider{height:1px;background:#2a2a2a;margin:4px 0;}
.multiselect-all{font-weight:700;color:#f5f5f5;}
.main{padding:24px 32px;max-width:1400px;margin:0 auto;}
.dropzone{border:2px dashed var(--border);border-radius:12px;padding:24px;text-align:center;margin-bottom:24px;transition:all 0.2s;}
.dropzone.over{border-color:var(--accent);background:rgba(196,30,58,0.07);}
.month-chips{display:flex;gap:6px;margin-bottom:20px;flex-wrap:wrap;align-items:center;}
.month-chip{padding:7px 18px;border-radius:8px;border:none;background:var(--card);color:var(--muted);font-size:13px;font-weight:600;cursor:pointer;transition:all 0.2s;font-family:'Plus Jakarta Sans',sans-serif;}
.month-chip:hover{color:var(--text);background:#252525;}
.month-chip.active{background:var(--accent);color:#fff;box-shadow:0 2px 12px rgba(196,30,58,0.3);}
.month-label{font-size:11px;color:var(--dim);font-weight:700;text-transform:uppercase;letter-spacing:1px;margin-right:4px;}
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
.file-list{display:flex;gap:6px;flex-wrap:wrap;margin-top:12px;}
.file-tag{background:rgba(196,30,58,0.15);border:1px solid rgba(196,30,58,0.3);color:var(--accentL);padding:4px 12px;border-radius:6px;font-size:11px;font-weight:600;}
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
    <div class="multiselect" id="multiselect">
      <button class="multiselect-btn" onclick="togglePanel()">Alla restauranger</button>
      <div class="multiselect-panel" id="msPanel"></div>
    </div>
    <label class="upload-btn mono">&uarr; Ladda upp Loggbok<input type="file" accept=".csv,.tsv,.txt" multiple onchange="loadFiles(this.files);this.value='';"></label>
  </div>
</div></div>
<div class="main">
  <div id="dropzone" class="dropzone">
    <div id="dzText" class="mono" style="font-size:14px;color:var(--muted)">&#128194; Dra och sl&auml;pp dina Loggbok-CSV:er h&auml;r</div>
    <div style="font-size:11px;color:var(--dim);margin-top:6px">Du kan ladda upp flera m&aring;nader samtidigt &mdash; Arkiv &rarr; Ladda ned &rarr; CSV</div>
    <div id="uploadError" class="error"></div>
    <div id="fileList" class="file-list"></div>
  </div>
  <div id="monthBar" class="month-chips" style="display:none"></div>
  <div class="tabs" id="tabs"></div>
  <div class="kpis" id="kpis"></div>
  <div id="content"></div>
  <div class="footer mono" id="footer"></div>
</div>
<script>
var CC=["#C41E3A","#22C55E","#3B82F6","#F59E0B","#A855F7","#EC4899","#06B6D4"];
var VIEWS=["overview","compare","trends","forecast","tickettime","comments"];
var VIEW_LABELS=["&Ouml;versikt","J&auml;mf&ouml;r","Trender","Prognos vs Utfall","Ticket Time","Kommentarer"];
var MONTH_NAMES=["","Jan","Feb","Mar","Apr","Maj","Jun","Jul","Aug","Sep","Okt","Nov","Dec"];

var currentView="overview";
var charts={};
var MONTHS={};
var currentMonth="";
var selectedRests=[];

// Multi-select
function togglePanel(){document.getElementById("msPanel").classList.toggle("open");}
document.addEventListener("click",function(e){if(!document.getElementById("multiselect").contains(e.target))document.getElementById("msPanel").classList.remove("open");});

function buildPanel(){
  var D=getData();if(!D)return;
  var panel=document.getElementById("msPanel");
  var allChecked=selectedRests.length===D.restaurants.length;
  var html='<div class="multiselect-item multiselect-all'+(allChecked?" checked":"")+'" onclick="toggleAll()"><div class="cb"></div>Alla restauranger</div><div class="multiselect-divider"></div>';
  D.restaurants.forEach(function(r){
    var checked=selectedRests.indexOf(r)>=0;
    html+='<div class="multiselect-item'+(checked?" checked":"")+'" onclick="toggleRest(\''+r.replace(/'/g,"\\'")+'\')"><div class="cb"></div>'+r+'</div>';
  });
  panel.innerHTML=html;
  var btn=document.querySelector(".multiselect-btn");
  if(selectedRests.length===D.restaurants.length||selectedRests.length===0)btn.textContent="Alla restauranger";
  else if(selectedRests.length===1)btn.textContent=selectedRests[0];
  else btn.textContent=selectedRests.length+" restauranger";
}
function toggleAll(){var D=getData();if(!D)return;if(selectedRests.length===D.restaurants.length)selectedRests=[];else selectedRests=D.restaurants.slice();buildPanel();render();}
function toggleRest(name){var D=getData();if(!D)return;var idx=selectedRests.indexOf(name);if(idx>=0)selectedRests.splice(idx,1);else selectedRests.push(name);if(selectedRests.length===0)selectedRests=D.restaurants.slice();buildPanel();render();}
function getSelected(){var D=getData();if(!D)return[];if(selectedRests.length===0||selectedRests.length===D.restaurants.length)return D.restaurants.slice();return selectedRests;}
function isAll(){var D=getData();return!D||getSelected().length===D.restaurants.length;}

function getData(){return MONTHS[currentMonth]||null;}
function getMonthKeys(){return Object.keys(MONTHS).sort(function(a,b){var pa=a.split("/"),pb=b.split("/");var ya=parseInt(pa[1]),yb=parseInt(pb[1]),ma=parseInt(pa[0]),mb=parseInt(pb[0]);return ya!==yb?ya-yb:ma-mb;});}

var fmt=function(n){return n!=null?Math.round(n).toLocaleString("sv-SE"):"\u2014";};
var fmtK=function(n){return n>=1000?(n/1000).toFixed(1)+"k":fmt(n);};
var fmtM=function(n){return(n/1000000).toFixed(2)+" mkr";};
var fmtPct=function(n){return n!=null?n.toFixed(1)+"%":"\u2014";};

function parseSEK(s){
  if(!s||s==="-"||s==="#DIV/0!"||s==="#REF!")return 0;
  s=String(s).replace(/"/g,"").replace(/kr/g,"").replace(/%/g,"").replace(/\u00a0/g," ").replace(/\u2212/g,"-").trim();
  s=s.replace(/\s/g,"");
  var commaMatch=s.match(/^([\-]?[\d]+),(\d{1,2})$/);
  if(commaMatch){s=commaMatch[1]+"."+commaMatch[2];}
  else{s=s.replace(/,/g,"");}
  return parseFloat(s)||0;
}

function parseCSVRow(line){
  var result=[];var current="";var inQuotes=false;
  for(var i=0;i<line.length;i++){var c=line[i];if(c==='"'){inQuotes=!inQuotes;}else if(c===","&&!inQuotes){result.push(current);current="";}else{current+=c;}}
  result.push(current);return result;
}

function detectMonthFromFilename(filename){
  if(!filename)return null;
  var fn=filename.toUpperCase();
  var monthMap={"JAN":1,"JANUARI":1,"FEB":2,"FEBRUARI":2,"MAR":3,"MARS":3,"APR":4,"APRIL":4,"MAJ":5,"JUN":6,"JUNI":6,"JUL":7,"JULI":7,"AUG":8,"AUGUSTI":8,"SEP":9,"SEPTEMBER":9,"OKT":10,"OKTOBER":10,"NOV":11,"NOVEMBER":11,"DEC":12,"DECEMBER":12};
  var foundMonth=null,foundYear=null;
  for(var name in monthMap){
    if(fn.indexOf(name)>=0){foundMonth=monthMap[name];break;}
  }
  var yrMatch=fn.match(/(20\d{2})/);
  if(yrMatch)foundYear=parseInt(yrMatch[1]);
  if(foundMonth&&foundYear)return{month:foundMonth,year:foundYear};
  return null;
}

function detectRestFromFilename(filename){
  if(!filename)return null;
  var fn=filename.replace(/\.[^.]+$/,"").replace(/_/g," ").replace(/-/g," ");
  var skip=["loggbok","stockholm","mars","februari","januari","april","maj","juni","juli","augusti","september","oktober","november","december","jan","feb","mar","apr","jun","jul","aug","sep","okt","nov","dec","2024","2025","2026","2027","csv","version"];
  var words=fn.split(/\s+/).filter(function(w){return w.length>1&&skip.indexOf(w.toLowerCase())<0&&!/^\d+$/.test(w);});
  return words.length>0?words.join(" "):null;
}

function parseLoggbok(text,filename){
  var lines=text.replace(/\r/g,"").split("\n");
  var data={restaurants:[],days:[],daily:[],perRest:{},month:"",year:"",monthKey:"",comments:[]};
  var headerIdx=-1;
  for(var i=0;i<Math.min(lines.length,25);i++){if(lines[i].indexOf("DATUM")>=0||lines[i].indexOf("Datum")>=0){headerIdx=i;break;}}
  if(headerIdx<0)return null;

  // Try to detect month from filename first
  var fnMonth=detectMonthFromFilename(filename);

  var SV_MONTHS={"jan":1,"januari":1,"feb":2,"februari":2,"mar":3,"mars":3,"apr":4,"april":4,"maj":5,"jun":6,"juni":6,"jul":7,"juli":7,"aug":8,"augusti":8,"sep":9,"september":9,"okt":10,"oktober":10,"nov":11,"november":11,"dec":12,"december":12};

  function parseDate(str){
    if(!str)return null;
    str=str.trim();
    // Format: "2/1/2026" (month/day/year)
    if(str.indexOf("/")>=0){
      var p=str.split("/");if(p.length>=3){var mo=parseInt(p[0]),dy=parseInt(p[1]),yr=parseInt(p[2]);if(!isNaN(mo)&&!isNaN(dy)&&!isNaN(yr))return{day:dy,month:mo,year:yr};}
    }
    // Format: "1 mars" or "15 mars" (day + swedish month)
    var m=str.match(/^(\d{1,2})\s+([a-zåäö]+)/i);
    if(m){var dy2=parseInt(m[1]);var moName=m[2].toLowerCase();if(SV_MONTHS[moName])return{day:dy2,month:SV_MONTHS[moName],year:fnMonth?fnMonth.year:2026};}
    // Format: just a number (day of month)
    var num=parseInt(str);
    if(!isNaN(num)&&num>=1&&num<=31)return{day:num,month:fnMonth?fnMonth.month:0,year:fnMonth?fnMonth.year:0};
    return null;
  }

  // Auto-detect column layout by scanning first data row
  // District: sales at col 12, Single restaurant: sales at col 13 (extra AF-stöd column)
  var layout="district";
  for(var i=headerIdx+1;i<lines.length;i++){
    var testRow=parseCSVRow(lines[i]);
    var testDate=(testRow[2]||"").trim();
    if(!parseDate(testDate))continue;
    var v12=parseSEK(testRow[12]),v13=parseSEK(testRow[13]);
    if(v12===0&&v13>0){layout="single";}
    break;
  }

  // Column mappings
  var COL;
  if(layout==="single"){
    COL={fcSales:5,fcHours:7,fcCost:8,fcPct:9,fcWaste:10,sales:13,absence:15,hours:16,laborCost:17,laborPct:18,waste:20,wastePct:21,ticketTime:37,commentStart:45};
  }else{
    COL={fcSales:5,fcHours:6,fcCost:7,fcPct:8,fcWaste:9,sales:12,absence:13,hours:14,laborCost:15,laborPct:16,waste:17,wastePct:18,ticketTime:33,commentStart:37};
  }

  for(var i=headerIdx+1;i<lines.length;i++){
    var row=parseCSVRow(lines[i]);
    var dateStr=(row[2]||"").trim();
    var parsed=parseDate(dateStr);
    if(!parsed)continue;
    var dy=parsed.day,mo=parsed.month,yr=parsed.year;
    var sales=parseSEK(row[COL.sales]);
    var fcSales=parseSEK(row[COL.fcSales]);
    if(sales===0&&fcSales===0)continue;

    // Use filename month if available (overrides the date column which may be wrong)
    if(!data.month){
      if(fnMonth){
        data.month=String(fnMonth.month);data.year=String(fnMonth.year);data.monthKey=fnMonth.month+"/"+fnMonth.year;
      }else if(mo&&yr){
        data.month=String(mo);data.year=String(yr);data.monthKey=mo+"/"+yr;
      }
    }

    var comment="";
    for(var ci=COL.commentStart;ci<row.length;ci++){
      var cv=(row[ci]||"").trim();
      if(cv.length>3&&cv.indexOf("#DIV")<0&&cv.indexOf("#REF")<0&&cv.indexOf("%")<0&&!cv.match(/^[\d.,\-]+$/)&&!cv.match(/^\d+\.\d+%?$/)){comment=cv;break;}
    }

    data.daily.push({
      day:dy,date:dateStr,weekday:(row[3]||"").trim(),
      fcSales:fcSales,fcLaborHours:parseSEK(row[COL.fcHours]),fcLaborCost:parseSEK(row[COL.fcCost]),fcLaborPct:parseSEK(row[COL.fcPct]),fcWaste:parseSEK(row[COL.fcWaste]),
      sales:sales,absenceHours:parseSEK(row[COL.absence]),laborHours:parseSEK(row[COL.hours]),laborCost:parseSEK(row[COL.laborCost]),laborPct:parseSEK(row[COL.laborPct]),
      wasteCost:parseSEK(row[COL.waste]),wastePct:parseSEK(row[COL.wastePct]),status:"",ticketTime:parseSEK(row[COL.ticketTime]),comment:comment
    });
    if(comment)data.comments.push({day:dy,date:dateStr,weekday:(row[3]||"").trim(),text:comment});
  }
  data.days=data.daily.map(function(d){return d.day;});
  if(!data.daily.length)return null;

  var sections=["Sales","Actual labor cost","Waste","Productive hours used","Absence hours","Ticket Time (sec)"];
  var keys=["sales","laborCost","waste","hours","absenceHours","ticketTime"];
  for(var si=0;si<sections.length;si++){
    var sName=sections[si],key=keys[si],startIdx=-1;
    for(var i=0;i<lines.length;i++){
      var row=parseCSVRow(lines[i]);
      if(row[28]&&row[28].trim().toLowerCase()===sName.toLowerCase()){startIdx=i;break;}
      if(row[28]&&row[28].trim().toLowerCase().indexOf(sName.toLowerCase().substring(0,6))===0){startIdx=i;break;}
    }
    if(startIdx<0)continue;
    var foundCount=0;
    for(var i=startIdx+1;i<Math.min(startIdx+20,lines.length);i++){
      var row=parseCSVRow(lines[i]);
      var name=(row[28]||"").trim();
      if(!name||name===""){if(foundCount>0)break;continue;}
      if(name.indexOf("Total")===0)break;
      if(["Sales","Actual labor cost","Waste","Productive hours used","Productive hours","Absence hours","Internal processes","Grant support","Optiqo Cleaning","Google review","Ticket Time (sec)","Total ticket time"].indexOf(name)>=0)break;
      foundCount++;
      if(data.restaurants.indexOf(name)<0)data.restaurants.push(name);
      if(!data.perRest[name])data.perRest[name]={};
      if(!data.perRest[name][key])data.perRest[name][key]={};
      for(var d=1;d<=31;d++){var val=parseSEK(row[28+d]);if(val)data.perRest[name][key][d]=val;}
    }
  }

  // Fallback: if no per-restaurant data found (single restaurant file),
  // use the daily totals as a single restaurant entry
  if(data.restaurants.length===0){
    var restName=detectRestFromFilename(filename)||"Min restaurang";
    data.restaurants.push(restName);
    data.perRest[restName]={sales:{},laborCost:{},waste:{},hours:{},absenceHours:{},ticketTime:{}};
    data.daily.forEach(function(d){
      if(d.sales)data.perRest[restName].sales[d.day]=d.sales;
      if(d.laborCost)data.perRest[restName].laborCost[d.day]=d.laborCost;
      if(d.wasteCost)data.perRest[restName].waste[d.day]=d.wasteCost;
      if(d.laborHours)data.perRest[restName].hours[d.day]=d.laborHours;
      if(d.absenceHours)data.perRest[restName].absenceHours[d.day]=d.absenceHours;
      if(d.ticketTime)data.perRest[restName].ticketTime[d.day]=d.ticketTime;
    });
  }

  return data;
}

function loadFiles(files){
  var errEl=document.getElementById("uploadError");
  if(errEl)errEl.textContent="";
  var total=files.length;
  var done=0;
  var added=0;
  Array.from(files).forEach(function(file){
    var fname=file.name;
    var reader=new FileReader();
    reader.onload=function(e){
      try{
        var parsed=parseLoggbok(e.target.result,fname);
        if(parsed&&parsed.daily.length){
          MONTHS[parsed.monthKey]=parsed;
          currentMonth=parsed.monthKey;
          added++;
        }
      }catch(ex){}
      done++;
      if(done===total){
        if(added===0){var el=document.getElementById("uploadError");if(el)el.textContent="Kunde inte l\u00e4sa filen. Kontrollera att det \u00e4r en Loggbok-export.";return;}
        selectedRests=getData().restaurants.slice();
        buildPanel();renderMonthBar();render();
        updateDropzone();
      }
    };
    reader.readAsText(file);
  });
}

function updateDropzone(){
  var keys=getMonthKeys();
  var dzText=document.getElementById("dzText");
  var fileList=document.getElementById("fileList");
  if(dzText&&keys.length>0)dzText.textContent="\uD83D\uDCC2 "+keys.length+" m\u00e5nad(er) laddade \u2014 dra fler h\u00e4r";
  if(fileList){
    fileList.innerHTML=keys.map(function(k){var p=k.split("/");return'<span class="file-tag">'+MONTH_NAMES[parseInt(p[0])]+" "+p[1]+'</span>';}).join("");
  }
}

// Dropzone drag & drop via JS event listeners (survives any DOM changes)
(function(){
  var dz=document.getElementById("dropzone");
  dz.addEventListener("dragover",function(e){e.preventDefault();e.stopPropagation();dz.classList.add("over");});
  dz.addEventListener("dragleave",function(e){e.preventDefault();e.stopPropagation();dz.classList.remove("over");});
  dz.addEventListener("drop",function(e){e.preventDefault();e.stopPropagation();dz.classList.remove("over");if(e.dataTransfer&&e.dataTransfer.files&&e.dataTransfer.files.length)loadFiles(e.dataTransfer.files);});
})();

function selectMonth(key){currentMonth=key;selectedRests=getData().restaurants.slice();buildPanel();renderMonthBar();render();}

function renderMonthBar(){
  var keys=getMonthKeys();
  var bar=document.getElementById("monthBar");
  if(keys.length<1){bar.style.display="none";return;}
  bar.style.display="flex";
  var html='<span class="month-label">M&aring;nad:</span>';
  keys.forEach(function(k){
    var parts=k.split("/");var mo=parseInt(parts[0]);
    var label=MONTH_NAMES[mo]+" "+parts[1];
    html+='<button class="month-chip'+(k===currentMonth?" active":"")+'" onclick="selectMonth(\''+k+'\')">'+label+'</button>';
  });
  html+='<label class="month-chip" style="color:var(--accent);border:1px dashed var(--accent);background:transparent">+ L&auml;gg till m&aring;nad<input type="file" accept=".csv,.tsv,.txt" multiple style="display:none" onchange="loadFiles(this.files);this.value=\'\';"></label>';
  bar.innerHTML=html;
}

function destroyCharts(){for(var k in charts){try{charts[k].destroy();}catch(e){}}charts={};}
function makeChart(id,config){var canvas=document.getElementById(id);if(!canvas)return;charts[id]=new Chart(canvas.getContext("2d"),config);}
var baseOpts={responsive:true,maintainAspectRatio:false,plugins:{legend:{labels:{color:"#a0a0a0",font:{size:11,family:"'Plus Jakarta Sans',sans-serif",weight:600}}},tooltip:{backgroundColor:"#1a1a1a",borderColor:"#C41E3A",borderWidth:1,titleColor:"#fff",bodyColor:"#a0a0a0",bodyFont:{family:"'Plus Jakarta Sans',sans-serif",size:12},cornerRadius:8,padding:12}},scales:{x:{ticks:{color:"#6b6b6b",font:{size:11}},grid:{color:"rgba(255,255,255,0.04)"}},y:{ticks:{color:"#6b6b6b",font:{size:11}},grid:{color:"rgba(255,255,255,0.04)"}}}};

function getRestData(name,key){var D=getData();if(!D||!D.perRest[name]||!D.perRest[name][key])return[];return D.days.map(function(d){return D.perRest[name][key][d]||0;});}
function getSelectedDayData(key){var D=getData();if(!D)return[];var sel=getSelected();return D.days.map(function(d){var sum=0;sel.forEach(function(r){if(D.perRest[r]&&D.perRest[r][key])sum+=(D.perRest[r][key][d]||0);});return sum;});}
function totalForRest(name,key){var D=getData();if(!D||!D.perRest[name]||!D.perRest[name][key])return 0;var sum=0;for(var d in D.perRest[name][key])sum+=D.perRest[name][key][d];return sum;}
function totalForSelected(key){var sum=0;getSelected().forEach(function(r){sum+=totalForRest(r,key);});return sum;}

function kpiHTML(icon,label,value,sub,grad){
  return'<div class="kpi"><div class="kpi-bar" style="background:'+grad+'"></div><div class="kpi-label mono">'+icon+' '+label+'</div><div class="kpi-value mono" style="color:var(--text)">'+value+'</div>'+(sub?'<div class="kpi-sub">'+sub+'</div>':"")+'</div>';
}

function render(){
  destroyCharts();
  var D=getData();
  if(!D){
    document.getElementById("kpis").innerHTML="";document.getElementById("tabs").innerHTML="";
    document.getElementById("content").innerHTML='<div style="text-align:center;color:var(--dim);padding:60px 0;font-size:15px;font-weight:600">Ladda upp en eller flera Loggbok-CSV:er f&ouml;r att komma ig&aring;ng</div>';
    document.getElementById("footer").textContent="";return;
  }

  var totalSales=0,totalLabor=0,totalWaste=0,totalHours=0;
  if(isAll()){D.daily.forEach(function(d){totalSales+=d.sales;totalLabor+=d.laborCost;totalWaste+=d.wasteCost;totalHours+=d.laborHours;});}
  else{totalSales=totalForSelected("sales");totalLabor=totalForSelected("laborCost");totalWaste=totalForSelected("waste");totalHours=totalForSelected("hours");}
  var laborPct=totalSales?totalLabor/totalSales*100:0;
  var wastePct=totalSales?totalWaste/totalSales*100:0;
  var salesPerHour=totalHours?totalSales/totalHours:0;

  var avgTT=0;
  if(isAll()){var ttD=D.daily.filter(function(d){return d.ticketTime>0;});avgTT=ttD.length?ttD.reduce(function(a,d){return a+d.ticketTime;},0)/ttD.length:0;}
  else{var ttC=0,ttS=0;getSelected().forEach(function(r){var td=D.perRest[r]&&D.perRest[r].ticketTime||{};for(var d in td){if(td[d]>0){ttS+=td[d];ttC++;}}});avgTT=ttC?ttS/ttC:0;}

  var mo=parseInt(D.month);
  var monthLabel=MONTH_NAMES[mo]+" "+D.year;
  document.getElementById("demoTag").textContent=" \u00B7 "+monthLabel;

  var ttMin=Math.floor(avgTT/60),ttSec=Math.round(avgTT%60);
  document.getElementById("kpis").innerHTML=[
    kpiHTML("&#128176;","F&ouml;rs&auml;ljning",fmtM(totalSales),"","linear-gradient(90deg,var(--accent),transparent)"),
    kpiHTML("&#128101;","Labor %",fmtPct(laborPct),fmt(totalLabor)+" kr","linear-gradient(90deg,"+(laborPct>30?"var(--red)":"var(--green)")+",transparent)"),
    kpiHTML("&#128465;","Waste %",fmtPct(wastePct),fmt(totalWaste)+" kr","linear-gradient(90deg,"+(wastePct>0.7?"var(--red)":"var(--green)")+",transparent)"),
    kpiHTML("&#9201;","Fsg/timme",fmt(salesPerHour)+" kr",fmt(totalHours)+" timmar","linear-gradient(90deg,var(--purple),transparent)"),
    kpiHTML("&#128337;","Ticket Time",ttMin+":"+String(ttSec).padStart(2,"0"),fmt(Math.round(avgTT))+" sek","linear-gradient(90deg,"+(avgTT>360?"var(--red)":"var(--green)")+",transparent)")
  ].join("");

  document.getElementById("tabs").innerHTML=VIEWS.map(function(v,i){return'<button class="tab'+(currentView===v?" active":"")+'" onclick="currentView=\''+v+'\';render()">'+VIEW_LABELS[i]+'</button>';}).join("");

  var el=document.getElementById("content");
  if(currentView==="overview")renderOverview(el);
  else if(currentView==="compare")renderCompare(el);
  else if(currentView==="trends")renderTrends(el);
  else if(currentView==="forecast")renderForecast(el);
  else if(currentView==="tickettime")renderTicketTime(el);
  else if(currentView==="comments")renderComments(el);

  document.getElementById("footer").textContent="Mister York \u00B7 District Dashboard \u00B7 "+monthLabel+" \u00B7 "+D.restaurants.length+" restauranger \u00B7 "+D.daily.length+" dagar \u00B7 "+Object.keys(MONTHS).length+" m\u00e5nad(er)";
}

function dayLabels(){var D=getData();return D?D.days.map(function(d){return d;}):[]; }

function renderOverview(el){
  var D=getData();if(!D)return;var labels=dayLabels();var sel=getSelected();
  var salesArr=isAll()?D.daily.map(function(d){return d.sales;}):getSelectedDayData("sales");
  var laborArr=isAll()?D.daily.map(function(d){return d.laborCost;}):getSelectedDayData("laborCost");
  var laborPctArr=D.daily.map(function(d,i){var s=salesArr[i];return s?(laborArr[i]/s*100):0;});

  el.innerHTML='<div class="grid grid-2"><div class="card"><h3 class="mono">DAGLIG F&Ouml;RS&Auml;LJNING</h3><div class="chart-wrap"><canvas id="o1"></canvas></div></div><div class="card"><h3 class="mono">LABOR % PER DAG</h3><div class="chart-wrap"><canvas id="o2"></canvas></div></div><div class="card card-full"><h3 class="mono">F&Ouml;RS&Auml;LJNING PER RESTAURANG</h3><div class="chart-wrap"><canvas id="o3"></canvas></div></div></div>';
  makeChart("o1",{type:"line",data:{labels:labels,datasets:[{label:"F\u00f6rs\u00e4ljning",data:salesArr,borderColor:"#C41E3A",backgroundColor:"rgba(196,30,58,0.15)",fill:true,tension:0.3,pointRadius:3}]},options:JSON.parse(JSON.stringify(baseOpts))});
  makeChart("o2",{type:"line",data:{labels:labels,datasets:[{label:"Labor %",data:laborPctArr,borderColor:"#A855F7",backgroundColor:"rgba(168,85,247,0.15)",fill:true,tension:0.3,pointRadius:3}]},options:JSON.parse(JSON.stringify(baseOpts))});
  var ds=sel.map(function(name){var i=D.restaurants.indexOf(name);return{label:name,data:getRestData(name,"sales"),borderColor:CC[i%CC.length],tension:0.3,pointRadius:0};});
  makeChart("o3",{type:"line",data:{labels:labels,datasets:ds},options:JSON.parse(JSON.stringify(baseOpts))});
}

function renderCompare(el){
  var D=getData();if(!D)return;var sel=getSelected();
  var comp=sel.map(function(name){var i=D.restaurants.indexOf(name);var s=totalForRest(name,"sales"),l=totalForRest(name,"laborCost"),w=totalForRest(name,"waste"),h=totalForRest(name,"hours");
    return{name:name,idx:i,sales:s,labor:l,waste:w,hours:h,laborPct:s?+(l/s*100).toFixed(1):0,wastePct:s?+(w/s*100).toFixed(1):0,sph:h?Math.round(s/h):0};});
  var rows=comp.map(function(r){return'<tr><td style="font-weight:600;color:'+CC[r.idx%CC.length]+'">'+r.name+'</td><td>'+fmt(r.sales)+' kr</td><td style="color:'+(r.laborPct>30?"var(--red)":"var(--green)")+'">'+fmtPct(r.laborPct)+'</td><td style="color:'+(r.wastePct>0.7?"var(--red)":"var(--green)")+'">'+fmtPct(r.wastePct)+'</td><td>'+fmt(r.sph)+' kr</td><td>'+fmt(r.hours)+'</td></tr>';}).join("");

  el.innerHTML='<div class="grid"><div class="card"><h3 class="mono">F&Ouml;RS&Auml;LJNING PER RESTAURANG</h3><div class="chart-wrap" style="height:320px"><canvas id="cmp1"></canvas></div></div><div class="card"><h3 class="mono">LABOR % PER RESTAURANG</h3><div class="chart-wrap" style="height:320px"><canvas id="cmp2"></canvas></div></div><div class="card" style="overflow-x:auto"><h3 class="mono">DETALJERAD J&Auml;MF&Ouml;RELSE</h3><table class="mono"><thead><tr><th>Restaurang</th><th>F&ouml;rs&auml;ljning</th><th>Labor %</th><th>Waste %</th><th>Fsg/timme</th><th>Timmar</th></tr></thead><tbody>'+rows+'</tbody></table></div></div>';
  makeChart("cmp1",{type:"bar",data:{labels:comp.map(function(r){return r.name;}),datasets:[{label:"F\u00f6rs\u00e4ljning",data:comp.map(function(r){return r.sales;}),backgroundColor:comp.map(function(r){return CC[r.idx%CC.length];}),borderRadius:6}]},options:JSON.parse(JSON.stringify(baseOpts))});
  makeChart("cmp2",{type:"bar",data:{labels:comp.map(function(r){return r.name;}),datasets:[{label:"Labor %",data:comp.map(function(r){return r.laborPct;}),backgroundColor:comp.map(function(r){return CC[r.idx%CC.length];}),borderRadius:6}]},options:JSON.parse(JSON.stringify(baseOpts))});
}

function renderTrends(el){
  var D=getData();if(!D)return;var labels=dayLabels();
  var salesArr=isAll()?D.daily.map(function(d){return d.sales;}):getSelectedDayData("sales");
  var laborArr=isAll()?D.daily.map(function(d){return d.laborCost;}):getSelectedDayData("laborCost");
  var wasteArr=isAll()?D.daily.map(function(d){return d.wasteCost;}):getSelectedDayData("waste");
  var hoursArr=isAll()?D.daily.map(function(d){return d.laborHours;}):getSelectedDayData("hours");

  el.innerHTML='<div class="grid grid-2"><div class="card"><h3 class="mono">DAGLIG F&Ouml;RS&Auml;LJNING</h3><div class="chart-wrap"><canvas id="tr1"></canvas></div></div><div class="card"><h3 class="mono">DAGLIG LABOR COST</h3><div class="chart-wrap"><canvas id="tr2"></canvas></div></div><div class="card"><h3 class="mono">DAGLIG WASTE</h3><div class="chart-wrap"><canvas id="tr3"></canvas></div></div><div class="card"><h3 class="mono">PRODUKTIVA TIMMAR</h3><div class="chart-wrap"><canvas id="tr4"></canvas></div></div></div>';
  makeChart("tr1",{type:"line",data:{labels:labels,datasets:[{label:"F\u00f6rs\u00e4ljning",data:salesArr,borderColor:"#C41E3A",backgroundColor:"rgba(196,30,58,0.15)",fill:true,tension:0.3,pointRadius:3}]},options:JSON.parse(JSON.stringify(baseOpts))});
  makeChart("tr2",{type:"line",data:{labels:labels,datasets:[{label:"Labor cost",data:laborArr,borderColor:"#A855F7",backgroundColor:"rgba(168,85,247,0.15)",fill:true,tension:0.3,pointRadius:3}]},options:JSON.parse(JSON.stringify(baseOpts))});
  makeChart("tr3",{type:"bar",data:{labels:labels,datasets:[{label:"Waste",data:wasteArr,backgroundColor:"#F59E0B",borderRadius:6}]},options:JSON.parse(JSON.stringify(baseOpts))});
  makeChart("tr4",{type:"line",data:{labels:labels,datasets:[{label:"Timmar",data:hoursArr,borderColor:"#22C55E",backgroundColor:"rgba(34,197,94,0.15)",fill:true,tension:0.3,pointRadius:3}]},options:JSON.parse(JSON.stringify(baseOpts))});
}

function renderForecast(el){
  var D=getData();if(!D)return;var labels=dayLabels();
  var fcSales=D.daily.map(function(d){return d.fcSales;});var actSales=D.daily.map(function(d){return d.sales;});
  var fcLabor=D.daily.map(function(d){return d.fcLaborCost;});var actLabor=D.daily.map(function(d){return d.laborCost;});
  var diffSales=D.daily.map(function(d){return d.sales-d.fcSales;});
  var tFS=0,tAS=0,tFL=0,tAL=0;D.daily.forEach(function(d){tFS+=d.fcSales;tAS+=d.sales;tFL+=d.fcLaborCost;tAL+=d.laborCost;});
  var dT=tAS-tFS;
  var stats=[{l:"Prognos f\u00f6rs\u00e4ljning",v:fmt(tFS)+" kr",c:"var(--dim)"},{l:"Utfall f\u00f6rs\u00e4ljning",v:fmt(tAS)+" kr",c:"var(--accent)"},{l:"Diff f\u00f6rs\u00e4ljning",v:(dT>=0?"+":"")+fmt(dT)+" kr",c:dT>=0?"var(--green)":"var(--red)"},
    {l:"Prognos labor",v:fmt(tFL)+" kr",c:"var(--dim)"},{l:"Utfall labor",v:fmt(tAL)+" kr",c:"var(--purple)"},{l:"Diff labor",v:(tAL-tFL>=0?"+":"")+fmt(tAL-tFL)+" kr",c:tAL>tFL?"var(--red)":"var(--green)"}
  ].map(function(s){return'<div class="stat-row"><span style="font-size:13px;color:var(--muted)">'+s.l+'</span><span class="mono" style="font-size:15px;font-weight:600;color:'+s.c+'">'+s.v+'</span></div>';}).join("");

  el.innerHTML='<div class="grid grid-2"><div class="card"><h3 class="mono">PROGNOS VS UTFALL &mdash; F&Ouml;RS&Auml;LJNING</h3><div class="chart-wrap"><canvas id="fc1"></canvas></div></div><div class="card"><h3 class="mono">DAGLIG DIFF (UTFALL - PROGNOS)</h3><div class="chart-wrap"><canvas id="fc2"></canvas></div></div><div class="card"><h3 class="mono">PROGNOS VS UTFALL &mdash; LABOR</h3><div class="chart-wrap"><canvas id="fc3"></canvas></div></div><div class="card"><h3 class="mono">SUMMERING</h3><div style="margin-top:8px">'+stats+'</div></div></div>';
  makeChart("fc1",{type:"line",data:{labels:labels,datasets:[{label:"Prognos",data:fcSales,borderColor:"#6b6b6b",borderDash:[6,4],tension:0.3,pointRadius:0},{label:"Utfall",data:actSales,borderColor:"#C41E3A",tension:0.3,pointRadius:3}]},options:JSON.parse(JSON.stringify(baseOpts))});
  makeChart("fc2",{type:"bar",data:{labels:labels,datasets:[{label:"Diff (kr)",data:diffSales,backgroundColor:diffSales.map(function(v){return v>=0?"#22C55E":"#EF4444";}),borderRadius:6}]},options:JSON.parse(JSON.stringify(baseOpts))});
  makeChart("fc3",{type:"line",data:{labels:labels,datasets:[{label:"Prognos",data:fcLabor,borderColor:"#6b6b6b",borderDash:[6,4],tension:0.3,pointRadius:0},{label:"Utfall",data:actLabor,borderColor:"#A855F7",tension:0.3,pointRadius:3}]},options:JSON.parse(JSON.stringify(baseOpts))});
}

function renderTicketTime(el){
  var D=getData();if(!D)return;var labels=dayLabels();var sel=getSelected();
  var dailyTT=isAll()?D.daily.map(function(d){return d.ticketTime||0;}):D.days.map(function(d){var sum=0,cnt=0;sel.forEach(function(r){var v=(D.perRest[r]&&D.perRest[r].ticketTime&&D.perRest[r].ticketTime[d])||0;if(v>0){sum+=v;cnt++;}});return cnt?sum/cnt:0;});
  var restAvgs=sel.map(function(name){var i=D.restaurants.indexOf(name);var vals=[];for(var d in(D.perRest[name]&&D.perRest[name].ticketTime||{})){var v=D.perRest[name].ticketTime[d];if(v>0)vals.push(v);}
    var avg=vals.length?vals.reduce(function(a,v){return a+v;},0)/vals.length:0;return{name:name,idx:i,avg:avg,min:vals.length?Math.min.apply(null,vals):0,max:vals.length?Math.max.apply(null,vals):0,days:vals.length};});
  var restDS=sel.map(function(name){var i=D.restaurants.indexOf(name);return{label:name,data:getRestData(name,"ticketTime"),borderColor:CC[i%CC.length],tension:0.3,pointRadius:2,borderWidth:2.5};});

  var tRows=restAvgs.map(function(r){var am=Math.floor(r.avg/60),as=Math.round(r.avg%60),nm=Math.floor(r.min/60),ns=Math.round(r.min%60),xm=Math.floor(r.max/60),xs=Math.round(r.max%60);
    return'<tr><td style="font-weight:700;color:'+CC[r.idx%CC.length]+'">'+r.name+'</td><td style="font-weight:700;font-size:15px">'+am+':'+String(as).padStart(2,"0")+'</td><td>'+fmt(Math.round(r.avg))+' sek</td><td style="color:var(--green)">'+nm+':'+String(ns).padStart(2,"0")+'</td><td style="color:var(--red)">'+xm+':'+String(xs).padStart(2,"0")+'</td><td>'+r.days+'</td></tr>';}).join("");

  el.innerHTML='<div class="grid grid-2"><div class="card"><h3 class="mono">DAGLIG TICKET TIME (SEKUNDER)</h3><div class="chart-wrap"><canvas id="tt1"></canvas></div></div><div class="card"><h3 class="mono">SNITT PER RESTAURANG</h3><div class="chart-wrap"><canvas id="tt2"></canvas></div></div><div class="card card-full"><h3 class="mono">TICKET TIME PER RESTAURANG &mdash; DAGLIG TREND</h3><div class="chart-wrap"><canvas id="tt3"></canvas></div></div><div class="card card-full" style="overflow-x:auto"><h3 class="mono">TICKET TIME &Ouml;VERSIKT</h3><table class="mono"><thead><tr><th>Restaurang</th><th>Snitt (mm:ss)</th><th>Snitt (sek)</th><th>B&auml;sta dag</th><th>S&auml;msta dag</th><th>Dagar</th></tr></thead><tbody>'+tRows+'</tbody></table></div></div>';
  makeChart("tt1",{type:"line",data:{labels:labels,datasets:[{label:"Ticket Time (sek)",data:dailyTT,borderColor:"#C41E3A",backgroundColor:"rgba(196,30,58,0.15)",fill:true,tension:0.3,pointRadius:3,borderWidth:2.5}]},options:JSON.parse(JSON.stringify(baseOpts))});
  makeChart("tt2",{type:"bar",data:{labels:restAvgs.map(function(r){return r.name;}),datasets:[{label:"Snitt (sek)",data:restAvgs.map(function(r){return Math.round(r.avg);}),backgroundColor:restAvgs.map(function(r){return CC[r.idx%CC.length];}),borderRadius:6}]},options:JSON.parse(JSON.stringify(baseOpts))});
  makeChart("tt3",{type:"line",data:{labels:labels,datasets:restDS},options:JSON.parse(JSON.stringify(baseOpts))});
}

function renderComments(el){
  var D=getData();if(!D||!D.comments.length){el.innerHTML='<div class="card"><h3 class="mono">KOMMENTARER</h3><p style="color:var(--dim)">Inga kommentarer hittade.</p></div>';return;}
  var cards=D.comments.map(function(c){
    var d=D.daily.find(function(dd){return dd.day===c.day;});
    var diffPct=d&&d.fcSales?((d.sales-d.fcSales)/d.fcSales*100):0;
    var diffTag=diffPct>=0?'<span style="color:var(--green)">+'+diffPct.toFixed(1)+'%</span>':'<span style="color:var(--red)">'+diffPct.toFixed(1)+'%</span>';
    return'<div class="comment-card"><div class="comment-date mono">'+c.date+' ('+c.weekday+') \u2014 Fsg: '+fmt(d?d.sales:0)+' kr '+diffTag+' vs prognos \u2014 LC: '+fmtPct(d?d.laborPct:0)+'</div><div class="comment-text">'+c.text+'</div></div>';
  }).join("");
  el.innerHTML='<div class="card"><h3 class="mono">DAGSRAPPORTER &amp; KOMMENTARER</h3>'+cards+'</div>';
}

Chart.defaults.font.family="'Plus Jakarta Sans','Segoe UI',sans-serif";
render();
</script>
</body>
</html>
