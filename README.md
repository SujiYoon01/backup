<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>우주사업부 근무실적 현황</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<style>
  :root{
    --c1:#35578C; --c2:#6C8FC4; --c3:#A9C3E6; --c4:#E3EDF9;
    --dark:#1E3358; --bg:#F7F9FC; --card:#FFFFFF; --border:#E4E7EC;
    --text:#101828; --sub:#667085; --mute:#98A2B3; --pos:#1F9E89; --neg:#E4572E; --gold:#D9A441;
  }
  *{ box-sizing:border-box; margin:0; padding:0; }
  html,body{ background:var(--bg); }
  body{ font-family:-apple-system,BlinkMacSystemFont,'Apple SD Gothic Neo','Malgun Gothic',sans-serif; color:var(--text); padding:28px 32px 60px; max-width:1280px; margin:0 auto; }

  .header{ display:flex; justify-content:space-between; align-items:flex-start; flex-wrap:wrap; gap:16px; margin-bottom:14px; }
  .titlewrap{ display:flex; align-items:flex-start; }
  .titlewrap .tab{ width:6px; height:36px; background:var(--c1); border-radius:1px; margin-right:12px; }
  .titlewrap h1{ font-size:24px; font-weight:800; color:var(--dark); letter-spacing:-0.4px; }
  .titlewrap .sub{ font-size:12.5px; color:var(--sub); margin-top:5px; }

  .header-actions{ display:flex; gap:8px; flex-wrap:wrap; }
  .btn{ font-size:12.5px; font-weight:700; padding:9px 14px; border-radius:8px; cursor:pointer; border:none; white-space:nowrap; }
  .btn-primary{ background:var(--dark); color:#fff; }
  .btn-accent{ background:var(--neg); color:#fff; }
  .btn-outline{ background:#fff; color:var(--dark); border:1px solid var(--border); }

  .filter-bar{ background:var(--card); border:1px solid var(--border); border-radius:10px; padding:12px 16px; margin-bottom:14px; display:flex; align-items:center; gap:10px; flex-wrap:wrap; }
  .filter-bar select{ font-family:inherit; font-size:12.5px; border:1px solid var(--border); border-radius:8px; padding:8px 12px; color:var(--text); background:#fff; min-width:150px; }
  .filter-bar .meta{ margin-left:auto; font-size:11.5px; color:var(--mute); }
  .upload-err{ background:#FDEDE8; color:#C6431E; border:1px solid #F6C9BA; border-radius:8px; padding:10px 14px; margin-bottom:14px; font-size:12px; font-weight:600; display:none; }

  .tabs{ display:flex; gap:6px; margin-bottom:16px; border-bottom:2px solid var(--dark); }
  .tab-btn{ font-size:13px; font-weight:700; color:var(--sub); background:none; border:none; padding:10px 16px; cursor:pointer; border-radius:8px 8px 0 0; }
  .tab-btn.active{ background:var(--dark); color:#fff; }
  .tab-panel{ display:none; }
  .tab-panel.active{ display:block; }

  .card{ background:var(--card); border:1px solid var(--border); border-radius:10px; padding:18px 20px 20px; box-shadow:0 1px 2px rgba(16,24,40,.04); margin-bottom:16px; }
  .card-title{ display:flex; align-items:center; margin-bottom:14px; }
  .card-title .tab{ width:4px; height:15px; border-radius:1px; margin-right:8px; background:var(--c1); }
  .card-title h2{ font-size:14.5px; font-weight:700; color:var(--dark); }
  .empty-state{ font-size:12.5px; color:var(--mute); text-align:center; padding:30px 10px; background:var(--bg); border-radius:8px; }

  .search-row input{ width:100%; font-family:inherit; font-size:13px; border:1px solid var(--border); border-radius:8px; padding:10px 14px; }

  .candidate-list{ margin-top:12px; display:flex; gap:8px; flex-wrap:wrap; }
  .candidate-btn{ font-size:12.5px; font-weight:600; padding:8px 12px; border-radius:8px; border:1px solid var(--c2); background:var(--c4); color:var(--dark); cursor:pointer; }
  .candidate-btn:hover{ background:var(--c3); }

  .person-detail{ margin-top:16px; }
  .p-top{ display:flex; justify-content:space-between; align-items:flex-start; flex-wrap:wrap; gap:16px; margin-bottom:14px; }
  .p-name{ font-size:18px; font-weight:800; color:var(--dark); }
  .p-sub{ font-size:12.5px; color:var(--sub); margin-top:3px; }
  .p-stats{ display:flex; gap:24px; flex-wrap:wrap; }
  .p-stat{ text-align:right; }
  .p-stat .l{ font-size:11px; color:var(--mute); font-weight:600; }
  .p-stat .v{ font-size:18px; font-weight:800; margin-top:2px; }
  .v.up{ color:var(--neg); }
  .v.down{ color:var(--pos); }
  .v.flat{ color:var(--sub); }

  .divider{ border:none; border-top:1px solid var(--border); margin:14px 0; }

  .week-list{ display:flex; gap:10px; flex-wrap:wrap; }
  .week-chip{ background:var(--bg); border:1px solid var(--border); border-radius:8px; padding:8px 14px; font-size:12.5px; }
  .week-chip b{ color:var(--dark); margin-left:6px; }

  @media (max-width:700px){ .filter-bar select{ min-width:120px; } .p-stats{ width:100%; justify-content:space-between; } }
</style>
</head>
<body>

  <div class="header">
    <div class="titlewrap">
      <span class="tab"></span>
      <div><h1>우주사업부 근무실적 현황</h1><div class="sub">HR운영팀 · 우주사업부HRBP</div></div>
    </div>
    <div class="header-actions">
      <label class="btn btn-primary" for="rawFile" style="cursor:pointer;">데이터 불러오기</label>
      <input type="file" id="rawFile" accept=".xlsx,.xls" style="display:none;" onchange="onRawFile(this.files[0])">
      <button class="btn btn-outline" onclick="saveHTML('full')">완성 파일로 저장</button>
      <button class="btn btn-accent" onclick="saveHTML('filtered')">현재 선택 필터 내용 저장</button>
    </div>
  </div>

  <div class="filter-bar">
    <select id="siteFilter" onchange="onFilterChange()"><option value="">사업장 전체</option></select>
    <select id="deptFilter2" onchange="onFilterChange()"><option value="">부서 전체</option></select>
    <span class="meta" id="metaInfo">데이터 업로드 대기중</span>
  </div>
  <div class="upload-err" id="uploadErrorBanner"></div>

  <div class="tabs">
    <button class="tab-btn active" data-tab="status" onclick="switchTab('status')">🏠 근무실적 현황</button>
    <button class="tab-btn" data-tab="quad" onclick="switchTab('quad')">📊 사분면 분포</button>
  </div>

  <div id="tab-status" class="tab-panel active">
    <div class="card">
      <div class="card-title"><span class="tab"></span><h2>인원 검색</h2></div>
      <div class="search-row">
        <input id="personSearch" placeholder="이름을 입력하세요" oninput="onSearchInput(this.value)">
      </div>
      <div id="candidateList" class="candidate-list" style="display:none;"></div>
      <div id="personDetail" class="person-detail" style="display:none;"></div>
      <div id="personEmpty" class="empty-state">이름을 검색하면 개인별 근무실적이 표시됩니다</div>
    </div>

    <!-- 다음 카드는 여기 아래에 추가 예정 -->
  </div>

  <div id="tab-quad" class="tab-panel">
    <div class="empty-state">사분면 분포는 근무실적 현황 완성 후 이어서 제작합니다.</div>
  </div>

<script>
/* ============================================================
   0. 설정 — 회사 상황에 맞게 아래 값만 수정하면 됩니다.
   ============================================================ */
const EXCLUDE_구분 = ['근태예외자'];
const EXCLUDE_직급 = ['상무', '전무', '부사장'];
const DEPT_REMAP = {};              // 예: { '제조팀':'제주우주센터' }
const REQUIRED_COLUMNS = ['구분','년월','주차','사번','이름','직급','사업장','부서명','주확정근무시간'];

/* ============================================================
   1. 상태
   ============================================================ */
let rawRows = [];          // 누적된 원본(가공) 행 데이터, 월별로 계속 쌓임
let selectedSite = '';
let selectedDept = '';

/* ============================================================
   2. 업로드 처리
   ============================================================ */
function showUploadError(msg){ const b=document.getElementById('uploadErrorBanner'); b.textContent=msg; b.style.display='block'; }
function clearUploadError(){ const b=document.getElementById('uploadErrorBanner'); b.style.display='none'; b.textContent=''; }

function onRawFile(file){
  if(!file) return;
  if(!/\.(xlsx|xls)$/i.test(file.name)){ showUploadError('엑셀 파일(.xlsx, .xls)만 업로드할 수 있습니다.'); return; }
  const reader = new FileReader();
  reader.onerror = () => showUploadError('파일을 읽는 중 오류가 발생했습니다.');
  reader.onload = e => {
    try{
      const wb = XLSX.read(new Uint8Array(e.target.result), {type:'array'});
      const sheet = wb.Sheets[wb.SheetNames[0]];
      const raw = XLSX.utils.sheet_to_json(sheet, {header:1, defval:''});
      if(raw.length===0){ showUploadError('데이터가 없습니다.'); return; }

      const normalize = s => String(s).normalize('NFC').trim();
      const reqNorm = REQUIRED_COLUMNS.map(normalize);

      // 상위 10개 행 중 필수 컬럼명이 가장 많이 일치하는 행을 헤더로 판단
      // (제목행/병합셀 등으로 헤더가 1행이 아닌 경우 대비)
      let headerIdx = -1, bestMatch = 0;
      for(let i=0; i<Math.min(10, raw.length); i++){
        const rowNorm = raw[i].map(normalize);
        const matchCount = reqNorm.filter(c => rowNorm.includes(c)).length;
        if(matchCount > bestMatch){ bestMatch = matchCount; headerIdx = i; }
      }

      if(headerIdx === -1 || bestMatch < REQUIRED_COLUMNS.length){
        const detected = headerIdx>=0 ? raw[headerIdx].join(', ') : '헤더를 찾지 못했습니다';
        showUploadError('필수 컬럼을 찾을 수 없습니다. 감지된 헤더: ' + detected);
        return;
      }

      const headers = raw[headerIdx].map(normalize);
      const rows = raw.slice(headerIdx+1)
        .map(r => {
          const obj = {};
          headers.forEach((h,idx) => obj[h] = r[idx] !== undefined ? r[idx] : '');
          return obj;
        })
        .filter(r => Object.values(r).some(v => String(v).trim() !== ''));

      if(rows.length===0){ showUploadError('헤더 아래에 데이터가 없습니다.'); return; }

      clearUploadError();
      mergeRows(rows);
      processAll();
    }catch(err){ showUploadError('처리 중 오류: '+err.message); }
  };
  reader.readAsArrayBuffer(file);
}

// 같은 (년월,사번,주차) 조합은 새 업로드로 덮어쓰기 — 같은 달 재업로드 시 최신 데이터로 갱신
function mergeRows(newRows){
  const cleaned = newRows
    .filter(r => !EXCLUDE_구분.includes(String(r['구분']).trim()) && !EXCLUDE_직급.includes(String(r['직급']).trim()))
    .map(r => {
      const dept = String(r['부서명']).trim();
      return {
        ...r,
        년월: String(r['년월']).trim(),
        주차: String(r['주차']).trim(),
        사번: String(r['사번']).trim(),
        이름: String(r['이름']).trim(),
        직급: String(r['직급']).trim(),
        사업장: String(r['사업장']).trim(),
        부서명: DEPT_REMAP[dept] || dept,
        __hours: Math.round((Number(r['주확정근무시간'])||0) / 60 * 10) / 10
      };
    });

  const keyOf = r => `${r.년월}__${r.사번}__${r.주차}`;
  const newKeys = new Set(cleaned.map(keyOf));
  rawRows = rawRows.filter(r => !newKeys.has(keyOf(r)));
  rawRows = rawRows.concat(cleaned);
}

/* ============================================================
   3. 필터 옵션 & 메타
   ============================================================ */
function latestYM(){
  const yms = [...new Set(rawRows.map(r=>r.년월))].sort();
  return yms.length ? yms[yms.length-1] : null;
}
function formatYM(ym){
  if(!ym) return '';
  const digits = ym.replace(/[^0-9]/g,'');
  if(digits.length>=6) return digits.slice(4,6)+'월';
  return ym;
}

function populateFilters(){
  const siteSel = document.getElementById('siteFilter');
  const deptSel = document.getElementById('deptFilter2');
  const prevSite = siteSel.value, prevDept = deptSel.value;

  const sites = [...new Set(rawRows.map(r=>r.사업장))].filter(Boolean).sort();
  siteSel.innerHTML = '<option value="">사업장 전체</option>' + sites.map(s=>`<option value="${s}">${s}</option>`).join('');
  siteSel.value = sites.includes(prevSite) ? prevSite : '';

  const scoped = siteSel.value ? rawRows.filter(r=>r.사업장===siteSel.value) : rawRows;
  const depts = [...new Set(scoped.map(r=>r.부서명))].filter(Boolean).sort();
  deptSel.innerHTML = '<option value="">부서 전체</option>' + depts.map(d=>`<option value="${d}">${d}</option>`).join('');
  deptSel.value = depts.includes(prevDept) ? prevDept : '';

  selectedSite = siteSel.value;
  selectedDept = deptSel.value;
}

function onFilterChange(){
  selectedSite = document.getElementById('siteFilter').value;
  selectedDept = document.getElementById('deptFilter2').value;
  populateFilters(); // 사업장 변경 시 부서 목록 재구성
  document.getElementById('siteFilter').value = selectedSite;
  renderMeta();
  // 필터가 바뀌면 검색결과 초기화 (다른 부서 사람이 노출되지 않도록)
  document.getElementById('personSearch').value = '';
  resetPersonView();
}

function renderMeta(){
  const ym = latestYM();
  const meta = document.getElementById('metaInfo');
  if(!ym){ meta.textContent = '데이터 업로드 대기중'; return; }
  const allYm = [...new Set(rawRows.map(r=>r.년월))].sort();
  meta.textContent = `로드된 월: ${allYm.join(', ')} · 최신월 기준 ${formatYM(ym)} 데이터 표시`;
}

/* ============================================================
   4. 인원 검색
   ============================================================ */
// 특정 년월의 인원별 월평균 근무시간 (필터 스코프 적용)
function personalMonthlyAverages(ym){
  const scoped = rawRows.filter(r =>
    r.년월===ym &&
    (!selectedSite || r.사업장===selectedSite) &&
    (!selectedDept || r.부서명===selectedDept)
  );
  const map = new Map();
  scoped.forEach(r=>{
    if(!map.has(r.사번)) map.set(r.사번, { 사번:r.사번, 이름:r.이름, 직급:r.직급, 부서명:r.부서명, 사업장:r.사업장, sum:0, cnt:0 });
    const p = map.get(r.사번);
    p.sum += r.__hours; p.cnt += 1;
  });
  map.forEach(p => p.avg = Math.round((p.sum/p.cnt)*10)/10);
  return map;
}

function deptAvg(monthly, dept){
  const vals = [...monthly.values()].filter(p=>p.부서명===dept).map(p=>p.avg);
  if(!vals.length) return null;
  return Math.round((vals.reduce((a,b)=>a+b,0)/vals.length)*10)/10;
}
function buAvg(monthly){
  const vals = [...monthly.values()].map(p=>p.avg);
  if(!vals.length) return null;
  return Math.round((vals.reduce((a,b)=>a+b,0)/vals.length)*10)/10;
}

function resetPersonView(){
  document.getElementById('candidateList').style.display = 'none';
  document.getElementById('candidateList').innerHTML = '';
  document.getElementById('personDetail').style.display = 'none';
  document.getElementById('personEmpty').style.display = 'block';
}

function onSearchInput(q){
  q = q.trim();
  const ym = latestYM();
  if(!q || !ym){ resetPersonView(); return; }

  const monthly = personalMonthlyAverages(ym);
  const matches = [...monthly.values()].filter(p => p.이름.includes(q));

  const candWrap = document.getElementById('candidateList');
  const detailWrap = document.getElementById('personDetail');
  const emptyWrap = document.getElementById('personEmpty');

  if(matches.length===0){
    candWrap.style.display='none'; detailWrap.style.display='none';
    emptyWrap.style.display='block'; emptyWrap.textContent = '검색 결과가 없습니다.';
    return;
  }
  emptyWrap.style.display='none';

  if(matches.length===1){
    candWrap.style.display='none';
    renderPersonDetail(matches[0].사번, ym);
    return;
  }

  // 동명이인 처리
  detailWrap.style.display='none';
  candWrap.style.display='flex';
  candWrap.innerHTML = matches.map(p =>
    `<button class="candidate-btn" onclick="renderPersonDetail('${p.사번}','${ym}')">${p.이름} · ${p.부서명} · ${p.직급}</button>`
  ).join('');
}

function renderPersonDetail(empId, ym){
  document.getElementById('candidateList').style.display='none';
  const monthly = personalMonthlyAverages(ym);
  const person = monthly.get(empId);
  if(!person) return;

  const dAvg = deptAvg(monthly, person.부서명);
  const bAvg = buAvg(monthly);
  const diffDept = dAvg!==null ? Math.round((person.avg - dAvg)*10)/10 : null;
  const diffBU = bAvg!==null ? Math.round((person.avg - bAvg)*10)/10 : null;

  const diffClass = v => v>0 ? 'up' : (v<0 ? 'down' : 'flat');
  const diffText = v => v===null ? '-' : (v>0 ? `+${v}시간` : `${v}시간`);

  const weeks = rawRows
    .filter(r => r.사번===empId && r.년월===ym)
    .sort((a,b)=> a.주차.localeCompare(b.주차, undefined, {numeric:true}));
  const monthLabel = formatYM(ym).replace('월','');
  const weekChips = weeks.map(w =>
    `<div class="week-chip">${monthLabel}/${w.주차}<b>${w.__hours}시간</b></div>`
  ).join('');

  document.getElementById('personDetail').style.display='block';
  document.getElementById('personDetail').innerHTML = `
    <div class="p-top">
      <div>
        <div class="p-name">${person.이름}</div>
        <div class="p-sub">${person.직급} · ${person.부서명} · ${person.사업장}</div>
      </div>
      <div class="p-stats">
        <div class="p-stat"><div class="l">${formatYM(ym)} 평균 근로시간</div><div class="v flat">${person.avg}시간</div></div>
        <div class="p-stat"><div class="l">부서 평균 대비</div><div class="v ${diffClass(diffDept)}">${diffText(diffDept)}</div></div>
        <div class="p-stat"><div class="l">사업부 평균 대비</div><div class="v ${diffClass(diffBU)}">${diffText(diffBU)}</div></div>
      </div>
    </div>
    <hr class="divider">
    <div class="week-list">${weekChips || '<span class="empty-state" style="padding:8px;">주차별 데이터가 없습니다</span>'}</div>
  `;
}

/* ============================================================
   5. 탭 전환
   ============================================================ */
function switchTab(name){
  document.querySelectorAll('.tab-btn').forEach(b => b.classList.toggle('active', b.dataset.tab===name));
  document.querySelectorAll('.tab-panel').forEach(p => p.classList.remove('active'));
  document.getElementById('tab-'+name).classList.add('active');
}

/* ============================================================
   6. 전체 갱신
   ============================================================ */
function processAll(){
  populateFilters();
  renderMeta();
  document.getElementById('personSearch').value = '';
  resetPersonView();
}

/* ============================================================
   7. 파일로 저장 (완성 파일 / 필터 적용본)
   ============================================================ */
function downloadHTMLString(htmlStr, filename){
  const blob = new Blob([htmlStr], {type:'text/html'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url; a.download = filename;
  document.body.appendChild(a); a.click(); document.body.removeChild(a);
  URL.revokeObjectURL(url);
}

function saveHTML(mode){
  const ym = latestYM();
  let exportRows = rawRows;
  let lockSite = '', lockDept = '';

  if(mode === 'filtered'){
    lockSite = document.getElementById('siteFilter').value;
    lockDept = document.getElementById('deptFilter2').value;
    if(!lockSite && !lockDept){
      alert('부서장 배포용 파일은 사업장 또는 부서 필터를 먼저 선택한 후 저장해주세요.');
      return;
    }
    exportRows = rawRows.filter(r => (!lockSite || r.사업장===lockSite) && (!lockDept || r.부서명===lockDept));
  }

  const clone = document.documentElement.cloneNode(true);

  // 기존 임베드 데이터 스크립트 제거 후 새로 삽입
  const old = clone.querySelector('#embeddedData');
  if(old) old.remove();
  const dataScript = document.createElement('script');
  dataScript.id = 'embeddedData';
  dataScript.type = 'application/json';
  dataScript.textContent = JSON.stringify(exportRows);
  clone.querySelector('head').appendChild(dataScript);

  // 업로드 UI는 완성/배포본에서는 숨김 처리 (재가공 방지)
  const uploadLabel = clone.querySelector('label[for="rawFile"]');
  if(uploadLabel) uploadLabel.style.display = 'none';
  const uploadInput = clone.querySelector('#rawFile');
  if(uploadInput) uploadInput.style.display = 'none';

  if(mode === 'filtered'){
    const siteSel = clone.querySelector('#siteFilter');
    const deptSel = clone.querySelector('#deptFilter2');
    siteSel.innerHTML = `<option value="${lockSite}">${lockSite || '사업장 전체'}</option>`;
    siteSel.value = lockSite; siteSel.disabled = true;
    deptSel.innerHTML = `<option value="${lockDept}">${lockDept || '부서 전체'}</option>`;
    deptSel.value = lockDept; deptSel.disabled = true;
    // 저장/필터저장 버튼도 배포본에서는 제거 (재유출 방지)
    ['btnSaveFull','btnSaveFiltered'].forEach(()=>{});
    clone.querySelectorAll('.header-actions .btn-outline, .header-actions .btn-accent').forEach(b=>b.remove());
  }

  const htmlStr = '<!DOCTYPE html>\n' + clone.outerHTML;
  const suffix = mode==='filtered' ? (lockDept||lockSite) : '전체';
  downloadHTMLString(htmlStr, `우주사업부_근무실적_${suffix}_${ym||'데이터없음'}.html`);
}

/* ============================================================
   8. 초기화 — 배포된 파일에 데이터가 임베드되어 있으면 자동 로드
   ============================================================ */
(function init(){
  const embedded = document.getElementById('embeddedData');
  if(embedded){
    try{
      rawRows = JSON.parse(embedded.textContent);
      processAll();
    }catch(e){ /* 무시 */ }
  }
})();
</script>
</body>
</html>
