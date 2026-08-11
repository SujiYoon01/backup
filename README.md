const __pyramidData = {};
function renderLevelPyramidCard(containerId, rows, order, unitLabel){
  if(!rows.length){ document.getElementById(containerId).innerHTML = '<div class="empty-state">표시할 데이터가 없습니다</div>'; return; }
  const colors = levelGradient(order.length);
  __pyramidData[containerId] = { rows, order, colors, unitLabel };
  const legend = `<div class="pie-legend" style="justify-content:flex-start;margin-bottom:10px;">` + order.map((lv,i)=>`<span class="pie-legend-item"><i style="background:${colors[i]}"></i>${LEVEL_LABELS[lv]}</span>`).join('') + `</div>`;
  const body = rows.map(r=>{
    const segs = order.map((lv,i)=>{ const v=r.counts[lv]||0; const pct=r.total? v/r.total*100:0; if(pct<=0) return ''; return `<div style="width:${pct}%;background:${colors[i]};" title="${LEVEL_LABELS[lv]}: ${v}명 (${Math.round(pct)}%)"></div>`; }).join('');
    return `<div class="barrow" style="cursor:pointer;" onclick="togglePyramidDetail('${containerId}','${esc(r.label)}')"><div class="name">${r.label}</div><div class="stack-track">${segs}</div><div class="num">${r.total}명</div></div>`;
  }).join('');
  const placeholder = `<div class="pyramid-placeholder">클릭 시 해당 ${unitLabel} 피라미드 분포 상세 확인 가능</div>`;
  document.getElementById(containerId).innerHTML = legend + body + `<div id="${containerId}-detail">${placeholder}</div>`;
}
function togglePyramidDetail(containerId, label){
  const detailEl = document.getElementById(containerId+'-detail');
  if(!detailEl) return;
  const data = __pyramidData[containerId];
  const placeholder = `<div class="pyramid-placeholder">클릭 시 해당 ${data.unitLabel} 피라미드 분포 상세 확인 가능</div>`;
  if(detailEl.dataset.label === label){ detailEl.innerHTML = placeholder; detailEl.dataset.label=''; return; }
  const row = data.rows.find(r=>r.label===label);
  if(!row) return;
  const maxCount = Math.max(...data.order.map(lv=>row.counts[lv]||0), 1);
  const rowsHtml = data.order.map((lv,i)=>{
    const v = row.counts[lv]||0;
    if(v===0) return '';
    const pct = Math.max(v/maxCount*100, 8);
    const textColor = i >= data.order.length*0.55 ? '#1E3358' : '#fff';
    return `<div class="pyramid-row"><div class="pyramid-bar" style="width:${pct}%;background:${data.colors[i]};color:${textColor};cursor:pointer;" title="클릭하면 이름 표시" onclick="togglePyramidNames(event,'${containerId}','${esc(label)}','${lv}')">${LEVEL_LABELS[lv]} ${v}명</div></div>`;
  }).join('');
  detailEl.innerHTML = `<div class="pyramid-block"><div class="pyramid-title">${label} 레벨 피라미드 (총 ${row.total}명)</div>${rowsHtml}</div>`;
  detailEl.dataset.label = label;
}
function togglePyramidNames(e, containerId, label, lv){
  e.stopPropagation();
  const data = __pyramidData[containerId];
  const row = data.rows.find(r=>r.label===label);
  const names = (row.names && row.names[lv]) || [];
  let tip = document.getElementById('pyramidNameTooltip');
  if(!tip){ tip=document.createElement('div'); tip.id='pyramidNameTooltip'; tip.className='flow-tooltip'; document.body.appendChild(tip); }
  const key = containerId+'|'+label+'|'+lv;
  if(tip.style.display==='block' && tip.dataset.key===key){ tip.style.display='none'; delete tip.dataset.key; return; }
  tip.innerHTML = `<div style="font-weight:700;margin-bottom:4px;">${label} · ${LEVEL_LABELS[lv]} (${names.length}명)</div>` + (names.length?names.map(n=>`<div>${n}</div>`).join(''):'<div style="color:#999;">해당 인원 없음</div>');
  tip.dataset.key = key;
  tip.style.left=(e.pageX+12)+'px'; tip.style.top=(e.pageY+12)+'px'; tip.style.display='block';
}
document.addEventListener('click', e=>{ const tip=document.getElementById('pyramidNameTooltip'); if(tip && !e.target.closest('.pyramid-bar')){ tip.style.display='none'; delete tip.dataset.key; } });
