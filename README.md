<html lang="sv">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mister York — District Dashboard</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
<style>
*{margin:0;padding:0;box-sizing:border-box;}
:root{--bg:#0c1117;--card:#151c25;--cardHover:#1a2330;--border:#1e2a36;--text:#e8ecf0;--muted:#8899a6;--dim:#556677;--accent:#C41E3A;--accentD:#8B1528;--accentL:#E8354F;--green:#22C55E;--red:#EF4444;--amber:#F59E0B;--purple:#A855F7;--blue:#3B82F6;--teal:#14B8A6;}
body{background:var(--bg);color:var(--text);font-family:'Plus Jakarta Sans',-apple-system,sans-serif;min-height:100vh;}
/* Header */
.header{background:var(--card);border-bottom:1px solid var(--border);padding:16px 28px;display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:12px;}
.header-left{display:flex;align-items:center;gap:14px;}
.logo-dot{width:36px;height:36px;border-radius:50%;background:var(--accent);display:flex;align-items:center;justify-content:center;font-weight:800;font-size:16px;color:#fff;}
.brand{font-size:18px;font-weight:800;color:var(--text);letter-spacing:-0.3px;}
.brand span{color:var(--dim);font-weight:500;font-size:13px;margin-left:8px;}
.header-right{display:flex;align-items:center;gap:10px;flex-wrap:wrap;}
/* Date range */
.date-range{display:flex;align-items:center;gap:6px;background:var(--bg);border:1px solid var(--border);border-radius:10px;padding:6px 12px;}
.date-range label{font-size:11px;color:var(--dim);font-weight:600;text-transform:uppercase;letter-spacing:0.5px;}
.date-range input{background:transparent;border:none;color:var(--text);font-family:'Plus Jakarta Sans',sans-serif;font-size:13px;font-weight:600;outline:none;width:120px;}
.date-range input::-webkit-calendar-picker-indicator{filter:invert(0.6);}
.date-sep{color:var(--dim);font-size:13px;margin:0 2px;}
/* Multiselect */
.multiselect{position:relative;display:inline-block;}
.ms-btn{background:var(--bg);color:var(--text);border:1px solid var(--border);border-radius:10px;padding:8px 30px 8px 12px;font-size:13px;cursor:pointer;font-family:'Plus Jakarta Sans',sans-serif;font-weight:600;white-space:nowrap;min-width:170px;text-align:left;position:relative;}
.ms-btn::after{content:"";position:absolute;right:11px;top:50%;transform:translateY(-50%);border-left:4px solid transparent;border-right:4px solid transparent;border-top:5px solid var(--dim);}
.ms-panel{display:none;position:absolute;top:calc(100% + 4px);right:0;background:var(--card);border:1px solid var(--border);border-radius:10px;min-width:220px;z-index:100;box-shadow:0 8px 30px rgba(0,0,0,0.5);padding:6px 0;max-height:320px;overflow-y:auto;}
.ms-panel.open{display:block;}
.ms-item{display:flex;align-items:center;gap:10px;padding:9px 16px;cursor:pointer;transition:background 0.15s;font-size:13px;font-weight:500;color:var(--muted);}
.ms-item:hover{background:var(--cardHover);}
.ms-item.checked{color:var(--text);}
.ms-item .cb{width:17px;height:17px;border-radius:4px;border:2px solid var(--border);display:flex;align-items:center;justify-content:center;flex-shrink:0;transition:all 0.15s;}
.ms-item.checked .cb{background:var(--accent);border-color:var(--accent);}
.ms-item.checked .cb::after{content:"";display:block;width:5px;height:8px;border:solid #fff;border-width:0 2px 2px 0;transform:rotate(45deg);margin-top:-2px;}
.ms-div{height:1px;background:var(--border);margin:4px 0;}
/* Upload */
.upload-btn{padding:8px 16px;border-radius:10px;cursor:pointer;font-size:13px;font-weight:600;display:inline-block;background:var(--accent);border:none;color:#fff;transition:all 0.2s;}
.upload-btn:hover{background:var(--accentL);}
.upload-btn input{display:none;}
/* Main */
.main{padding:24px 28px;max-width:1500px;margin:0 auto;}
.dropzone{border:2px dashed var(--border);border-radius:14px;padding:28px;text-align:center;margin-bottom:24px;transition:all 0.2s;}
.dropzone.over{border-color:var(--accent);background:rgba(196,30,58,0.05);}
/* Tabs */
.tabs{display:flex;gap:4px;margin-bottom:22px;background:var(--card);border-radius:12px;padding:4px;border:1px solid var(--border);width:fit-content;}
.tab{padding:8px 20px;border-radius:9px;border:none;background:transparent;color:var(--dim);font-size:13px;font-weight:600;cursor:pointer;transition:all 0.15s;font-family:'Plus Jakarta Sans',sans-serif;}
.tab:hover{color:var(--muted);}
.tab.active{background:var(--accent);color:#fff;box-shadow:0 2px 8px rgba(196,30,58,0.25);}
/* KPI cards */
.kpis{display:grid;grid-template-columns:repeat(auto-fit,minmax(250px,1fr));gap:16px;margin-bottom:22px;}
.kpi{background:var(--card);border:1px solid var(--border);border-radius:14px;padding:22px 26px;position:relative;overflow:hidden;}
.kpi-top{display:flex;justify-content:space-between;align-items:center;margin-bottom:14px;}
.kpi-title{font-size:13px;color:var(--muted);font-weight:600;}
.kpi-badge{font-size:11px;font-weight:700;padding:3px 10px;border-radius:20px;}
.kpi-row{display:flex;align-items:baseline;gap:20px;}
.kpi-main{font-size:32px;font-weight:800;letter-spacing:-1px;line-height:1;}
.kpi-unit{font-size:14px;font-weight:600;color:var(--dim);margin-left:4px;}
.kpi-sub{font-size:12px;color:var(--dim);margin-top:6px;font-weight:500;}
/* Grid */
.grid{display:grid;gap:16px;}.grid-2{grid-template-columns:1fr 1fr;}
.card{background:var(--card);border:1px solid var(--border);border-radius:14px;padding:22px;}
.card-full{grid-column:1 / -1;}
.card h3{font-size:12px;color:var(--muted);letter-spacing:1px;margin-bottom:14px;text-transform:uppercase;font-weight:700;}
.chart-wrap{position:relative;width:100%;height:280px;}
.chart-wrap.tall{height:360px;}
/* Table */
table{width:100%;border-collapse:collapse;font-size:13px;}
th{padding:10px 12px;text-align:left;color:var(--dim);font-size:11px;text-transform:uppercase;letter-spacing:0.8px;border-bottom:1px solid var(--border);font-weight:700;}
td{padding:11px 12px;font-weight:500;}tr+tr{border-top:1px solid rgba(30,42,54,0.5);}
/* Comments */
.cmt{background:var(--bg);border-left:3px solid var(--accent);border-radius:0 10px 10px 0;padding:12px 16px;margin-bottom:8px;}
.cmt-date{font-size:11px;color:var(--accent);margin-bottom:3px;font-weight:700;}
.cmt-text{font-size:13px;color:var(--muted);line-height:1.6;font-weight:500;}
/* Month manager */
.months-row{display:flex;gap:6px;flex-wrap:wrap;margin-bottom:16px;align-items:center;}
.m-chip{display:inline-flex;align-items:center;gap:4px;padding:5px 12px;border-radius:8px;background:var(--bg);border:1px solid var(--border);font-size:12px;font-weight:600;color:var(--muted);}
.m-chip .del{cursor:pointer;color:var(--dim);font-size:14px;margin-left:2px;line-height:1;}
.m-chip .del:hover{color:var(--red);}
.m-label{font-size:11px;color:var(--dim);font-weight:700;text-transform:uppercase;letter-spacing:0.8px;}
/* Misc */
.stat-row{display:flex;justify-content:space-between;align-items:center;padding:11px 16px;border-radius:10px;background:var(--bg);border:1px solid var(--border);margin-bottom:8px;}
.footer{text-align:center;margin-top:28px;padding-bottom:24px;font-size:11px;color:var(--dim);font-weight:500;}
.error{color:var(--red);margin-top:8px;font-size:13px;}
.file-tag{background:rgba(196,30,58,0.1);border:1px solid rgba(196,30,58,0.2);color:var(--accentL);padding:3px 10px;border-radius:6px;font-size:11px;font-weight:600;display:inline-block;margin:2px;}
@media(max-width:900px){.grid-2{grid-template-columns:1fr;}.header{flex-direction:column;align-items:flex-start;}}
::-webkit-scrollbar{width:7px;height:7px;}::-webkit-scrollbar-track{background:var(--bg);}::-webkit-scrollbar-thumb{background:#2a3a4a;border-radius:4px;}
</style>
</head>
<body>
<!-- HEADER -->
<div class="header">
  <div class="header-left">
    <div class="logo-dot">MY</div>
    <div class="brand">Mister York<span>District Dashboard</span></div>
  </div>
  <div class="header-right">
    <div class="date-range">
      <label>Fr&aring;n</label><input type="date" id="dateFrom" onchange="render()">
      <span class="date-sep">&mdash;</span>
      <label>Till</label><input type="date" id="dateTo" onchange="render()">
    </div>
    <div class="multiselect" id="multiselect">
      <button class="ms-btn" onclick="togglePanel()">Alla restauranger</button>
      <div class="ms-panel" id="msPanel"></div>
    </div>
    <label class="upload-btn">+ Ladda upp<input type="file" accept=".csv,.tsv,.txt" multiple onchange="loadFiles(this.files);this.value='';"></label>
  </div>
</div>
<!-- MAIN -->
<div class="main">
  <div id="dropzone" class="dropzone">
    <div id="dzText" style="font-size:14px;color:var(--muted);font-weight:600">&#128194; Dra och sl&auml;pp dina Loggbok-CSV:er h&auml;r</div>
    <div style="font-size:11px;color:var(--dim);margin-top:6px">Ladda upp en eller flera m&aring;nader &mdash; v&auml;lj sedan datumperiod fritt</div>
    <div id="uploadError" class="error"></div>
    <div id="fileList" style="margin-top:8px;text-align:center"></div>
  </div>
  <div id="monthsRow" class="months-row" style="display:none"></div>
  <div id="tabs" class="tabs" style="display:none"></div>
  <div id="kpis" class="kpis"></div>
  <div id="content"></div>
  <div class="footer" id="footer"></div>
</div>
<script>
var CC=["#C41E3A","#22C55E","#3B82F6","#F59E0B","#A855F7","#EC4899","#14B8A6"];
var VIEWS=["overview","compare","trends","forecast","tickettime","comments"];
var VIEW_LABELS=["&Ouml;versikt","J&auml;mf&ouml;r","Trender","Prognos vs Utfall","Ticket Time","Kommentarer"];
var MONTH_NAMES=["","Jan","Feb","Mar","Apr","Maj","Jun","Jul","Aug","Sep","Okt","Nov","Dec"];

var currentView="overview";
var charts={};
// Unified data: ALL_DAILY[]=full date entries, ALL_REST={name:{dateStr:{sales,labor,...}}}
var ALL_DAILY=[];
var ALL_REST={};
var ALL_RESTAURANTS=[];
var LOADED_MONTHS={};// key->true, tracks which months are loaded
var selectedRests=[];

var STORAGE_KEY="misterYorkDash_v2";
function saveData(){try{localStorage.setItem(STORAGE_KEY,JSON.stringify({daily:ALL_DAILY,rest:ALL_REST,restaurants:ALL_RESTAURANTS,months:LOADED_MONTHS}));}catch(e){}}
function loadData(){try{var r=localStorage.getItem(STORAGE_KEY);if(!r)return false;var s=JSON.parse(r);if(s&&s.daily&&s.daily.length){ALL_DAILY=s.daily;ALL_REST=s.rest||{};ALL_RESTAURANTS=s.restaurants||[];LOADED_MONTHS=s.months||{};return true;}}catch(e){}return false;}
function clearData(){try{localStorage.removeItem(STORAGE_KEY);}catch(e){}}

// Multi-select
function togglePanel(){document.getElementById("msPanel").classList.toggle("open");}
document.addEventListener("click",function(e){if(!document.getElementById("multiselect").contains(e.target))document.getElementById("msPanel").classList.remove("open");});
function buildPanel(){
  var panel=document.getElementById("msPanel");
  var allC=selectedRests.length===ALL_RESTAURANTS.length;
  var h='<div class="ms-item'+(allC?" checked":"")+'" onclick="togAll()"><div class="cb"></div>Alla restauranger</div><div class="ms-div"></div>';
  ALL_RESTAURANTS.forEach(function(r){h+='<div class="ms-item'+(selectedRests.indexOf(r)>=0?" checked":"")+'" onclick="togR(\''+r.replace(/'/g,"\\'")+'\')"><div class="cb"></div>'+r+'</div>';});
  panel.innerHTML=h;
  var btn=document.querySelector(".ms-btn");
  if(allC||selectedRests.length===0)btn.textContent="Alla restauranger";
  else if(selectedRests.length===1)btn.textContent=selectedRests[0];
  else btn.textContent=selectedRests.length+" restauranger";
}
function togAll(){if(selectedRests.length===ALL_RESTAURANTS.length)selectedRests=[];else selectedRests=ALL_RESTAURANTS.slice();buildPanel();render();}
function togR(n){var i=selectedRests.indexOf(n);if(i>=0)selectedRests.splice(i,1);else selectedRests.push(n);if(!selectedRests.length)selectedRests=ALL_RESTAURANTS.slice();buildPanel();render();}
function getSel(){return(!selectedRests.length||selectedRests.length===ALL_RESTAURANTS.length)?ALL_RESTAURANTS.slice():selectedRests;}
function isAll(){return getSel().length===ALL_RESTAURANTS.length;}

// Helpers
var fmt=function(n){return n!=null?Math.round(n).toLocaleString("sv-SE"):"\u2014";};
var fmtK=function(n){return n>=1000000?(n/1000000).toFixed(2)+" mkr":n>=1000?Math.round(n/1000)+"k":fmt(n);};
var fmtPct=function(n){return n!=null?n.toFixed(1)+"%":"\u2014";};
function fmtMM(secs){var m=Math.floor(secs/60),s=Math.round(secs%60);return m+":"+String(s).padStart(2,"0");}

function parseSEK(s){
  if(!s||s==="-"||s==="#DIV/0!"||s==="#REF!")return 0;
  s=String(s).replace(/"/g,"").replace(/kr/g,"").replace(/%/g,"").replace(/\u00a0/g," ").replace(/\u2212/g,"-").trim();
  s=s.replace(/\s/g,"");
  var m=s.match(/^(-?\d+),(\d{1,2})$/);
  if(m)s=m[1]+"."+m[2];else s=s.replace(/,/g,"");
  return parseFloat(s)||0;
}
function parseCSVRow(l){var r=[];var c="";var q=false;for(var i=0;i<l.length;i++){var ch=l[i];if(ch==='"')q=!q;else if(ch===","&&!q){r.push(c);c="";}else c+=ch;}r.push(c);return r;}

var SV_MONTHS={"jan":1,"januari":1,"feb":2,"februari":2,"mar":3,"mars":3,"apr":4,"april":4,"maj":5,"jun":6,"juni":6,"jul":7,"juli":7,"aug":8,"augusti":8,"sep":9,"september":9,"okt":10,"oktober":10,"nov":11,"november":11,"dec":12,"december":12};
function detectMonth(fn){if(!fn)return null;var f=fn.toUpperCase();var fm=null,fy=null;var mm={"JAN":1,"JANUARI":1,"FEB":2,"FEBRUARI":2,"MAR":3,"MARS":3,"APR":4,"APRIL":4,"MAJ":5,"JUN":6,"JUNI":6,"JUL":7,"JULI":7,"AUG":8,"AUGUSTI":8,"SEP":9,"SEPTEMBER":9,"OKT":10,"OKTOBER":10,"NOV":11,"NOVEMBER":11,"DEC":12,"DECEMBER":12};for(var n in mm){if(f.indexOf(n)>=0){fm=mm[n];break;}}var ym=f.match(/(20\d{2})/);if(ym)fy=parseInt(ym[1]);return(fm&&fy)?{m:fm,y:fy}:null;}
function detectRest(fn){if(!fn)return null;var f=fn.replace(/\.[^.]+$/,"").replace(/_/g," ").replace(/-/g," ");var skip=["loggbok","stockholm","mars","februari","januari","april","maj","juni","juli","augusti","september","oktober","november","december","jan","feb","mar","apr","jun","jul","aug","sep","okt","nov","dec","2024","2025","2026","2027","csv","version"];var w=f.split(/\s+/).filter(function(x){return x.length>1&&skip.indexOf(x.toLowerCase())<0&&!/^\d+$/.test(x);});return w.length?w.join(" "):null;}

function pad(n){return n<10?"0"+n:""+n;}
function toISO(y,m,d){return y+"-"+pad(m)+"-"+pad(d);}

function parseLoggbok(text,filename){
  var lines=text.replace(/\r/g,"").split("\n");
  var hIdx=-1;
  for(var i=0;i<Math.min(lines.length,25);i++){if(lines[i].indexOf("DATUM")>=0){hIdx=i;break;}}
  if(hIdx<0)return null;
  var fnM=detectMonth(filename);

  function parseDate(s){
    if(!s)return null;s=s.trim();
    if(s.indexOf("/")>=0){var p=s.split("/");if(p.length>=3){var mo=parseInt(p[0]),dy=parseInt(p[1]),yr=parseInt(p[2]);if(!isNaN(mo)&&!isNaN(dy)&&!isNaN(yr))return{d:dy,m:mo,y:yr};}}
    var mt=s.match(/^(\d{1,2})\s+([a-z\u00e5\u00e4\u00f6]+)/i);
    if(mt){var dy2=parseInt(mt[1]),mn=mt[2].toLowerCase();if(SV_MONTHS[mn])return{d:dy2,m:SV_MONTHS[mn],y:fnM?fnM.y:2026};}
    var nm=parseInt(s);if(!isNaN(nm)&&nm>=1&&nm<=31&&fnM)return{d:nm,m:fnM.m,y:fnM.y};
    return null;
  }

  // Detect layout
  var layout="district";
  for(var i=hIdx+1;i<lines.length;i++){
    var tr=parseCSVRow(lines[i]);var td=(tr[2]||"").trim();
    if(!parseDate(td))continue;
    if(parseSEK(tr[12])===0&&parseSEK(tr[13])>0)layout="single";
    break;
  }
  var C;
  if(layout==="single")C={fc:5,fcH:7,fcC:8,fcP:9,fcW:10,s:13,ab:15,h:16,lc:17,lp:18,w:20,wp:21,tt:37,cs:45};
  else C={fc:5,fcH:6,fcC:7,fcP:8,fcW:9,s:12,ab:13,h:14,lc:15,lp:16,w:17,wp:18,tt:33,cs:37};

  // Use filename month override
  var useMo=fnM?fnM.m:null,useYr=fnM?fnM.y:null;

  var daily=[];var comments=[];var monthKey="";
  for(var i=hIdx+1;i<lines.length;i++){
    var row=parseCSVRow(lines[i]);
    var pd=parseDate((row[2]||"").trim());
    if(!pd)continue;
    var sales=parseSEK(row[C.s]),fcS=parseSEK(row[C.fc]);
    if(sales===0&&fcS===0)continue;
    var mo=useMo||pd.m,yr=useYr||pd.y;
    if(!monthKey)monthKey=mo+"/"+yr;
    var iso=toISO(yr,mo,pd.d);

    var cmt="";
    for(var ci=C.cs;ci<row.length;ci++){var cv=(row[ci]||"").trim();if(cv.length>3&&cv.indexOf("#DIV")<0&&cv.indexOf("#REF")<0&&!/^\d+[\d.,%-]*$/.test(cv)&&cv.indexOf("%")<0){cmt=cv;break;}}

    daily.push({
      date:iso,day:pd.d,month:mo,year:yr,weekday:(row[3]||"").trim(),
      fcSales:fcS,fcHours:parseSEK(row[C.fcH]),fcCost:parseSEK(row[C.fcC]),fcPct:parseSEK(row[C.fcP]),fcWaste:parseSEK(row[C.fcW]),
      sales:sales,absence:parseSEK(row[C.ab]),hours:parseSEK(row[C.h]),laborCost:parseSEK(row[C.lc]),laborPct:parseSEK(row[C.lp]),
      waste:parseSEK(row[C.w]),wastePct:parseSEK(row[C.wp]),tt:parseSEK(row[C.tt]),comment:cmt
    });
    if(cmt)comments.push({date:iso,day:pd.d,weekday:(row[3]||"").trim(),text:cmt});
  }
  if(!daily.length)return null;

  // Per-restaurant grids
  var restaurants=[];var perRest={};
  var sections=["Sales","Actual labor cost","Waste","Productive hours used","Absence hours","Ticket Time (sec)"];
  var keys=["sales","laborCost","waste","hours","absence","tt"];
  for(var si=0;si<sections.length;si++){
    var sN=sections[si],key=keys[si],sIdx=-1;
    for(var i=0;i<lines.length;i++){var r=parseCSVRow(lines[i]);if(r[28]&&r[28].trim().toLowerCase()===sN.toLowerCase()){sIdx=i;break;}if(r[28]&&r[28].trim().toLowerCase().indexOf(sN.toLowerCase().substring(0,6))===0){sIdx=i;break;}}
    if(sIdx<0)continue;
    var fc=0;
    for(var i=sIdx+1;i<Math.min(sIdx+20,lines.length);i++){
      var r=parseCSVRow(lines[i]);var nm=(r[28]||"").trim();
      if(!nm||nm===""){if(fc>0)break;continue;}
      if(nm.indexOf("Total")===0)break;
      if(["Sales","Actual labor cost","Waste","Productive hours used","Productive hours","Absence hours","Internal processes","Grant support","Optiqo Cleaning","Google review","Ticket Time (sec)","Total ticket time"].indexOf(nm)>=0)break;
      fc++;
      if(restaurants.indexOf(nm)<0)restaurants.push(nm);
      if(!perRest[nm])perRest[nm]={};
      for(var d=1;d<=31;d++){
        var val=parseSEK(r[28+d]);
        if(val){var mo2=useMo||daily[0].month,yr2=useYr||daily[0].year;var iso2=toISO(yr2,mo2,d);
          if(!perRest[nm][iso2])perRest[nm][iso2]={};
          perRest[nm][iso2][key]=val;
        }
      }
    }
  }

  // Fallback single restaurant
  if(restaurants.length===0){
    var rn=detectRest(filename)||"Min restaurang";
    restaurants.push(rn);perRest[rn]={};
    daily.forEach(function(d){
      if(!perRest[rn][d.date])perRest[rn][d.date]={};
      if(d.sales)perRest[rn][d.date].sales=d.sales;
      if(d.laborCost)perRest[rn][d.date].laborCost=d.laborCost;
      if(d.waste)perRest[rn][d.date].waste=d.waste;
      if(d.hours)perRest[rn][d.date].hours=d.hours;
      if(d.absence)perRest[rn][d.date].absence=d.absence;
      if(d.tt)perRest[rn][d.date].tt=d.tt;
    });
  }
  return{daily:daily,restaurants:restaurants,perRest:perRest,monthKey:monthKey,comments:comments};
}

// Merge parsed data into global store
function mergeData(parsed){
  // Remove existing data for this month
  var dates=parsed.daily.map(function(d){return d.date;});
  ALL_DAILY=ALL_DAILY.filter(function(d){return dates.indexOf(d.date)<0;});
  ALL_DAILY=ALL_DAILY.concat(parsed.daily);
  ALL_DAILY.sort(function(a,b){return a.date<b.date?-1:a.date>b.date?1:0;});

  parsed.restaurants.forEach(function(r){
    if(ALL_RESTAURANTS.indexOf(r)<0)ALL_RESTAURANTS.push(r);
    if(!ALL_REST[r])ALL_REST[r]={};
    for(var dt in parsed.perRest[r]){ALL_REST[r][dt]=parsed.perRest[r][dt];}
  });
  LOADED_MONTHS[parsed.monthKey]=true;
}

function deleteMonth(key){
  var parts=key.split("/");var mo=parseInt(parts[0]),yr=parseInt(parts[1]);
  ALL_DAILY=ALL_DAILY.filter(function(d){return!(d.month===mo&&d.year===yr);});
  ALL_RESTAURANTS.forEach(function(r){
    if(!ALL_REST[r])return;
    for(var dt in ALL_REST[r]){var p=dt.split("-");if(parseInt(p[0])===yr&&parseInt(p[1])===mo)delete ALL_REST[r][dt];}
  });
  delete LOADED_MONTHS[key];
  if(!ALL_DAILY.length){ALL_RESTAURANTS=[];ALL_REST={};clearData();}else saveData();
  setDateRange();buildPanel();render();renderMonths();updateDZ();
}

function loadFiles(files){
  var el=document.getElementById("uploadError");if(el)el.textContent="";
  var tot=files.length,done=0,added=0;
  Array.from(files).forEach(function(f){
    var fn=f.name;var reader=new FileReader();
    reader.onload=function(e){
      try{var p=parseLoggbok(e.target.result,fn);if(p&&p.daily.length){mergeData(p);added++;}}catch(ex){}
      done++;
      if(done===tot){
        if(!added){if(el)el.textContent="Kunde inte l\u00e4sa filen.";return;}
        selectedRests=ALL_RESTAURANTS.slice();
        saveData();setDateRange();buildPanel();renderMonths();render();updateDZ();
      }
    };reader.readAsText(f);
  });
}

function setDateRange(){
  if(!ALL_DAILY.length)return;
  var df=document.getElementById("dateFrom"),dt=document.getElementById("dateTo");
  if(!df.value)df.value=ALL_DAILY[0].date;
  if(!dt.value)dt.value=ALL_DAILY[ALL_DAILY.length-1].date;
  // Expand range if new data is outside current range
  if(ALL_DAILY[0].date<df.value)df.value=ALL_DAILY[0].date;
  if(ALL_DAILY[ALL_DAILY.length-1].date>dt.value)dt.value=ALL_DAILY[ALL_DAILY.length-1].date;
}

function getRange(){
  var f=document.getElementById("dateFrom").value,t=document.getElementById("dateTo").value;
  return ALL_DAILY.filter(function(d){return d.date>=f&&d.date<=t;});
}
function getRangeDates(){return getRange().map(function(d){return d.date;});}

function updateDZ(){
  var dz=document.getElementById("dzText"),fl=document.getElementById("fileList");
  var keys=Object.keys(LOADED_MONTHS);
  if(dz){dz.textContent=keys.length?"\uD83D\uDCC2 "+keys.length+" m\u00e5nad(er) \u2014 dra fler h\u00e4r":"\uD83D\uDCC2 Dra och sl\u00e4pp dina Loggbok-CSV:er h\u00e4r";}
  if(fl){fl.innerHTML=keys.sort().map(function(k){var p=k.split("/");return'<span class="file-tag">'+MONTH_NAMES[parseInt(p[0])]+" "+p[1]+'</span>';}).join("");}
}
function renderMonths(){
  var keys=Object.keys(LOADED_MONTHS).sort(function(a,b){var pa=a.split("/"),pb=b.split("/");return(parseInt(pa[1])*100+parseInt(pa[0]))-(parseInt(pb[1])*100+parseInt(pb[0]));});
  var el=document.getElementById("monthsRow");
  if(!keys.length){el.style.display="none";return;}
  el.style.display="flex";
  var h='<span class="m-label">Laddade:</span>';
  keys.forEach(function(k){var p=k.split("/");h+='<span class="m-chip">'+MONTH_NAMES[parseInt(p[0])]+" "+p[1]+' <span class="del" onclick="deleteMonth(\''+k+'\')">&times;</span></span>';});
  el.innerHTML=h;
}

// Dropzone
(function(){var dz=document.getElementById("dropzone");dz.addEventListener("dragover",function(e){e.preventDefault();dz.classList.add("over");});dz.addEventListener("dragleave",function(e){e.preventDefault();dz.classList.remove("over");});dz.addEventListener("drop",function(e){e.preventDefault();dz.classList.remove("over");if(e.dataTransfer&&e.dataTransfer.files.length)loadFiles(e.dataTransfer.files);});})();

function destroyCharts(){for(var k in charts){try{charts[k].destroy();}catch(e){}}charts={};}
function makeChart(id,cfg){var c=document.getElementById(id);if(!c)return;charts[id]=new Chart(c.getContext("2d"),cfg);}
var BO={responsive:true,maintainAspectRatio:false,plugins:{legend:{labels:{color:"#8899a6",font:{size:11,family:"'Plus Jakarta Sans'"}}},tooltip:{backgroundColor:"#151c25",borderColor:"#C41E3A",borderWidth:1,titleColor:"#e8ecf0",bodyColor:"#8899a6",bodyFont:{family:"'Plus Jakarta Sans'",size:12},cornerRadius:8,padding:12}},scales:{x:{ticks:{color:"#556677",font:{size:10},maxRotation:0},grid:{color:"rgba(30,42,54,0.5)"}},y:{ticks:{color:"#556677",font:{size:10}},grid:{color:"rgba(30,42,54,0.3)"}}}};

function shortDate(iso){var p=iso.split("-");return parseInt(p[2])+"/"+parseInt(p[1]);}

// ========== RENDER ==========
function render(){
  destroyCharts();
  var data=getRange();
  var tabsEl=document.getElementById("tabs");
  if(!ALL_DAILY.length){
    document.getElementById("kpis").innerHTML="";tabsEl.style.display="none";
    document.getElementById("content").innerHTML='<div style="text-align:center;padding:50px 0;color:var(--dim);font-size:15px;font-weight:600">Ladda upp Loggbok-filer f\u00f6r att komma ig\u00e5ng</div>';
    document.getElementById("footer").textContent="";return;
  }
  tabsEl.style.display="flex";
  tabsEl.innerHTML=VIEWS.map(function(v,i){return'<button class="tab'+(currentView===v?" active":"")+'" onclick="currentView=\''+v+'\';render()">'+VIEW_LABELS[i]+'</button>';}).join("");

  // Aggregate
  var sel=getSel();var tS=0,tL=0,tW=0,tH=0,ttS=0,ttC=0,tFcS=0;
  data.forEach(function(d){tS+=d.sales;tL+=d.laborCost;tW+=d.waste;tH+=d.hours;tFcS+=d.fcSales;if(d.tt>0){ttS+=d.tt;ttC++;}});
  if(!isAll()){
    tS=0;tL=0;tW=0;tH=0;ttS=0;ttC=0;
    var dates=data.map(function(d){return d.date;});
    sel.forEach(function(r){if(!ALL_REST[r])return;dates.forEach(function(dt){var rd=ALL_REST[r][dt];if(!rd)return;tS+=(rd.sales||0);tL+=(rd.laborCost||0);tW+=(rd.waste||0);tH+=(rd.hours||0);if(rd.tt>0){ttS+=rd.tt;ttC++;}});});
  }
  var lp=tS?tL/tS*100:0,wp=tS?tW/tS*100:0,sph=tH?tS/tH:0,avgTT=ttC?ttS/ttC:0;
  var diffS=tS-tFcS;

  // KPIs
  document.getElementById("kpis").innerHTML=
    '<div class="kpi"><div class="kpi-top"><span class="kpi-title">F\u00f6rs\u00e4ljning</span></div><div class="kpi-row"><span class="kpi-main" style="color:var(--text)">'+(tS/1000000).toFixed(2)+'<span class="kpi-unit">mkr</span></span></div><div class="kpi-sub">'+data.length+' dagar | '+fmt(data.length?Math.round(tS/data.length):0)+' kr/dag</div></div>'+
    '<div class="kpi"><div class="kpi-top"><span class="kpi-title">Labor</span><span class="kpi-badge" style="background:'+(lp>30?"rgba(239,68,68,0.15);color:var(--red)":"rgba(34,197,94,0.15);color:var(--green)")+'">'+fmtPct(lp)+'</span></div><div class="kpi-row"><span class="kpi-main" style="color:var(--text)">'+fmt(tL)+'<span class="kpi-unit">kr</span></span></div><div class="kpi-sub">'+fmt(tH)+' timmar | '+fmt(sph)+' kr/timme</div></div>'+
    '<div class="kpi"><div class="kpi-top"><span class="kpi-title">Waste</span><span class="kpi-badge" style="background:'+(wp>0.7?"rgba(239,68,68,0.15);color:var(--red)":"rgba(34,197,94,0.15);color:var(--green)")+'">'+fmtPct(wp)+'</span></div><div class="kpi-row"><span class="kpi-main" style="color:var(--text)">'+fmt(tW)+'<span class="kpi-unit">kr</span></span></div><div class="kpi-sub">'+(tS?fmtPct(wp):"0%")+' av f\u00f6rs\u00e4ljning</div></div>'+
    '<div class="kpi"><div class="kpi-top"><span class="kpi-title">Ticket Time</span><span class="kpi-badge" style="background:'+(avgTT>360?"rgba(239,68,68,0.15);color:var(--red)":"rgba(34,197,94,0.15);color:var(--green)")+'">'+fmtMM(avgTT)+'</span></div><div class="kpi-row"><span class="kpi-main" style="color:var(--text)">'+fmt(Math.round(avgTT))+'<span class="kpi-unit">sek</span></span></div><div class="kpi-sub">Snitt \u00f6ver '+ttC+' dagar</div></div>'+
    '<div class="kpi"><div class="kpi-top"><span class="kpi-title">Prognos vs Utfall</span><span class="kpi-badge" style="background:'+(diffS>=0?"rgba(34,197,94,0.15);color:var(--green)":"rgba(239,68,68,0.15);color:var(--red)")+'">'+(diffS>=0?"+":"")+fmt(diffS)+' kr</span></div><div class="kpi-row"><span class="kpi-main" style="color:var(--text)">'+(tFcS/1000000).toFixed(2)+'<span class="kpi-unit">mkr prognos</span></span></div><div class="kpi-sub">Utfall: '+(tS/1000000).toFixed(2)+' mkr</div></div>';

  var el=document.getElementById("content");
  if(currentView==="overview")vOverview(el,data);
  else if(currentView==="compare")vCompare(el,data);
  else if(currentView==="trends")vTrends(el,data);
  else if(currentView==="forecast")vForecast(el,data);
  else if(currentView==="tickettime")vTicket(el,data);
  else if(currentView==="comments")vComments(el,data);

  var nDays=data.length;var nMo=Object.keys(LOADED_MONTHS).length;
  document.getElementById("footer").textContent="Mister York \u00B7 "+nDays+" dagar \u00B7 "+ALL_RESTAURANTS.length+" restauranger \u00B7 "+nMo+" m\u00e5nad(er)";
}

function vOverview(el,data){
  var labels=data.map(function(d){return shortDate(d.date);});
  var sel=getSel();
  var salesArr=isAll()?data.map(function(d){return d.sales;}):data.map(function(d){var s=0;sel.forEach(function(r){var rd=ALL_REST[r]&&ALL_REST[r][d.date];if(rd)s+=(rd.sales||0);});return s;});
  var laborArr=isAll()?data.map(function(d){return d.laborCost;}):data.map(function(d){var s=0;sel.forEach(function(r){var rd=ALL_REST[r]&&ALL_REST[r][d.date];if(rd)s+=(rd.laborCost||0);});return s;});
  var lpArr=data.map(function(_,i){return salesArr[i]?(laborArr[i]/salesArr[i]*100):0;});

  el.innerHTML='<div class="grid grid-2"><div class="card"><h3>Daglig f\u00f6rs\u00e4ljning</h3><div class="chart-wrap"><canvas id="o1"></canvas></div></div><div class="card"><h3>Labor % per dag</h3><div class="chart-wrap"><canvas id="o2"></canvas></div></div><div class="card card-full"><h3>F\u00f6rs\u00e4ljning per restaurang</h3><div class="chart-wrap tall"><canvas id="o3"></canvas></div></div></div>';

  makeChart("o1",{type:"line",data:{labels:labels,datasets:[{label:"F\u00f6rs\u00e4ljning",data:salesArr,borderColor:"#14B8A6",backgroundColor:"rgba(20,184,166,0.1)",fill:true,tension:0.35,pointRadius:0,borderWidth:2}]},options:JSON.parse(JSON.stringify(BO))});
  makeChart("o2",{type:"line",data:{labels:labels,datasets:[{label:"Labor %",data:lpArr,borderColor:"#A855F7",backgroundColor:"rgba(168,85,247,0.1)",fill:true,tension:0.35,pointRadius:0,borderWidth:2}]},options:JSON.parse(JSON.stringify(BO))});

  var ds=sel.map(function(r){var i=ALL_RESTAURANTS.indexOf(r);return{label:r,data:data.map(function(d){var rd=ALL_REST[r]&&ALL_REST[r][d.date];return rd?(rd.sales||0):0;}),borderColor:CC[i%CC.length],tension:0.35,pointRadius:0,borderWidth:2};});
  makeChart("o3",{type:"line",data:{labels:labels,datasets:ds},options:JSON.parse(JSON.stringify(BO))});
}

function vCompare(el,data){
  var sel=getSel();var dates=data.map(function(d){return d.date;});
  var comp=sel.map(function(r){var i=ALL_RESTAURANTS.indexOf(r);var s=0,l=0,w=0,h=0;dates.forEach(function(dt){var rd=ALL_REST[r]&&ALL_REST[r][dt];if(!rd)return;s+=(rd.sales||0);l+=(rd.laborCost||0);w+=(rd.waste||0);h+=(rd.hours||0);});
    return{name:r,idx:i,sales:s,labor:l,waste:w,hours:h,lp:s?+(l/s*100).toFixed(1):0,wp:s?+(w/s*100).toFixed(1):0,sph:h?Math.round(s/h):0};});

  var rows=comp.map(function(r){return'<tr><td style="font-weight:700;color:'+CC[r.idx%CC.length]+'">'+r.name+'</td><td>'+fmt(r.sales)+' kr</td><td style="color:'+(r.lp>30?"var(--red)":"var(--green)")+'">'+fmtPct(r.lp)+'</td><td style="color:'+(r.wp>0.7?"var(--red)":"var(--green)")+'">'+fmtPct(r.wp)+'</td><td>'+fmt(r.sph)+' kr</td><td>'+fmt(r.hours)+'</td></tr>';}).join("");

  el.innerHTML='<div class="grid"><div class="grid grid-2"><div class="card"><h3>F\u00f6rs\u00e4ljning</h3><div class="chart-wrap"><canvas id="c1"></canvas></div></div><div class="card"><h3>Labor %</h3><div class="chart-wrap"><canvas id="c2"></canvas></div></div></div><div class="card" style="overflow-x:auto"><h3>Detaljerad j\u00e4mf\u00f6relse</h3><table><thead><tr><th>Restaurang</th><th>F\u00f6rs\u00e4ljning</th><th>Labor %</th><th>Waste %</th><th>Fsg/timme</th><th>Timmar</th></tr></thead><tbody>'+rows+'</tbody></table></div></div>';

  makeChart("c1",{type:"bar",data:{labels:comp.map(function(r){return r.name;}),datasets:[{label:"F\u00f6rs\u00e4ljning",data:comp.map(function(r){return r.sales;}),backgroundColor:comp.map(function(r){return CC[r.idx%CC.length];}),borderRadius:6}]},options:JSON.parse(JSON.stringify(BO))});
  makeChart("c2",{type:"bar",data:{labels:comp.map(function(r){return r.name;}),datasets:[{label:"Labor %",data:comp.map(function(r){return r.lp;}),backgroundColor:comp.map(function(r){return CC[r.idx%CC.length];}),borderRadius:6}]},options:JSON.parse(JSON.stringify(BO))});
}

function vTrends(el,data){
  var labels=data.map(function(d){return shortDate(d.date);});
  var sel=getSel();
  function arr(key){return isAll()?data.map(function(d){return d[key]||0;}):data.map(function(d){var s=0;sel.forEach(function(r){var rd=ALL_REST[r]&&ALL_REST[r][d.date];if(rd)s+=(rd[key]||0);});return s;});}
  var sA=arr("sales"),lA=arr("laborCost"),wA=arr("waste"),hA=arr("hours");

  el.innerHTML='<div class="grid grid-2"><div class="card"><h3>F\u00f6rs\u00e4ljning</h3><div class="chart-wrap"><canvas id="t1"></canvas></div></div><div class="card"><h3>Labor cost</h3><div class="chart-wrap"><canvas id="t2"></canvas></div></div><div class="card"><h3>Waste</h3><div class="chart-wrap"><canvas id="t3"></canvas></div></div><div class="card"><h3>Produktiva timmar</h3><div class="chart-wrap"><canvas id="t4"></canvas></div></div></div>';

  makeChart("t1",{type:"line",data:{labels:labels,datasets:[{label:"F\u00f6rs\u00e4ljning",data:sA,borderColor:"#14B8A6",backgroundColor:"rgba(20,184,166,0.08)",fill:true,tension:0.35,pointRadius:0,borderWidth:2}]},options:JSON.parse(JSON.stringify(BO))});
  makeChart("t2",{type:"line",data:{labels:labels,datasets:[{label:"Labor",data:lA,borderColor:"#A855F7",backgroundColor:"rgba(168,85,247,0.08)",fill:true,tension:0.35,pointRadius:0,borderWidth:2}]},options:JSON.parse(JSON.stringify(BO))});
  makeChart("t3",{type:"bar",data:{labels:labels,datasets:[{label:"Waste",data:wA,backgroundColor:"#F59E0B",borderRadius:4}]},options:JSON.parse(JSON.stringify(BO))});
  makeChart("t4",{type:"line",data:{labels:labels,datasets:[{label:"Timmar",data:hA,borderColor:"#22C55E",backgroundColor:"rgba(34,197,94,0.08)",fill:true,tension:0.35,pointRadius:0,borderWidth:2}]},options:JSON.parse(JSON.stringify(BO))});
}

function vForecast(el,data){
  var labels=data.map(function(d){return shortDate(d.date);});
  var fc=data.map(function(d){return d.fcSales;}),act=data.map(function(d){return d.sales;});
  var fcL=data.map(function(d){return d.fcCost;}),actL=data.map(function(d){return d.laborCost;});
  var diff=data.map(function(d){return d.sales-d.fcSales;});
  var tF=0,tA=0,tFL=0,tAL=0;data.forEach(function(d){tF+=d.fcSales;tA+=d.sales;tFL+=d.fcCost;tAL+=d.laborCost;});
  var dT=tA-tF;

  var stats=[{l:"Prognos fsg",v:fmtK(tF),c:"var(--dim)"},{l:"Utfall fsg",v:fmtK(tA),c:"var(--teal)"},{l:"Diff fsg",v:(dT>=0?"+":"")+fmt(dT)+" kr",c:dT>=0?"var(--green)":"var(--red)"},{l:"Prognos labor",v:fmt(tFL)+" kr",c:"var(--dim)"},{l:"Utfall labor",v:fmt(tAL)+" kr",c:"var(--purple)"},{l:"Diff labor",v:(tAL-tFL>=0?"+":"")+fmt(tAL-tFL)+" kr",c:tAL>tFL?"var(--red)":"var(--green)"}].map(function(s){return'<div class="stat-row"><span style="font-size:13px;color:var(--muted)">'+s.l+'</span><span style="font-size:15px;font-weight:700;color:'+s.c+'">'+s.v+'</span></div>';}).join("");

  el.innerHTML='<div class="grid grid-2"><div class="card"><h3>Prognos vs Utfall &mdash; F\u00f6rs\u00e4ljning</h3><div class="chart-wrap"><canvas id="f1"></canvas></div></div><div class="card"><h3>Daglig diff</h3><div class="chart-wrap"><canvas id="f2"></canvas></div></div><div class="card"><h3>Prognos vs Utfall &mdash; Labor</h3><div class="chart-wrap"><canvas id="f3"></canvas></div></div><div class="card"><h3>Summering</h3><div style="margin-top:6px">'+stats+'</div></div></div>';

  makeChart("f1",{type:"line",data:{labels:labels,datasets:[{label:"Prognos",data:fc,borderColor:"#556677",borderDash:[6,4],tension:0.35,pointRadius:0},{label:"Utfall",data:act,borderColor:"#14B8A6",tension:0.35,pointRadius:0,borderWidth:2}]},options:JSON.parse(JSON.stringify(BO))});
  makeChart("f2",{type:"bar",data:{labels:labels,datasets:[{label:"Diff",data:diff,backgroundColor:diff.map(function(v){return v>=0?"#22C55E":"#EF4444";}),borderRadius:4}]},options:JSON.parse(JSON.stringify(BO))});
  makeChart("f3",{type:"line",data:{labels:labels,datasets:[{label:"Prognos",data:fcL,borderColor:"#556677",borderDash:[6,4],tension:0.35,pointRadius:0},{label:"Utfall",data:actL,borderColor:"#A855F7",tension:0.35,pointRadius:0,borderWidth:2}]},options:JSON.parse(JSON.stringify(BO))});
}

function vTicket(el,data){
  var labels=data.map(function(d){return shortDate(d.date);});
  var sel=getSel();
  var ttArr=isAll()?data.map(function(d){return d.tt||0;}):data.map(function(d){var s=0,c=0;sel.forEach(function(r){var rd=ALL_REST[r]&&ALL_REST[r][d.date];if(rd&&rd.tt>0){s+=rd.tt;c++;}});return c?s/c:0;});
  var dates=data.map(function(d){return d.date;});

  var avgs=sel.map(function(r){var i=ALL_RESTAURANTS.indexOf(r);var vals=[];dates.forEach(function(dt){var rd=ALL_REST[r]&&ALL_REST[r][dt];if(rd&&rd.tt>0)vals.push(rd.tt);});
    var avg=vals.length?vals.reduce(function(a,v){return a+v;},0)/vals.length:0;
    return{name:r,idx:i,avg:avg,min:vals.length?Math.min.apply(null,vals):0,max:vals.length?Math.max.apply(null,vals):0,days:vals.length};});

  var ds=sel.map(function(r){var i=ALL_RESTAURANTS.indexOf(r);return{label:r,data:dates.map(function(dt){var rd=ALL_REST[r]&&ALL_REST[r][dt];return rd&&rd.tt?rd.tt:0;}),borderColor:CC[i%CC.length],tension:0.35,pointRadius:0,borderWidth:2};});

  var rows=avgs.map(function(r){return'<tr><td style="font-weight:700;color:'+CC[r.idx%CC.length]+'">'+r.name+'</td><td style="font-weight:700">'+fmtMM(r.avg)+'</td><td>'+fmt(Math.round(r.avg))+' sek</td><td style="color:var(--green)">'+fmtMM(r.min)+'</td><td style="color:var(--red)">'+fmtMM(r.max)+'</td><td>'+r.days+'</td></tr>';}).join("");

  el.innerHTML='<div class="grid grid-2"><div class="card"><h3>Daglig ticket time</h3><div class="chart-wrap"><canvas id="tt1"></canvas></div></div><div class="card"><h3>Snitt per restaurang</h3><div class="chart-wrap"><canvas id="tt2"></canvas></div></div><div class="card card-full"><h3>Per restaurang &mdash; trend</h3><div class="chart-wrap tall"><canvas id="tt3"></canvas></div></div><div class="card card-full" style="overflow-x:auto"><h3>Ticket Time \u00d6versikt</h3><table><thead><tr><th>Restaurang</th><th>Snitt</th><th>Sek</th><th>B\u00e4st</th><th>S\u00e4mst</th><th>Dagar</th></tr></thead><tbody>'+rows+'</tbody></table></div></div>';

  makeChart("tt1",{type:"line",data:{labels:labels,datasets:[{label:"TT (sek)",data:ttArr,borderColor:"#14B8A6",backgroundColor:"rgba(20,184,166,0.08)",fill:true,tension:0.35,pointRadius:0,borderWidth:2}]},options:JSON.parse(JSON.stringify(BO))});
  makeChart("tt2",{type:"bar",data:{labels:avgs.map(function(r){return r.name;}),datasets:[{label:"Snitt",data:avgs.map(function(r){return Math.round(r.avg);}),backgroundColor:avgs.map(function(r){return CC[r.idx%CC.length];}),borderRadius:6}]},options:JSON.parse(JSON.stringify(BO))});
  makeChart("tt3",{type:"line",data:{labels:labels,datasets:ds},options:JSON.parse(JSON.stringify(BO))});
}

function vComments(el,data){
  var cmts=[];data.forEach(function(d){if(d.comment)cmts.push(d);});
  if(!cmts.length){el.innerHTML='<div class="card"><h3>Kommentarer</h3><p style="color:var(--dim)">Inga kommentarer i vald period.</p></div>';return;}
  var cards=cmts.map(function(d){
    var dp=d.fcSales?((d.sales-d.fcSales)/d.fcSales*100):0;
    var tag=dp>=0?'<span style="color:var(--green)">+'+dp.toFixed(1)+'%</span>':'<span style="color:var(--red)">'+dp.toFixed(1)+'%</span>';
    return'<div class="cmt"><div class="cmt-date">'+d.date+' ('+d.weekday+') \u2014 Fsg: '+fmt(d.sales)+' kr '+tag+' vs prognos \u2014 LC: '+fmtPct(d.laborPct)+'</div><div class="cmt-text">'+d.comment+'</div></div>';
  }).join("");
  el.innerHTML='<div class="card"><h3>Dagsrapporter &amp; kommentarer ('+cmts.length+')</h3>'+cards+'</div>';
}

Chart.defaults.font.family="'Plus Jakarta Sans','Segoe UI',sans-serif";
if(loadData()){selectedRests=ALL_RESTAURANTS.slice();setDateRange();buildPanel();renderMonths();render();updateDZ();}else{render();}
</script>
</body>
</html>
