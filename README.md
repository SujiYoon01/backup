<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<title>우주사업부 근무시간 분석 대시보드</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<style>
  :root{
    --c1:#35578C; --c2:#6C8FC4; --c3:#A9C3E6; --c4:#E3EDF9;
    --dark:#1E3358; --bg:#F7F9FC; --card:#FFFFFF; --border:#E4E7EC;
    --text:#101828; --sub:#667085; --mute:#98A2B3; --pos:#1F9E89; --neg:#E4572E; --gold:#D9A441; --avgred:#B5534B
  }
  *{ box-sizing:border-box; margin:0; padding:0; }
  html,body{ background:var(--bg); }
  body{ font-family:-apple-system,BlinkMacSystemFont,'Apple SD Gothic Neo','Malgun Gothic',sans-serif; color:var(--text); padding:28px 32px 60px; max-width:1440px; margin:0 auto; }
  .header{ display:flex; justify-content:space-between; align-items:flex-start; padding-bottom:18px; margin-bottom:14px; border-bottom:2px solid var(--dark); flex-wrap:wrap; gap:16px; }
  .titlewrap{ display:flex; align-items:flex-start; }
  .titlewrap .tab{ width:6px; height:36px; background:var(--c1); border-radius:1px; margin-right:12px; }
  .titlewrap h1{ font-size:24px; font-weight:800; color:var(--dark); letter-spacing:-0.4px; }
  .titlewrap .sub{ font-size:12.5px; color:var(--sub); margin-top:5px; }
  .meta{ text-align:right; font-size:11.5px; color:var(--mute); }

  .data-bar{ background:var(--card); border:1px solid var(--border); border-radius:10px; padding:14px 18px; margin-bottom:12px; display:flex; align-items:center; gap:18px; flex-wrap:wrap; }
  .data-bar .grp{ display:flex; align-items:center; gap:8px; }
  .req{ font-size:10px; font-weight:800; padding:2px 6px; border-radius:6px; }
  .req.must{ background:#FDEDE8; color:#C6431E; }
  .data-bar label.upbtn{ font-size:12px; font-weight:700; color:#fff; background:var(--c1); padding:7px 14px; border-radius:8px; cursor:pointer; white-space:nowrap; }
  .data-bar input[type=file]{ display:none; }
  .data-bar .fname{ font-size:11.5px; color:var(--sub); max-width:220px; overflow:hidden; text-overflow:ellipsis; white-space:nowrap; }
  .status{ font-size:11px; padding:3px 9px; border-radius:10px; font-weight:700; }
  .status.wait{ background:#F0F2F5; color:var(--mute); }
  .status.ok{ background:#E6F7F2; color:#16876F; }
  .data-bar .divider{ width:1px; height:26px; background:var(--border); }
  .upload-err{ background:#FDEDE8; color:#C6431E; border:1px solid #F6C9BA; border-radius:8px; padding:10px 14px; margin-bottom:14px; font-size:12px; font-weight:600; display:none; }

  .part-banner{ background:var(--c4); border-left:5px solid var(--c2); border-radius:6px; padding:12px 16px; margin-bottom:14px; }
  .part-banner .t{ font-size:14.5px; font-weight:800; color:var(--dark); }
  .part-banner .s{ font-size:11.5px; color:var(--sub); margin-top:2px; }

  .kpi-row{ display:flex; gap:14px; margin-bottom:18px; flex-wrap:wrap; }
  .kpi{ flex:1; min-width:200px; background:var(--card); border:1px solid var(--border); border-radius:10px; padding:16px 20px; }
  .kpi .label{ font-size:11.5px; color:var(--sub); font-weight:600; }
  .kpi .value{ font-size:26px; font-weight:800; margin-top:6px; }
  .kpi .chip{ display:inline-block; font-size:11.5px; font-weight:700; margin-top:7px; padding:2px 8px; border-radius:11px; }
  .chip.up{ background:#E6F7F2; color:#16876F; }
  .chip.neutral{ background:#F0F2F5; color:var(--mute); }

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
  .track-mark{ position:absolute; top:-5px; bottom:-5px; width:0; border-left:2px dashed var(--gold); z-index:2; }
  .track-mark-legend{ display:flex; align-items:center; gap:6px; font-size:11px; font-weight:700; color:var(--gold); margin-bottom:10px; }
  .track-mark-swatch{ width:10px; height:2px; background:var(--gold); display:inline-block; }

  .quad-note{ font-size:11px; color:var(--mute); margin-top:10px; line-height:1.6; }

  table.tbl{ width:100%; border-collapse:collapse; font-size:12px; }
  table.tbl th{ text-align:center; color:var(--mute); font-weight:700; font-size:10.5px; padding:6px 8px; border-bottom:1px solid var(--border); background:var(--bg); cursor:pointer; user-select:none; }
  table.tbl th.lbl{ text-align:left; }
  table.tbl td{ padding:7px 8px; border-bottom:1px solid #F0F2F5; color:#344054; text-align:center; }
  table.tbl td.lbl{ text-align:left; font-weight:600; }

  .table-toolbar{ display:flex; gap:10px; margin-bottom:12px; flex-wrap:wrap; }
  .table-toolbar select, .table-toolbar input{ font-family:inherit; font-size:12px; border:1px solid var(--border); border-radius:8px; padding:7px 10px; color:var(--text); background:#fff; }
  .table-toolbar input{ flex:1; min-width:140px; }
  .table-scroll{ max-height:420px; overflow-y:auto; }

  @media (max-width:900px){ .w-half{ width:100%; } }
</style>
</head>
<body>

  <div class="header">
    <div class="titlewrap"><span class="tab"></span><div><h1>우주사업부 근무시간 분석 대시보드</h1><div class="sub">HR운영팀 · 우주사업부HRBP</div></div></div>
    <div class="meta" id="metaInfo">데이터 업로드 대기중</div>
  </div>

  <div class="data-bar">
    <div class="grp"><span class="req must">필수</span><label class="upbtn" for="rawFile">근무시간 로우데이터(xlsx) 업로드</label>
      <input type="file" id="rawFile" accept=".xlsx,.xls" onchange="onRawFile(this.files[0])">
      <span class="fname" id="rawFname">미선택</span><span class="status wait" id="rawStatus">대기중</span></div>
  </div>
  <div class="upload-err" id="uploadErrorBanner"></div>

  <div class="part-banner"><div class="t">2026년 7월 근무시간 분석</div></div>
  <div class="kpi-row">
    <div class="kpi"><div class="label">분석 대상 인원</div><div class="value" id="kpiCount">-</div><span class="chip neutral" id="kpiCountChip">로우데이터 업로드 대기중</span></div>
    <div class="kpi"><div class="label">부서 수</div><div class="value" id="kpiDeptCount">-</div><span class="chip neutral" id="kpiDeptChip">부서명 기준</span></div>
    <div class="kpi"><div class="label">전체 평균 근무시간</div><div class="value" id="kpiAvg">-</div><span class="chip neutral" id="kpiAvgChip">주 기준 · 시간</span></div>
  </div>

  <div class="grid">
    <div class="card w-full">
      <div class="card-title"><span class="tab"></span><h2>부서별 평균 근무시간</h2><span class="note" id="deptBarNote">개인 월평균 근무시간의 부서 평균</span></div>
      <div id="deptBarContent" class="card-body"><div class="empty-state">로우데이터를 업로드하면 표시됩니다</div></div>
    </div>

    <div class="card w-full">
      <div class="card-title"><span class="tab" style="background:#7C5FD1"></span><h2>근무시간 4사분면</h2><span class="note">X: 부서 평균 근무시간 · Y: 부서 내 표준편차</span></div>
      <div id="quadContent" class="card-body"><div class="empty-state">로우데이터를 업로드하면 표시됩니다</div></div>
      <div class="quad-note">점선은 전체 부서 평균 기준입니다. 우측 상단(1사분면)일수록 해당 부서의 평균 근무시간이 길고, 동시에 부서 내 인원들 간 근무시간 편차도 크다는 뜻입니다 — 즉 그 부서 안에서 특정 인원에게 업무가 몰렸을 가능성을 의미하며, "어떤 부서는 많이 일하고 어떤 부서는 적게 일한다"는 부서 간 비교와는 다른 해석입니다.</div>
    </div>

    <div class="card w-full">
      <div class="card-title"><span class="tab"></span><h2>개인별 근무시간</h2><span class="note" id="personalNote">월평균 근무시간(시간)</span></div>
      <div class="table-toolbar">
        <select id="deptFilter"><option value="">전체 부서</option></select>
        <input id="searchBox" placeholder="이름 검색">
      </div>
      <div class="table-scroll">
        <table class="tbl" id="personalTable">
          <thead>
            <tr>
              <th class="lbl" data-key="사번">사번</th>
              <th class="lbl" data-key="이름">이름</th>
              <th class="lbl" data-key="부서명">부서명</th>
              <th data-key="월평균근무시간">월평균근무시간(시간)</th>
            </tr>
          </thead>
          <tbody id="personalTbody"><tr><td colspan="4" class="empty-state">로우데이터를 업로드하면 표시됩니다</td></tr></tbody>
        </table>
      </div>
    </div>
  </div>

<script>
/* ===== 0. 설정 ===== */
const EXCLUDE_구분 = ['근태예외자'];
const DEPT_REMAP = { '제조팀':'제주우주센터', '체계기술2팀':'제주우주센터' };
const EXCLUDE_직급 = ['상무', '전무', '부사장'];
const MIN_HOURS = 40;
const REQUIRED_COLUMNS = ['구분','년월','주차','사번','이름','부서명','주확정근무시간'];

const state = { personal: [], deptStats: [], avgX: 0, avgY: 0 };

/* ===== 1. 업로드 처리 ===== */
function showUploadError(msg){ const b=document.getElementById('uploadErrorBanner'); b.textContent=msg; b.style.display='block'; }
function clearUploadError(){ const b=document.getElementById('uploadErrorBanner'); b.style.display='none'; b.textContent=''; }
function resetFileStatus(){ const s=document.getElementById('rawStatus'); s.textContent='대기중'; s.classList.remove('ok'); s.classList.add('wait'); }

function onRawFile(file){
  if(!file) return;
  document.getElementById('rawFname').textContent = file.name;
  if(!/\.(xlsx|xls)$/i.test(file.name)){ showUploadError('엑셀 파일(.xlsx, .xls)만 업로드할 수 있습니다.'); resetFileStatus(); return; }
  const reader = new FileReader();
  reader.onerror = () => { showUploadError('파일을 읽는 중 오류가 발생했습니다.'); resetFileStatus(); };
  reader.onload = e => {
    try{
      const wb = XLSX.read(new Uint8Array(e.target.result), {type:'array'});
      const sheet = wb.Sheets[wb.SheetNames[0]];
      const rows = XLSX.utils.sheet_to_json(sheet, {defval:''});
      if(rows.length===0){ showUploadError('데이터가 없습니다.'); resetFileStatus(); return; }
      const missing = REQUIRED_COLUMNS.filter(c=>!Object.keys(rows[0]).includes(c));
      if(missing.length){ showUploadError('필수 컬럼이 없습니다: '+missing.join(', ')); resetFileStatus(); return; }
      clearUploadError();
      const s=document.getElementById('rawStatus'); s.textContent=rows.length+'행 로드됨'; s.classList.remove('wait'); s.classList.add('ok');
      processData(rows);
    }catch(err){ showUploadError('처리 중 오류: '+err.message); resetFileStatus(); }
  };
  reader.readAsArrayBuffer(file);
}

/* ===== 2. 데이터 처리 (전처리 -> 개인별 -> 부서별) ===== */
function processData(rows){
  const cleaned = rows.filter(r =>
  !EXCLUDE_구분.includes(String(r['구분']).trim()) &&
  !EXCLUDE_직급.includes(String(r['직급']).trim())
);
  cleaned.forEach(r=>{
    const dept = String(r['부서명']).trim();
    r['부서명'] = DEPT_REMAP[dept] || dept;
    const minutes = Number(r['주확정근무시간']) || 0;
    r['__hours'] = Math.round((minutes/60) * 10) / 10;
  });

  // 년월 메타 표시
  const ymSet = [...new Set(cleaned.map(r=>String(r['년월']).trim()).filter(Boolean))];
  document.getElementById('metaInfo').textContent = ymSet.length ? '데이터 기준월: ' + ymSet.join(', ') : '데이터 업로드 완료';

  // 개인별 평균 (사번 기준, 실제 존재하는 주차 수로 평균)
  const personMap = new Map();
  cleaned.forEach(r=>{
    const key = String(r['사번']).trim();
    if(!personMap.has(key)) personMap.set(key, { 사번:key, 이름:r['이름'], 부서명:r['부서명'], sum:0, cnt:0 });
    const p = personMap.get(key);
    p.sum += r['__hours']; p.cnt += 1;
  });
  const personal = [...personMap.values()].map(p => ({
    사번:p.사번, 이름:p.이름, 부서명:p.부서명,
    월평균근무시간: Math.round((p.sum/p.cnt) * 10) / 10
  }));

  // 부서별 통계 (평균, 표준편차)
  const deptMap = new Map();
  personal.forEach(p=>{
    if(!deptMap.has(p.부서명)) deptMap.set(p.부서명, []);
    deptMap.get(p.부서명).push(p.월평균근무시간);
  });
  const deptStats = [...deptMap.entries()].map(([dept, values])=>{
    const n = values.length;
    const mean = values.reduce((a,b)=>a+b,0) / n;
    let std = 0;
    if(n > 1){
      const variance = values.reduce((a,v)=>a + Math.pow(v-mean,2), 0) / (n-1);
      std = Math.sqrt(variance);
    }
    return { 부서명:dept, 부서평균:Math.round(mean*10)/10, 부서표준편차:Math.round(std*100)/100, 인원수:n };
  }).sort((a,b)=>b.부서평균 - a.부서평균);

  state.personal = personal;
  state.deptStats = deptStats;
  state.avgX = deptStats.reduce((a,d)=>a+d.부서평균,0) / (deptStats.length||1);
  state.avgY = deptStats.reduce((a,d)=>a+d.부서표준편차,0) / (deptStats.length||1);

  renderKPI();
  renderDeptBar();
  renderQuadrant();
  renderPersonalTable();
}

/* ===== 3. KPI ===== */
function renderKPI(){
  document.getElementById('kpiCount').textContent = state.personal.length + '명';
  document.getElementById('kpiCountChip').textContent = '근태예외자 제외';
  document.getElementById('kpiCountChip').className = 'chip up';

  document.getElementById('kpiDeptCount').textContent = state.deptStats.length + '개';
  document.getElementById('kpiDeptChip').textContent = '제주우주센터 통합 반영';
  document.getElementById('kpiDeptChip').className = 'chip up';

  document.getElementById('kpiAvg').textContent = state.avgX.toFixed(1) + '시간';
  document.getElementById('kpiAvgChip').textContent = '부서 평균의 평균';
  document.getElementById('kpiAvgChip').className = 'chip neutral';
}

/* ===== 4. 부서별 평균 근무시간 막대 ===== */
function renderDeptBar(){
  const max = Math.max(1, ...state.deptStats.map(d=>d.부서평균));
  const markPct = Math.min(100, Math.max(0, (state.avgX/max)*100));
  const legend = `<div class="track-mark-legend"><span class="track-mark-swatch"></span>전체 평균 ${state.avgX.toFixed(1)}시간</div>`;
  const rows = state.deptStats.map(d=>{
    const pct = Math.max(2, Math.round(d.부서평균/max*100));
    return `<div class="barrow"><div class="name">${d.부서명}</div><div class="track"><div class="track-mark" style="left:${markPct}%"></div><div class="fill" style="width:${pct}%"></div></div><div class="num">${d.부서평균}시간</div></div>`;
  }).join('');
  document.getElementById('deptBarContent').innerHTML = legend + rows;
}

/* ===== 5. 4사분면 (SVG) ===== */
function renderQuadrant(){
  const data = state.deptStats;
  if(!data.length){ document.getElementById('quadContent').innerHTML = '<div class="empty-state">표시할 데이터가 없습니다</div>'; return; }

  const W = 900, H = 460, pad = 60;
  const xs = data.map(d=>d.부서평균), ys = data.map(d=>d.부서표준편차);
  const xMin = Math.min(...xs, state.avgX) , xMax = Math.max(...xs, state.avgX);
  const yMin = Math.min(...ys, state.avgY), yMax = Math.max(...ys, state.avgY);
  const xPad = (xMax-xMin || 1) * 0.15, yPad = (yMax-yMin || 1) * 0.2;
  const x0 = xMin - xPad, x1 = xMax + xPad, y0 = yMin - yPad, y1 = yMax + yPad;

  const sx = v => pad + (v-x0)/(x1-x0) * (W - pad*2);
  const sy = v => (H - pad) - (v-y0)/(y1-y0) * (H - pad*2);

  const avgXpix = sx(state.avgX), avgYpix = sy(state.avgY);

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
    return `<g>
      <circle cx="${cx}" cy="${cy}" r="6" fill="#35578C" stroke="#fff" stroke-width="1.5"/>
      <text x="${cx}" y="${cy-11}" text-anchor="middle" font-size="11" font-weight="700" fill="#1E3358">${d.부서명}</text>
    </g>`;
  }).join('');

  document.getElementById('quadContent').innerHTML =
    `<svg width="100%" viewBox="0 0 ${W} ${H}" style="overflow:visible;">${axisLines}${gridLines}${points}${axisLabels}</svg>`;
}

/* ===== 6. 개인별 테이블 ===== */
let sortKey = '월평균근무시간', sortAsc = false;

function renderPersonalTable(){
  const deptFilter = document.getElementById('deptFilter');
  const depts = [...new Set(state.personal.map(p=>p.부서명))].sort();
  deptFilter.innerHTML = '<option value="">전체 부서</option>' + depts.map(d=>`<option value="${d}">${d}</option>`).join('');
  document.getElementById('personalNote').textContent = `총 ${state.personal.length}명`;
  renderPersonalRows();
}

function renderPersonalRows(){
  const dept = document.getElementById('deptFilter').value;
  const q = document.getElementById('searchBox').value.trim();
  let rows = state.personal.filter(p =>
    (!dept || p.부서명 === dept) && (!q || String(p.이름).includes(q))
  );
  rows.sort((a,b)=>{
    const av=a[sortKey], bv=b[sortKey];
    const cmp = typeof av === 'number' ? av-bv : String(av).localeCompare(String(bv));
    return sortAsc ? cmp : -cmp;
  });
  const tbody = document.getElementById('personalTbody');
  if(!rows.length){ tbody.innerHTML = '<tr><td colspan="4" class="empty-state">조건에 맞는 인원이 없습니다</td></tr>'; return; }
  tbody.innerHTML = rows.map(p => `
    <tr><td class="lbl">${p.사번}</td><td class="lbl">${p.이름}</td><td class="lbl">${p.부서명}</td><td>${p.월평균근무시간}</td></tr>
  `).join('');
}

document.querySelectorAll('#personalTable th').forEach(th=>{
  th.addEventListener('click', ()=>{
    const key = th.dataset.key;
    sortAsc = (sortKey === key) ? !sortAsc : false;
    sortKey = key;
    renderPersonalRows();
  });
});
document.getElementById('deptFilter').addEventListener('change', renderPersonalRows);
document.getElementById('searchBox').addEventListener('input', renderPersonalRows);
</script>
</body>
</html>
