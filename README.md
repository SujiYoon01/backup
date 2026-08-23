<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<title>우주사업부 근무실적 현황</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<style>
  :root{
    --c1:#35578C; --c2:#6C8FC4; --c3:#A9C3E6; --c4:#E3EDF9;
    --dark:#1E3358; --bg:#F7F9FC; --card:#FFFFFF; --border:#E4E7EC;
    --text:#101828; --sub:#667085; --mute:#98A2B3; --pos:#1F9E89; --neg:#E4572E; --gold:#D9A441;
    --avgred:#B5534B;
  }
  *{ box-sizing:border-box; margin:0; padding:0; }
  html,body{ background:var(--bg); }
  body{ font-family:-apple-system,BlinkMacSystemFont,'Apple SD Gothic Neo','Malgun Gothic',sans-serif; color:var(--text); padding:28px 32px 60px; max-width:1440px; margin:0 auto; }
  .header{ display:flex; justify-content:space-between; align-items:flex-start; padding-bottom:18px; margin-bottom:14px; border-bottom:2px solid var(--dark); flex-wrap:wrap; gap:16px; }
  .titlewrap{ display:flex; align-items:flex-start; }
  .titlewrap .tab{ width:6px; height:36px; background:var(--c1); border-radius:1px; margin-right:12px; }
  .titlewrap h1{ font-size:24px; font-weight:800; color:var(--dark); letter-spacing:-0.4px; }
  .titlewrap .sub{ font-size:12.5px; color:var(--sub); margin-top:5px; }
  .header-actions{ display:flex; flex-direction:column; align-items:flex-end; gap:8px; }
  .header-actions .btns{ display:flex; gap:8px; }
  .btn{ font-family:inherit; font-size:12px; font-weight:700; padding:8px 14px; border-radius:8px; cursor:pointer; border:none; white-space:nowrap; }
  .btn-primary{ background:var(--c1); color:#fff; }
  .btn-outline{ background:#fff; color:var(--dark); border:1px solid var(--border); }
  .data-bar input[type=file]{ display:none; }
  .meta{ text-align:right; font-size:11.5px; color:var(--mute); }
  .status{ font-size:11px; padding:3px 9px; border-radius:10px; font-weight:700; margin-left:8px; }
  .status.wait{ background:#F0F2F5; color:var(--mute); }
  .status.ok{ background:#E6F7F2; color:#16876F; }
  .upload-err{ background:#FDEDE8; color:#C6431E; border:1px solid #F6C9BA; border-radius:8px; padding:10px 14px; margin-bottom:14px; font-size:12px; font-weight:600; display:none; }

  .filter-bar{ display:flex; gap:10px; align-items:center; margin-bottom:12px; flex-wrap:wrap; }
  .filter-bar select{ font-family:inherit; font-size:12.5px; border:1px solid var(--border); border-radius:8px; padding:8px 12px; color:var(--text); background:#fff; min-width:150px; }

  .tab-nav{ display:flex; gap:8px; margin-bottom:16px; }
  .tab-nav button{ font-family:inherit; font-size:13px; font-weight:700; color:var(--c1); background:#EAF1FF; border:1px solid var(--c3); border-radius:20px; padding:8px 18px; cursor:pointer; }
  .tab-nav button.active{ background:var(--c1); color:#fff; border-color:var(--c1); }

  .kpi-row{ display:flex; gap:14px; margin-bottom:18px; flex-wrap:wrap; }
  .kpi{ flex:1; min-width:210px; background:var(--card); border:1px solid var(--border); border-radius:10px; padding:16px 20px; }
  .kpi .label{ font-size:11.5px; color:var(--sub); font-weight:600; }
  .kpi .value{ font-size:26px; font-weight:800; margin-top:6px; }
  .kpi .chip{ display:inline-block; font-size:11.5px; font-weight:700; margin-top:7px; padding:2px 8px; border-radius:11px; }
  .chip.up{ background:#E6F7F2; color:#16876F; }
  .chip.neutral{ background:#F0F2F5; color:var(--mute); }
  .chip.risk{ background:#FDEDE8; color:#C6431E; }

  .grid{ display:flex; flex-wrap:wrap; gap:14px; }
  .card{ background:var(--card); border:1px solid var(--border); border-radius:10px; padding:18px 20px 20px; box-shadow:0 1px 2px rgba(16,24,40,.04); display:flex; flex-direction:column; }
  .w-half{ width:calc(50% - 7px); }
  .w-full{ width:100%; }
  .card-body{ flex:1; display:flex; flex-direction:column; justify-content:center; }
  .card-title{ display:flex; align-items:center; margin-bottom:14px; }
  .card-title .tab{ width:4px; height:15px; border-radius:1px; margin-right:8px; background:var(--c1); }
  .card-title h2{ font-size:14.5px; font-weight:700; color:var(--dark); }
  .card-title .note{ margin-left:auto; font-size:10.5px; color:var(--mute); }
  .empty-state{ font-size:12.5px; color:var(--mute); text-align:center; padding:30px 10px; background:var(--bg); border-radius:8px; }

  .barrow{ display:flex; align-items:center; margin-bottom:10px; }
  .barrow .name{ width:140px; font-size:12px; color:#475467; flex-shrink:0; overflow:hidden; text-overflow:ellipsis; white-space:nowrap; }
  .barrow .track{ flex:1; background:#F0F2F5; border-radius:4px; height:16px; position:relative; margin-right:10px; }
  .barrow .fill{ position:absolute; left:0; top:0; height:100%; border-radius:4px; background:var(--c1); }
  .barrow .num{ width:64px; font-size:12px; font-weight:700; color:var(--dark); text-align:right; flex-shrink:0; }
  .track-mark{ position:absolute; top:-5px; bottom:-5px; width:0; border-left:3px dashed var(--avgred); z-index:2; }
  .track-mark-legend{ display:flex; align-items:center; gap:6px; font-size:11px; font-weight:700; color:var(--avgred); margin-bottom:10px; }
  .track-mark-swatch{ width:10px; height:3px; background:var(--avgred); display:inline-block; }

  .quad-note{ font-size:11px; color:var(--mute); margin-top:10px; line-height:1.6; }
  .legend-row{ display:flex; flex-wrap:wrap; gap:12px; margin-top:10px; font-size:11px; color:#475467; }
  .legend-row i{ display:inline-block; width:10px; height:10px; border-radius:2px; margin-right:5px; vertical-align:-1px; }

  table.tbl{ width:100%; border-collapse:collapse; font-size:12px; }
  table.tbl th{ text-align:center; color:var(--mute); font-weight:700; font-size:10.5px; padding:6px 8px; border-bottom:1px solid var(--border); background:var(--bg); cursor:pointer; user-select:none; }
  table.tbl th.lbl{ text-align:left; }
  table.tbl td{ padding:7px 8px; border-bottom:1px solid #F0F2F5; color:#344054; text-align:center; }
  table.tbl td.lbl{ text-align:left; font-weight:600; }

  .table-toolbar{ display:flex; gap:10px; margin-bottom:12px; flex-wrap:wrap; }
  .table-toolbar select, .table-toolbar input{ font-family:inherit; font-size:12px; border:1px solid var(--border); border-radius:8px; padding:7px 10px; color:var(--text); background:#fff; }
  .table-toolbar input{ flex:1; min-width:140px; }
  .table-scroll{ max-height:420px; overflow-y:auto; }
  .flow-tooltip{ position:absolute; background:#fff; border:1px solid #E5E5E5; border-radius:6px; padding:8px 10px; font-size:12px; line-height:1.6; box-shadow:0 4px 12px rgba(0,0,0,.12); z-index:50; }
  .trend-tile{ background:var(--bg); border-radius:8px; padding:10px; }
  .trend-tile .name{ font-size:11.5px; font-weight:700; color:var(--dark); margin-bottom:4px; }
  #trendPersonResults button{ font-size:11.5px; padding:5px 10px; border-radius:14px; border:1px solid var(--border); background:#fff; cursor:pointer; }

  .summary-row{ display:flex; justify-content:space-between; align-items:center; padding:14px 4px; border-bottom:1px dashed var(--border); }
  .summary-row:last-child{ border-bottom:none; }
  .summary-row .lab{ font-size:12.5px; color:var(--sub); }
  .summary-row .sub{ font-size:10.5px; color:var(--mute); margin-top:2px; }
  .summary-row .val{ font-size:22px; font-weight:800; color:var(--neg); }

  .pie-legend{ display:flex; flex-wrap:wrap; justify-content:center; gap:10px; margin-top:10px; font-size:11px; color:#475467; }
  .pie-legend i{ width:10px; height:10px; border-radius:2px; display:inline-block; margin-right:4px; }

  @media (max-width:900px){ .w-half{ width:100%; } }
</style>
</head>
<body>

  <div class="header">
    <div class="titlewrap"><span class="tab"></span><div><h1>우주사업부 근무실적 현황</h1><div class="sub">HR운영팀 · 우주사업부HRBP</div></div></div>
    <div class="header-actions">
      <div class="btns">
        <label class="btn btn-primary" for="rawFile">xlsx 업로드 (여러 달 가능)<input type="file" id="rawFile" accept=".xlsx,.xls" multiple onchange="onRawFiles(this.files)"></label>
        <button type="button" class="btn btn-outline" onclick="exportFilteredCsv()">현재 선택 필터 내용 저장</button>
        <button type="button" class="btn btn-outline" onclick="exportSnapshot()">완성 파일로 저장</button>
      </div>
      <div class="meta" id="metaInfo">데이터 업로드 대기중 <span class="status wait" id="rawStatus">대기중</span></div>
    </div>
  </div>
  <div class="upload-err" id="uploadErrorBanner"></div>

  <div class="filter-bar">
    <select id="siteFilter"><option value="">사업장 전체</option></select>
    <select id="deptTopFilter"><option value="">부서 전체</option></select>
  </div>

  <div class="tab-nav">
    <button id="navTab1" onclick="showTab(1)">📋 근무실적 현황</button>
    <button id="navTab2" onclick="showTab(2)">◆ 사분면 보기</button>
  </div>

  <div class="card w-full" style="margin-bottom:14px;">
    <div style="display:flex; gap:10px; align-items:center; flex-wrap:wrap;">
      <label style="font-size:13px; font-weight:700; color:var(--dark); white-space:nowrap;">인원 검색</label>
      <input id="globalPersonSearch" placeholder="이름을 입력하세요" style="font-family:inherit; font-size:13px; border:1px solid var(--border); border-radius:8px; padding:9px 12px; flex:1; min-width:200px;" onkeydown="if(event.key==='Enter') globalPersonSearch();">
      <button type="button" class="btn btn-primary" onclick="globalPersonSearch()">검색</button>
    </div>
    <div id="globalSearchResult" style="margin-top:12px;"></div>
  </div>

  <div class="kpi-row">
    <div class="kpi"><div class="label">이번달 평균 52시간 이상 근무 인원</div><div class="value" id="kpiAvg52">-</div><span class="chip risk" id="kpiAvg52Chip">이번달 월평균 기준</span></div>
    <div class="kpi"><div class="label">위험군 (2주 이상 연속 52시간)</div><div class="value" id="kpiRiskStreak">-</div><span class="chip risk" id="kpiRiskStreakChip">전체 대비 비중</span></div>
    <div class="kpi"><div class="label">평균 주간 확정근무시간</div><div class="value" id="kpiAvg">-</div><span class="chip neutral" id="kpiAvgChip">이번달 전체 평균</span></div>
    <div class="kpi"><div class="label">주말 근무 발생 인원</div><div class="value" id="kpiWeekend">-</div><span class="chip neutral" id="kpiWeekendChip">이번달, 토/일 1회 이상</span></div>
  </div>

  <div id="tab1">
    <div class="grid">
      <div class="card w-half">
        <div class="card-title"><span class="tab"></span><h2>주별 52시간 이상 근무자 비율 추이</h2><span class="note" id="weeklyTrendNote">전체 업로드 기간</span></div>
        <div id="weeklyTrendContent" class="card-body"><div class="empty-state">로우데이터를 업로드하면 표시됩니다</div></div>
      </div>

      <div class="card w-half">
        <div class="card-title"><span class="tab"></span><h2>부서별 평균 주간 확정근무시간</h2><span class="note" id="deptBarNote">이번달 기준</span></div>
        <div id="deptBarContent" class="card-body"><div class="empty-state">로우데이터를 업로드하면 표시됩니다</div></div>
      </div>

      <div class="card w-half">
        <div class="card-title"><span class="tab" style="background:#7C5FD1"></span><h2>주 근무시간대별 인원 분포</h2><span class="note">이번달 기준</span></div>
        <div id="bucketPieContent" class="card-body"><div class="empty-state">로우데이터를 업로드하면 표시됩니다</div></div>
      </div>

      <div class="card w-half">
        <div class="card-title"><span class="tab"></span><h2>사업장별 평균 주간 확정근무시간</h2><span class="note">이번달 기준</span></div>
        <div id="siteBarContent" class="card-body"><div class="empty-state">로우데이터를 업로드하면 표시됩니다</div></div>
      </div>

      <div class="card w-half">
        <div class="card-title"><span class="tab"></span><h2>평일 요일별 근무시간 편차</h2><span class="note">이번달 평균 대비, 시간</span></div>
        <div id="weekdayDevContent" class="card-body"><div class="empty-state">로우데이터를 업로드하면 표시됩니다</div></div>
      </div>

      <div class="card w-half">
        <div class="card-title"><span class="tab"></span><h2>주말 근무 현황</h2><span class="note">이번달, 1회 이상 근무 인원</span></div>
        <div id="weekendSummaryContent" class="card-body"><div class="empty-state">로우데이터를 업로드하면 표시됩니다</div></div>
      </div>

      <div class="card w-full">
        <div class="card-title"><span class="tab" style="background:var(--neg)"></span><h2>위험군 상세 (연속 52시간 이상 근무자)</h2><span class="note" id="riskTableNote">연속 2주 이상 기준</span></div>
        <div class="table-scroll">
          <table class="tbl" id="riskTable">
            <thead><tr><th class="lbl">이름</th><th class="lbl">부서</th><th class="lbl">직급</th><th>연속 초과주</th><th>최근 주 근무시간</th></tr></thead>
            <tbody id="riskTbody"><tr><td colspan="5" class="empty-state">로우데이터를 업로드하면 표시됩니다</td></tr></tbody>
          </table>
        </div>
      </div>
    </div>
  </div>

  <div id="tab2" style="display:none;">
    <div class="grid">
      <div class="card w-full">
        <div class="card-title"><span class="tab" style="background:#7C5FD1"></span><h2>근무시간 4사분면</h2><span class="note">X: 부서 평균 근무시간 · Y: 부서 내 표준편차 · 원 크기: 인원수</span></div>
        <div id="quadContent" class="card-body"><div class="empty-state">로우데이터를 업로드하면 표시됩니다</div></div>
        <div class="quad-note">점선은 전체 부서 평균 기준입니다. 우측 상단(1사분면)일수록 해당 부서의 평균 근무시간이 길고, 동시에 부서 내 인원들 간 근무시간 편차도 크다는 뜻입니다 — 즉 그 부서 안에서 특정 인원에게 업무가 몰렸을 가능성을 의미하며, "어떤 부서는 많이 일하고 어떤 부서는 적게 일한다"는 부서 간 비교와는 다른 해석입니다.</div>
      </div>
    </div>
  </div>

<script>
/* ===== 0. 설정 ===== */
const EXCLUDE_구분 = ['근태예외자'];
const EXCLUDE_직급 = ['상무','전무','부사장'];
const DEPT_REMAP = { '제조팀':'제주우주센터', '체계기술2팀':'제주우주센터' };
const REQUIRED_COLUMNS = ['구분','년월','주차','사번','이름','부서명','주확정근무시간'];
const MIN_HOURS = 40;
const RISK_HOURS = 52;
const RISK_STREAK_WEEKS = 2;
const DEPT_ORDER = ['우주사업전략팀','우주사업단','솔루션사업팀','차세대위성체계팀','위성체계팀','위성본체팀','위성탑재체1팀','위성탑재체2팀','위성탑재체3팀','위성지상체팀','위성기계팀','제주우주센터'];
function deptSortIndex(d){ const i = DEPT_ORDER.indexOf(d); return i===-1 ? 999 : i; }
const SITE_ORDER = ['서울2','용인','서현','제주'];
function siteSortIndex(s){ const i = SITE_ORDER.indexOf(s); return i===-1 ? 999 : i; }
function hexToRgb(hex){ hex=hex.replace('#',''); return [parseInt(hex.substring(0,2),16),parseInt(hex.substring(2,4),16),parseInt(hex.substring(4,6),16)]; }
function rgbToHex(r,g,b){ return '#'+[r,g,b].map(v=>Math.max(0,Math.min(255,Math.round(v))).toString(16).padStart(2,'0')).join(''); }
function mixColor(hexA,hexB,t){ const [r1,g1,b1]=hexToRgb(hexA), [r2,g2,b2]=hexToRgb(hexB); return rgbToHex(r1+(r2-r1)*t, g1+(g2-g1)*t, b1+(b2-b1)*t); }
function escHtml(s){ return String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;'); }

const state = {
  personal: [], deptStats: [], siteStats: [], avgX: 0, avgY: 0, overallAvg: 0,
  monthly: {}, allWeeklyRows: [], allPeople: [],
  filterSite: '', filterDept: ''
};

/* ===== 1. 업로드 처리 (여러 달 파일을 한번에 선택) ===== */
function showUploadError(msg){ const b=document.getElementById('uploadErrorBanner'); b.innerHTML=msg; b.style.display='block'; }
function clearUploadError(){ const b=document.getElementById('uploadErrorBanner'); b.style.display='none'; b.textContent=''; }
function resetFileStatus(){ const s=document.getElementById('rawStatus'); s.textContent='대기중'; s.classList.remove('ok'); s.classList.add('wait'); }

async function onRawFiles(fileList){
  const files = Array.from(fileList || []).filter(f=>/\.(xlsx|xls)$/i.test(f.name));
  if(!files.length){ showUploadError('엑셀 파일(.xlsx, .xls)만 업로드할 수 있습니다.'); resetFileStatus(); return; }

  try{
    const monthly = {};
    for(const file of files){
      const buf = await file.arrayBuffer();
      const wb = XLSX.read(new Uint8Array(buf), {type:'array'});
      const found = findHeaderAndBuildRows(wb);
      if(!found){
        const preview = describeSheetsForDebug(wb);
        showUploadError('"' + file.name + '"에서 필수 컬럼(' + REQUIRED_COLUMNS.join(', ') + ')을 찾지 못했습니다.<br><br>' + preview);
        resetFileStatus(); return;
      }
      if(found.rows.length===0) continue;
      const agg = computeMonthAggregate(found.rows);
      monthly[agg.ym] = agg;
    }
    if(Object.keys(monthly).length===0){ showUploadError('처리할 데이터가 없습니다.'); resetFileStatus(); return; }

    clearUploadError();
    state.monthly = monthly;
    state.allWeeklyRows = Object.values(monthly).flatMap(m => m.weeklyRows);
    state.filterSite = ''; state.filterDept = '';
    document.getElementById('siteFilter').value = '';
    document.getElementById('deptTopFilter').value = '';

    applyLatestMonth();
    populateTopFilters();
    renderAll();

    const s=document.getElementById('rawStatus'); s.textContent='업로드 완료'; s.classList.remove('wait'); s.classList.add('ok');
  }catch(err){ showUploadError('처리 중 오류: '+err.message); resetFileStatus(); }
}

/* 모든 시트, 첫 10행을 훑어서 필수 컬럼이 다 있는 헤더 행을 찾는다. */
function normalizeHeader(h){ return String(h).replace(/\s+/g,'').trim(); }

function findHeaderAndBuildRows(wb){
  const requiredNorm = REQUIRED_COLUMNS.map(normalizeHeader);
  for(const sheetName of wb.SheetNames){
    const sheet = wb.Sheets[sheetName];
    const raw = XLSX.utils.sheet_to_json(sheet, {header:1, defval:'', blankrows:false});
    const scanLimit = Math.min(10, raw.length);
    for(let i=0; i<scanLimit; i++){
      const headerRow = raw[i].map(h=>String(h).trim());
      const headerNorm = headerRow.map(normalizeHeader);
      const hasAll = requiredNorm.every(rc => headerNorm.includes(rc));
      if(hasAll){
        const rows = raw.slice(i+1)
          .filter(r => r.some(cell => String(cell).trim() !== ''))
          .map(r => {
            const obj = {};
            headerRow.forEach((h, idx) => { if(h) obj[h] = r[idx] !== undefined ? r[idx] : ''; });
            return obj;
          });
        return { rows, sheetName, headerRowIndex:i };
      }
    }
  }
  return null;
}

function describeSheetsForDebug(wb){
  return wb.SheetNames.slice(0,3).map(name=>{
    const sheet = wb.Sheets[name];
    const raw = XLSX.utils.sheet_to_json(sheet, {header:1, defval:'', blankrows:false});
    const firstRow = (raw[0]||[]).map(h=>String(h).trim()).filter(h=>h!=='').join(' | ') || '(비어있음)';
    return `· "${name}" 시트 1행: ${firstRow}`;
  }).join('<br>');
}

/* ===== 2. 데이터 처리 ===== */
function preprocessRows(rows){
  return rows.filter(r =>
    !EXCLUDE_구분.includes(String(r['구분']).trim()) &&
    !EXCLUDE_직급.includes(String(r['직급']).trim())
  ).map(r=>{
    const dept = String(r['부서명']).trim();
    return { ...r, 부서명: DEPT_REMAP[dept] || dept, __minutes: Number(r['주확정근무시간']) || 0 };
  });
}

/* '202607'+'2주차' -> 연속 주차 비교가 가능한 정수 ID (월 4주 고정 가정) */
function computeWeekId(ymStr, week){
  const s = String(ymStr);
  if(s.length !== 6) return null;
  const y = parseInt(s.slice(0,4),10), m = parseInt(s.slice(4,6),10);
  return (y*48) + ((m-1)*4) + (Number(week)-1);
}

function aggregate(cleaned){
  const personMap = new Map();
  cleaned.forEach(r=>{
    const key = String(r['사번']).trim();
    if(!personMap.has(key)) personMap.set(key, { 사번:key, 이름:r['이름'], 직급:r['직급'], 부서명:r['부서명'], 사업장:r['사업장'], sumMinutes:0, cnt:0 });
    const p = personMap.get(key);
    p.sumMinutes += r['__minutes']; p.cnt += 1;
  });
  const personal = [...personMap.values()].map(p => ({
    사번:p.사번, 이름:p.이름, 직급:p.직급, 부서명:p.부서명, 사업장:p.사업장,
    월평균근무시간: Math.max(MIN_HOURS, Math.round((p.sumMinutes / p.cnt / 60) * 10) / 10)
  }));

  function groupStats(field){
    const map = new Map();
    personal.forEach(p=>{
      const key = p[field];
      if(!map.has(key)) map.set(key, []);
      map.get(key).push(p.월평균근무시간);
    });
    return [...map.entries()].map(([name, values])=>{
      const n = values.length;
      const mean = values.reduce((a,b)=>a+b,0) / n;
      let std = 0;
      if(n > 1){
        const variance = values.reduce((a,v)=>a + Math.pow(v-mean,2), 0) / (n-1);
        std = Math.sqrt(variance);
      }
      return { 이름:name, 평균:Math.max(MIN_HOURS, Math.round(mean*10)/10), 표준편차:Math.round(std*100)/100, 인원수:n };
    }).sort((a,b)=>b.평균 - a.평균);
  }

  const deptStats = groupStats('부서명').map(d=>({ 부서명:d.이름, 부서평균:d.평균, 부서표준편차:d.표준편차, 인원수:d.인원수 }));
  const siteStats = groupStats('사업장').map(d=>({ 사업장:d.이름, 평균:d.평균, 인원수:d.인원수 }));

  return { personal, deptStats, siteStats };
}

function mostCommonYm(rows){
  const counts = {};
  rows.forEach(r=>{ const ym=String(r['년월']).trim(); if(ym) counts[ym]=(counts[ym]||0)+1; });
  let best=null, bestCount=-1;
  Object.entries(counts).forEach(([ym,c])=>{ if(c>bestCount){ best=ym; bestCount=c; } });
  return best || 'unknown';
}

function computeMonthAggregate(rows){
  const cleaned = preprocessRows(rows);
  const ym = mostCommonYm(cleaned);
  const { personal, deptStats, siteStats } = aggregate(cleaned);
  const weeklyRows = cleaned.map(r=>({
    사번: String(r['사번']).trim(), 이름:r['이름'], 직급:r['직급'], 부서명:r['부서명'], 사업장:r['사업장'],
    년월: String(r['년월']).trim(), 주차: Number(r['주차']),
    weekId: computeWeekId(r['년월'], r['주차']),
    hours: Math.round((r.__minutes/60)*10)/10,
    월:Number(r['월'])||0, 화:Number(r['화'])||0, 수:Number(r['수'])||0, 목:Number(r['목'])||0, 금:Number(r['금'])||0,
    토:Number(r['토'])||0, 일:Number(r['일'])||0
  }));
  return { ym, personal, deptStats, siteStats, weeklyRows };
}

function formatYm(ym, short){
  const s = String(ym);
  if(s.length !== 6 || isNaN(Number(s))) return s;
  const y = s.slice(0,4), m = parseInt(s.slice(4,6), 10);
  return short ? (m + '월') : (y + '년 ' + m + '월');
}

function applyLatestMonth(){
  const yms = Object.keys(state.monthly).sort();
  const latestYm = yms[yms.length-1];
  const latest = state.monthly[latestYm];
  state.latestYm = latestYm;
  state.personal = latest.personal;
  state.deptStats = latest.deptStats;
  state.siteStats = latest.siteStats;
  state.avgX = state.deptStats.reduce((a,d)=>a+d.부서평균,0) / (state.deptStats.length||1);
  state.avgY = state.deptStats.reduce((a,d)=>a+d.부서표준편차,0) / (state.deptStats.length||1);
  state.overallAvg = state.personal.length ? Math.round((state.personal.reduce((a,p)=>a+p.월평균근무시간,0)/state.personal.length)*10)/10 : 0;
  const today = new Date();
  const todayStr = today.getFullYear() + '-' + String(today.getMonth()+1).padStart(2,'0') + '-' + String(today.getDate()).padStart(2,'0');
  document.getElementById('metaInfo').innerHTML =
    '데이터 반영일: ' + todayStr + ' · 최신 데이터 기준월: ' + formatYm(latestYm) + (yms.length>1 ? ` · 누적 ${yms.length}개월 업로드됨` : '') +
    ' <span class="status ok" id="rawStatus">업로드 완료</span>';
}

/* ===== 2-1. 필터(사업장/부서) ===== */
function populateTopFilters(){
  const sites = [...new Set(state.personal.map(p=>p.사업장).filter(Boolean))].sort((a,b)=> siteSortIndex(a)-siteSortIndex(b) || a.localeCompare(b));
  const depts = [...new Set(state.personal.map(p=>p.부서명))].sort((a,b)=> deptSortIndex(a)-deptSortIndex(b) || a.localeCompare(b));
  document.getElementById('siteFilter').innerHTML = '<option value="">사업장 전체</option>' + sites.map(s=>`<option value="${escHtml(s)}">${escHtml(s)}</option>`).join('');
  document.getElementById('deptTopFilter').innerHTML = '<option value="">부서 전체</option>' + depts.map(d=>`<option value="${escHtml(d)}">${escHtml(d)}</option>`).join('');
}
/* ===== 15. 상단 인원 검색 (이름 검색 -> 요약 + 추이) ===== */
function globalPersonSearch(){
  const q = document.getElementById('globalPersonSearch').value.trim();
  const box = document.getElementById('globalSearchResult');
  if(!q){ box.innerHTML = ''; return; }

  const matches = state.personal.filter(p => String(p.이름).includes(q));
  if(!matches.length){
    box.innerHTML = '<div class="empty-state">일치하는 인원이 없습니다</div>';
    return;
  }
  if(matches.length > 1){
    box.innerHTML = '<div style="font-size:12px; color:var(--sub); margin-bottom:6px;">' + matches.length + '명이 검색되었습니다. 한 명을 선택하세요.</div>' +
      '<div style="display:flex; gap:6px; flex-wrap:wrap;">' +
      matches.map(p=>`<button type="button" class="btn btn-outline" onclick="showGlobalPersonDetail('${escHtml(p.사번)}')">${escHtml(p.이름)} (${escHtml(p.부서명)})</button>`).join('') +
      '</div>';
    return;
  }
  showGlobalPersonDetail(matches[0].사번);
}

function showGlobalPersonDetail(sabun){
  const box = document.getElementById('globalSearchResult');
  const p = state.personal.find(x=>x.사번===sabun);
  if(!p){ box.innerHTML = '<div class="empty-state">일치하는 인원이 없습니다</div>'; return; }

  const rows = state.allWeeklyRows.filter(r=>r.사번===sabun);
  const streaks = computeStreaks(rows);
  const s = streaks.get(sabun) || { tailStreak:0, lastHours:null };

  const yms = Object.keys(state.monthly).sort();
  const series = yms.map(ym=>{
    const mp = state.monthly[ym].personal.find(x=>x.사번===sabun);
    return mp ? { ym, label:mp.이름, value:mp.월평균근무시간 } : null;
  }).filter(Boolean);

  let trendHtml = '<div class="empty-state">추이를 그릴 데이터가 없습니다</div>';
  if(series.length){
    const vals = series.map(v=>v.value);
    const yMin = Math.min(...vals), yMax = Math.max(...vals);
    const yPad = (yMax - yMin || 1) * 0.2;
    const w = 480, h = 120;
    const svg = buildSparkline(series, [yMin - yPad, yMax + yPad], w, h, 24);
    const monthLabels = series.map(v=>`<span style="width:${(w/series.length).toFixed(1)}px; display:inline-block; text-align:center;">${formatYm(v.ym, true)}</span>`).join('');
    trendHtml = svg + `<div style="display:flex;font-size:10.5px;color:var(--mute);margin-top:2px;">${monthLabels}</div>`;
  }

  box.innerHTML = `
    <div style="display:flex; gap:24px; flex-wrap:wrap; align-items:flex-start;">
      <div style="min-width:180px;">
        <div style="font-size:16px; font-weight:800; color:var(--dark);">${escHtml(p.이름)}</div>
        <div style="font-size:12px; color:var(--sub); margin-top:2px;">${escHtml(p.부서명)} · ${escHtml(p.직급||'')} · ${escHtml(p.사업장||'')}</div>
        <div style="margin-top:10px; font-size:12px; color:var(--sub);">이번달 평균 근무시간</div>
        <div style="font-size:22px; font-weight:800; color:var(--dark);">${p.월평균근무시간}시간</div>
        <div style="margin-top:8px;">
          <span class="chip ${s.tailStreak>=RISK_STREAK_WEEKS ? 'risk' : 'neutral'}">연속 52시간↑ ${s.tailStreak}주</span>
        </div>
      </div>
      <div style="flex:1; min-width:280px;">
        <div style="font-size:12px; font-weight:700; color:var(--dark); margin-bottom:6px;">월별 근무시간 추이</div>
        ${trendHtml}
      </div>
    </div>`;

  // 인원 검색 시 부서 필터 참고용으로 사용 (선택된 사람의 부서 저장)
}

document.getElementById('siteFilter').addEventListener('change', function(){ state.filterSite = this.value; renderAll(); });
document.getElementById('deptTopFilter').addEventListener('change', function(){ state.filterDept = this.value; renderAll(); });

function filteredPersonal(){
  return state.personal.filter(p => (!state.filterSite || p.사업장===state.filterSite) && (!state.filterDept || p.부서명===state.filterDept));
}
function rowMatchesFilter(r){
  return (!state.filterSite || r.사업장===state.filterSite) && (!state.filterDept || r.부서명===state.filterDept);
}
function latestMonthWeeklyRows(){
  return (state.monthly[state.latestYm] ? state.monthly[state.latestYm].weeklyRows : []).filter(rowMatchesFilter);
}

/* ===== 2-2. 탭 전환 ===== */
function showTab(n){
  document.getElementById('tab1').style.display = n===1 ? 'block' : 'none';
  document.getElementById('tab2').style.display = n===2 ? 'block' : 'none';
  document.getElementById('navTab1').classList.toggle('active', n===1);
  document.getElementById('navTab2').classList.toggle('active', n===2);
}
showTab(1);

/* ===== 3. 전체 렌더 ===== */
function renderAll(){
  renderKPI();
  renderWeeklyTrend();
  renderDeptBar();
  renderBucketPie();
  renderSiteBar();
  renderWeekdayDeviation();
  renderWeekendSummary();
  renderRiskTable();
  renderQuadrant();
  renderMonthlyTrend();
}

/* ===== 4. KPI ===== */
/* 사번별 정렬된 주간 기록에서, 마지막 주까지 이어지는 '연속 52시간↑' 스트릭 계산 */
function computeStreaks(weeklyRows){
  const byPerson = new Map();
  weeklyRows.forEach(r=>{
    if(!byPerson.has(r.사번)) byPerson.set(r.사번, []);
    byPerson.get(r.사번).push(r);
  });
  const result = new Map(); // 사번 -> { tailStreak, lastHours, 이름, 부서명, 직급 }
  byPerson.forEach((rows, sabun)=>{
    rows.sort((a,b)=> a.weekId - b.weekId);
    let tail = 0;
    for(let i=rows.length-1; i>=0; i--){
      const r = rows[i];
      if(r.hours < RISK_HOURS) break;
      if(i < rows.length-1 && rows[i+1].weekId - r.weekId !== 1) break;
      tail++;
    }
    const last = rows[rows.length-1];
    result.set(sabun, { tailStreak: tail, lastHours: last.hours, 이름:last.이름, 부서명:last.부서명, 직급:last.직급 });
  });
  return result;
}

function renderKPI(){
  const filtered = filteredPersonal();

  const avg52 = filtered.filter(p => p.월평균근무시간 >= RISK_HOURS).length;
  document.getElementById('kpiAvg52').textContent = avg52 + '명';
  document.getElementById('kpiAvg52Chip').textContent = '이번달 월평균 기준';
  document.getElementById('kpiAvg52Chip').className = avg52>0 ? 'chip risk' : 'chip up';

  const overall = filtered.length ? Math.round((filtered.reduce((a,p)=>a+p.월평균근무시간,0)/filtered.length)*10)/10 : 0;
  document.getElementById('kpiAvg').textContent = overall.toFixed(1) + '시간';
  document.getElementById('kpiAvgChip').textContent = '이번달 전체 평균';
  document.getElementById('kpiAvgChip').className = 'chip neutral';

  const allRows = state.allWeeklyRows.filter(rowMatchesFilter);
  const streaks = computeStreaks(allRows);
  const riskStreak = [...streaks.values()].filter(s=>s.tailStreak >= RISK_STREAK_WEEKS).length;
  const totalPeople = new Set(allRows.map(r=>r.사번)).size || 1;
  document.getElementById('kpiRiskStreak').textContent = riskStreak + '명';
  document.getElementById('kpiRiskStreakChip').textContent = '전체 대비 ' + Math.round(riskStreak/totalPeople*1000)/10 + '%';
  document.getElementById('kpiRiskStreakChip').className = riskStreak>0 ? 'chip risk' : 'chip up';

  const latestRows = latestMonthWeeklyRows();
  const satPeople = new Set(latestRows.filter(r=>r.토>0).map(r=>r.사번));
  const sunPeople = new Set(latestRows.filter(r=>r.일>0).map(r=>r.사번));
  const weekendUnion = new Set([...satPeople, ...sunPeople]);
  document.getElementById('kpiWeekend').textContent = weekendUnion.size + '명';
  document.getElementById('kpiWeekendChip').textContent = '이번달, 토/일 1회 이상';
  document.getElementById('kpiWeekendChip').className = 'chip neutral';
}

/* ===== 5. 주별 52시간 이상 근무자 수 추이 ===== */
function renderWeeklyTrend(){
  const rows = state.allWeeklyRows.filter(rowMatchesFilter);
  const weekMap = new Map(); // weekId -> {ym, week, over:Set, all:Set}
  rows.forEach(r=>{
    if(!weekMap.has(r.weekId)) weekMap.set(r.weekId, { ym:r.년월, week:r.주차, over:new Set(), all:new Set() });
    const m = weekMap.get(r.weekId);
    m.all.add(r.사번);
    if(r.hours >= RISK_HOURS) m.over.add(r.사번);
  });
  const weekIds = [...weekMap.keys()].sort((a,b)=>a-b);
  const box = document.getElementById('weeklyTrendContent');
  if(!weekIds.length){ box.innerHTML = '<div class="empty-state">로우데이터를 업로드하면 표시됩니다</div>'; return; }
  const series = weekIds.map(wid=>{
    const m = weekMap.get(wid);
    const pct = m.all.size ? Math.round((m.over.size / m.all.size) * 1000) / 10 : 0;
    return { ym:m.ym, label: formatYm(m.ym, true) + ' ' + m.week + '주', value: pct };
  });
  const pcts = series.map(s=>s.value);
  const yMax = Math.max(5, ...pcts);
  const svg = buildLineChart(series, [0, yMax], 600, 160, 30, '%');
  box.innerHTML = svg;
}

/* 일반 라인차트 (x축 라벨 표시 포함) - series: [{label,value}] */
function buildLineChart(series, yDomain, w, h, pad, unit){
  unit = unit || '';
  const [yMin, yMax] = yDomain;
  const stepX = series.length > 1 ? (w - pad*2) / (series.length - 1) : 0;
  const xs = series.map((v,i)=> pad + i*stepX);
  const ys = series.map(v => (h - pad) - (v.value - yMin) / ((yMax - yMin) || 1) * (h - pad*2 - 16));
  const points = xs.map((x,i)=>`${x},${ys[i]}`).join(' ');
  const circles = series.map((v,i)=> `<circle class="trend-dot" cx="${xs[i]}" cy="${ys[i]}" r="4" fill="#35578C" stroke="#fff" stroke-width="1.5" data-label="${escHtml(v.label)}" data-ym="" data-value="${v.value}${unit}" style="cursor:pointer"/>`).join('');
  const labels = series.map((v,i)=> i % Math.ceil(series.length/8 || 1) === 0 ? `<text x="${xs[i]}" y="${h-6}" text-anchor="middle" font-size="9.5" fill="#98A2B3">${escHtml(v.label)}</text>` : '').join('');
  return `<svg viewBox="0 0 ${w} ${h}" width="100%" height="${h}" style="overflow:visible;"><polyline points="${points}" fill="none" stroke="#6C8FC4" stroke-width="2"/>${circles}${labels}</svg>`;
}

/* ===== 6. 부서별 평균 근무시간 막대 ===== */
function renderDeptBar(){
  const filtered = filteredPersonal();
  const map = new Map();
  filtered.forEach(p=>{ if(!map.has(p.부서명)) map.set(p.부서명, []); map.get(p.부서명).push(p.월평균근무시간); });
  const deptStats = [...map.entries()].map(([dept, values])=>({
    부서명:dept, 부서평균: Math.round((values.reduce((a,b)=>a+b,0)/values.length)*10)/10
  })).sort((a,b)=>b.부서평균-a.부서평균);

  const box = document.getElementById('deptBarContent');
  if(!deptStats.length){ box.innerHTML = '<div class="empty-state">로우데이터를 업로드하면 표시됩니다</div>'; return; }
  const avg = Math.round((deptStats.reduce((a,d)=>a+d.부서평균,0)/deptStats.length)*10)/10;
  const max = Math.max(1, ...deptStats.map(d=>d.부서평균));
  const markPct = Math.min(100, Math.max(0, (avg/max)*100));
  const legend = `<div class="track-mark-legend"><span class="track-mark-swatch"></span>전체 평균 ${avg}시간</div>`;
  const n = deptStats.length;
  const rows = deptStats.map((d,i)=>{
    const pct = Math.max(2, Math.round(d.부서평균/max*100));
    const color = mixColor('#1E3358', '#A9C3E6', n>1 ? i/(n-1) : 0);
    return `<div class="barrow"><div class="name">${escHtml(d.부서명)}</div><div class="track"><div class="track-mark" style="left:${markPct}%"></div><div class="fill" style="width:${pct}%; background:${color};"></div></div><div class="num">${d.부서평균}시간</div></div>`;
  }).join('');
  box.innerHTML = legend + rows;
}

/* ===== 7. 사업장별 평균 근무시간 막대 ===== */
function renderSiteBar(){
  const filtered = filteredPersonal();
  const map = new Map();
  filtered.forEach(p=>{ const site=p.사업장||'미기재'; if(!map.has(site)) map.set(site, []); map.get(site).push(p.월평균근무시간); });
  const siteStats = [...map.entries()].map(([site, values])=>({
    사업장:site, 평균: Math.round((values.reduce((a,b)=>a+b,0)/values.length)*10)/10
  })).sort((a,b)=>b.평균-a.평균);

  const box = document.getElementById('siteBarContent');
  if(!siteStats.length){ box.innerHTML = '<div class="empty-state">로우데이터를 업로드하면 표시됩니다</div>'; return; }
  const avg = Math.round((siteStats.reduce((a,d)=>a+d.평균,0)/siteStats.length)*10)/10;
  const max = Math.max(1, ...siteStats.map(d=>d.평균));
  const markPct = Math.min(100, Math.max(0, (avg/max)*100));
  const legend = `<div class="track-mark-legend"><span class="track-mark-swatch"></span>전체 평균 ${avg}시간</div>`;
  const rows = siteStats.map(d=>{
    const pct = Math.max(2, Math.round(d.평균/max*100));
    return `<div class="barrow"><div class="name">${escHtml(d.사업장)}</div><div class="track"><div class="track-mark" style="left:${markPct}%"></div><div class="fill" style="width:${pct}%;"></div></div><div class="num">${d.평균}시간</div></div>`;
  }).join('');
  box.innerHTML = legend + rows;
}

/* ===== 8. 주 근무시간대별 인원 분포 (도넛) ===== */
const HOUR_BUCKETS = [
  { label:'40~44시간', min:40, max:44 },
  { label:'44~48시간', min:44, max:48 },
  { label:'48~52시간', min:48, max:52 },
  { label:'52~56시간', min:52, max:56 },
  { label:'56시간 이상', min:56, max:Infinity }
];
function renderBucketPie(){
  const filtered = filteredPersonal();
  const box = document.getElementById('bucketPieContent');
  if(!filtered.length){ box.innerHTML = '<div class="empty-state">로우데이터를 업로드하면 표시됩니다</div>'; return; }
  const counts = HOUR_BUCKETS.map(b => filtered.filter(p => p.월평균근무시간 >= b.min && p.월평균근무시간 < b.max).length);
  const colors = HOUR_BUCKETS.map((b,i)=> mixColor('#A9C3E6', '#E4572E', i/(HOUR_BUCKETS.length-1)));
  const total = counts.reduce((a,b)=>a+b,0) || 1;

  const size = 220, cx = size/2, cy = size/2, r = size*0.32;
  let start = -90, paths = '';
  HOUR_BUCKETS.forEach((b,i)=>{
    const sweep = (counts[i]/total) * 360;
    const end = start + sweep;
    const toRad = d=>d*Math.PI/180;
    const [x1,y1] = [cx+r*Math.cos(toRad(start)), cy+r*Math.sin(toRad(start))];
    const [x2,y2] = [cx+r*Math.cos(toRad(end)), cy+r*Math.sin(toRad(end))];
    const large = sweep>180 ? 1 : 0;
    if(counts[i]>0) paths += `<path d="M${cx},${cy} L${x1},${y1} A${r},${r} 0 ${large} 1 ${x2},${y2} Z" fill="${colors[i]}" stroke="#fff" stroke-width="1.5"/>`;
    start = end;
  });
  const legend = HOUR_BUCKETS.map((b,i)=> `<span><i style="background:${colors[i]}"></i>${b.label} ${counts[i]}명</span>`).join('');
  box.innerHTML = `<svg width="100%" height="${size}" viewBox="0 0 ${size} ${size}">${paths}</svg><div class="pie-legend">${legend}</div>`;
}

/* ===== 9. 평일 요일별 근무시간 편차 ===== */
const WEEKDAY_COLS = ['월','화','수','목','금'];
function renderWeekdayDeviation(){
  const rows = latestMonthWeeklyRows();
  const box = document.getElementById('weekdayDevContent');
  if(!rows.length){ box.innerHTML = '<div class="empty-state">로우데이터를 업로드하면 표시됩니다</div>'; return; }
  const dayAvg = WEEKDAY_COLS.map(day => {
    const total = rows.reduce((a,r)=>a + (r[day]||0), 0);
    return Math.round((total / rows.length / 60) * 100) / 100; // 시간, 소수점 2자리
  });
  const overall = Math.round((dayAvg.reduce((a,b)=>a+b,0) / dayAvg.length) * 100) / 100;
  const dev = dayAvg.map(v => Math.round((v - overall) * 100) / 100);

  const maxAbs = Math.max(0.1, ...dev.map(Math.abs));
  const items = WEEKDAY_COLS.map((day,i)=>{
    const v = dev[i];
    const color = v < 0 ? 'var(--neg)' : 'var(--c1)';
    const barH = Math.max(3, Math.abs(v)/maxAbs*40);
    return `<div style="flex:1; text-align:center;">
      <div style="height:44px; display:flex; align-items:flex-end; justify-content:center;">
        <div style="width:14px; height:${barH}px; background:${color}; border-radius:3px 3px 0 0;"></div>
      </div>
      <div style="font-size:11px; font-weight:700; color:${color}; margin-top:4px;">${v>0?'+':''}${v}h</div>
      <div style="font-size:11px; color:var(--mute); margin-top:2px;">${day}</div>
    </div>`;
  }).join('');
  box.innerHTML = `<div style="display:flex; align-items:flex-end; gap:6px;">${items}</div>
    <div style="font-size:10.5px; color:var(--mute); margin-top:10px; text-align:center;">요일 평균 ${overall}시간 기준 편차</div>`;
}

/* ===== 10. 주말 근무 현황 ===== */
function renderWeekendSummary(){
  const rows = latestMonthWeeklyRows();
  const box = document.getElementById('weekendSummaryContent');
  if(!rows.length){ box.innerHTML = '<div class="empty-state">로우데이터를 업로드하면 표시됩니다</div>'; return; }
  const satPeople = new Set(rows.filter(r=>r.토>0).map(r=>r.사번));
  const sunPeople = new Set(rows.filter(r=>r.일>0).map(r=>r.사번));
  box.innerHTML = `
    <div class="summary-row"><div><div class="lab">토요일 근무</div><div class="sub">이번달 1회 이상 근무 인원</div></div><div class="val">${satPeople.size}명</div></div>
    <div class="summary-row"><div><div class="lab">일요일 근무</div><div class="sub">이번달 1회 이상 근무 인원</div></div><div class="val">${sunPeople.size}명</div></div>
  `;
}

/* ===== 11. 위험군 상세 테이블 ===== */
function renderRiskTable(){
  const rows = state.allWeeklyRows.filter(rowMatchesFilter);
  const streaks = computeStreaks(rows);
  const list = [...streaks.entries()]
    .filter(([,s]) => s.tailStreak >= RISK_STREAK_WEEKS)
    .map(([sabun,s]) => ({ 사번:sabun, ...s }))
    .sort((a,b) => b.tailStreak - a.tailStreak || b.lastHours - a.lastHours);

  const tbody = document.getElementById('riskTbody');
  if(!list.length){ tbody.innerHTML = '<tr><td colspan="5" class="empty-state">연속 2주 이상 52시간을 초과한 인원이 없습니다</td></tr>'; return; }
  tbody.innerHTML = list.map(r => `
    <tr><td class="lbl">${escHtml(r.이름)}</td><td class="lbl">${escHtml(r.부서명)}</td><td class="lbl">${escHtml(r.직급||'')}</td>
    <td><span class="chip risk">${r.tailStreak}주</span></td><td>${r.lastHours}시간</td></tr>
  `).join('');
}

/* ===== 12. 4사분면 (SVG, 원 크기 = 인원수) ===== */
function renderQuadrant(){
  const filtered = filteredPersonal();
  const map = new Map();
  filtered.forEach(p=>{ if(!map.has(p.부서명)) map.set(p.부서명, []); map.get(p.부서명).push(p.월평균근무시간); });
  const data = [...map.entries()].map(([dept, values])=>{
    const n = values.length;
    const mean = values.reduce((a,b)=>a+b,0)/n;
    let std = 0;
    if(n>1){ const variance = values.reduce((a,v)=>a+Math.pow(v-mean,2),0)/(n-1); std = Math.sqrt(variance); }
    return { 부서명:dept, 부서평균:Math.round(mean*10)/10, 부서표준편차:Math.round(std*100)/100, 인원수:n };
  });

  const box = document.getElementById('quadContent');
  if(!data.length){ box.innerHTML = '<div class="empty-state">표시할 데이터가 없습니다</div>'; return; }

  const avgX = data.reduce((a,d)=>a+d.부서평균,0)/data.length;
  const avgY = data.reduce((a,d)=>a+d.부서표준편차,0)/data.length;

  const W = 900, H = 460, pad = 60;
  const xs = data.map(d=>d.부서평균), ys = data.map(d=>d.부서표준편차);
  const xMin = Math.min(...xs, avgX), xMax = Math.max(...xs, avgX);
  const yMin = Math.min(...ys, avgY), yMax = Math.max(...ys, avgY);
  const xPad = (xMax-xMin || 1) * 0.15, yPad = (yMax-yMin || 1) * 0.2;
  const x0 = xMin-xPad, x1 = xMax+xPad, y0 = yMin-yPad, y1 = yMax+yPad;
  const sx = v => pad + (v-x0)/(x1-x0) * (W-pad*2);
  const sy = v => (H-pad) - (v-y0)/(y1-y0) * (H-pad*2);
  const avgXpix = sx(avgX), avgYpix = sy(avgY);
  const maxN = Math.max(1, ...data.map(d=>d.인원수));

  const gridLines = `
    <line x1="${avgXpix}" y1="${pad}" x2="${avgXpix}" y2="${H-pad}" stroke="#D9A441" stroke-width="1.5" stroke-dasharray="5,4"/>
    <line x1="${pad}" y1="${avgYpix}" x2="${W-pad}" y2="${avgYpix}" stroke="#D9A441" stroke-width="1.5" stroke-dasharray="5,4"/>`;
  const axisLines = `
    <line x1="${pad}" y1="${H-pad}" x2="${W-pad}" y2="${H-pad}" stroke="#D0D5DD" stroke-width="1"/>
    <line x1="${pad}" y1="${pad}" x2="${pad}" y2="${H-pad}" stroke="#D0D5DD" stroke-width="1"/>`;
  const axisLabels = `
    <text x="${W/2}" y="${H-16}" text-anchor="middle" font-size="12" fill="#667085">부서 평균 근무시간(시간) →</text>
    <text x="18" y="${H/2}" text-anchor="middle" font-size="12" fill="#667085" transform="rotate(-90 18 ${H/2})">부서 내 표준편차 →</text>`;
  const points = data.map(d=>{
    const cx = sx(d.부서평균), cy = sy(d.부서표준편차);
    const radius = 6 + (d.인원수/maxN) * 16;
    const color = mixColor('#A9C3E6', '#E4572E', Math.max(0, Math.min(1, (d.부서평균-40)/20)));
    return `<g>
      <circle cx="${cx}" cy="${cy}" r="${radius}" fill="${color}" fill-opacity="0.85" stroke="#fff" stroke-width="1.5"/>
      <text x="${cx}" y="${cy-radius-5}" text-anchor="middle" font-size="11" font-weight="700" fill="#1E3358">${escHtml(d.부서명)}</text>
    </g>`;
  }).join('');

  const legend = `<div class="legend-row">
    <span><i style="background:${mixColor('#A9C3E6','#E4572E',0)}"></i>~40시간</span>
    <span><i style="background:${mixColor('#A9C3E6','#E4572E',0.5)}"></i>~50시간</span>
    <span><i style="background:${mixColor('#A9C3E6','#E4572E',1)}"></i>60시간~</span>
    <span style="margin-left:10px;">원 크기 = 부서 인원수</span>
  </div>`;

  box.innerHTML = `<svg width="100%" viewBox="0 0 ${W} ${H}" style="overflow:visible;">${axisLines}${gridLines}${points}${axisLabels}</svg>${legend}`;
}

/* ===== 14. 월별 추이 ===== */
function buildSparkline(values, yDomain, w, h, pad){
  w = w || 150; h = h || 70; pad = pad || 14;
  const [yMin, yMax] = yDomain;
  const stepX = values.length > 1 ? (w - pad*2) / (values.length - 1) : 0;
  const xs = values.map((v,i) => pad + i*stepX);
  const ys = values.map(v => (h - pad) - (v.value - yMin) / ((yMax - yMin) || 1) * (h - pad*2));
  const points = xs.map((x,i) => `${x},${ys[i]}`).join(' ');
  const circles = values.map((v,i) => `<circle class="trend-dot" cx="${xs[i]}" cy="${ys[i]}" r="3.5" fill="#35578C" stroke="#fff" stroke-width="1" data-label="${escHtml(v.label)}" data-ym="${v.ym}" data-value="${v.value}" style="cursor:pointer"/>`).join('');
  return `<svg viewBox="0 0 ${w} ${h}" width="100%" height="${h}" style="overflow:visible;"><polyline points="${points}" fill="none" stroke="#6C8FC4" stroke-width="2"/>${circles}</svg>`;
}

function showTrendTooltip(e, label, ym, value){
  let tip = document.getElementById('trendTooltip');
  if(!tip){ tip = document.createElement('div'); tip.id = 'trendTooltip'; tip.className = 'flow-tooltip'; document.body.appendChild(tip); }
  tip.innerHTML = ym ? `<div style="font-weight:700;margin-bottom:2px;">${escHtml(label)}</div><div>${formatYm(ym)} · ${value}시간</div>`
                      : `<div style="font-weight:700;">${escHtml(label)}: ${value}</div>`;
  tip.style.left = (e.pageX + 12) + 'px'; tip.style.top = (e.pageY + 12) + 'px'; tip.style.display = 'block';
}
document.addEventListener('click', e=>{
  if(e.target.classList && e.target.classList.contains('trend-dot')){
    showTrendTooltip(e, e.target.dataset.label, e.target.dataset.ym, e.target.dataset.value);
  } else {
    const tip = document.getElementById('trendTooltip');
    if(tip) tip.style.display = 'none';
  }
});

function renderMonthlyTrend(){
  // 개인 검색용 전체 인원 목록만 갱신 (부서/사분면 스파크라인 그리드 카드는 사용하지 않음)
  const yms = Object.keys(state.monthly).sort();
  const personMapAll = new Map();
  yms.forEach(ym => state.monthly[ym].personal.forEach(p=>{
    personMapAll.set(p.사번, { 사번:p.사번, 이름:p.이름, 부서명:p.부서명 });
  }));
  state.allPeople = [...personMapAll.values()];
}

/* ===== 15. 현재 화면 스냅샷 저장 ===== */
function exportSnapshot(){
  if(!state.personal.length){ alert('먼저 로우데이터를 업로드해주세요.'); return; }
  const clone = document.documentElement.cloneNode(true);
  const dataBar = clone.querySelector('.header-actions .btns'); if(dataBar) dataBar.remove();
  const html = '<!DOCTYPE html>\n' + clone.outerHTML;
  const blob = new Blob([html], {type:'text/html;charset=utf-8'});
  const url = URL.createObjectURL(blob); const a = document.createElement('a');
  a.href = url; a.download = `우주사업부_근무실적_${state.latestYm||'snapshot'}.html`;
  document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
}

/* 현재 선택된 사업장/부서 필터가 반영된 인원 목록을 CSV로 저장 */
function exportFilteredCsv(){
  const filtered = filteredPersonal();
  if(!filtered.length){ alert('먼저 로우데이터를 업로드해주세요.'); return; }
  const header = ['사번','이름','직급','부서명','사업장','월평균근무시간'];
  const csvRows = [header.join(',')].concat(
    filtered.map(p => [p.사번, p.이름, p.직급||'', p.부서명, p.사업장||'', p.월평균근무시간].map(v=>`"${String(v).replace(/"/g,'""')}"`).join(','))
  );
  const csv = '\uFEFF' + csvRows.join('\n'); // BOM 포함 (엑셀 한글 깨짐 방지)
  const blob = new Blob([csv], {type:'text/csv;charset=utf-8'});
  const url = URL.createObjectURL(blob); const a = document.createElement('a');
  const filterLabel = (state.filterSite||'전체') + '_' + (state.filterDept||'전체');
  a.href = url; a.download = `우주사업부_필터목록_${filterLabel}_${state.latestYm||''}.csv`;
  document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
}
</script>
</body>
</html>
