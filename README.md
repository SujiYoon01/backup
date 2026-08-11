function renderLevelPyramidCard(containerId, rows, order, unitLabel, interactive=true){
  if(!rows.length){ document.getElementById(containerId).innerHTML = '<div class="empty-state">표시할 데이터가 없습니다</div>'; return; }
  const colors = levelGradient(order.length);
  __pyramidData[containerId] = { rows, order, colors, unitLabel };
  const legend = `<div class="pie-legend" style="justify-content:flex-start;margin-bottom:10px;">` + order.map((lv,i)=>`<span class="pie-legend-item"><i style="background:${colors[i]}"></i>${LEVEL_LABELS[lv]}</span>`).join('') + `</div>`;
  const body = rows.map(r=>{
    const segs = order.map((lv,i)=>{ const v=r.counts[lv]||0; const pct=r.total? v/r.total*100:0; if(pct<=0) return ''; return `<div style="width:${pct}%;background:${colors[i]};" title="${LEVEL_LABELS[lv]}: ${v}명 (${Math.round(pct)}%)"></div>`; }).join('');
    const nameStyle = r.label==='우주' ? 'color:var(--gold);font-weight:800;' : '';
    const clickAttr = interactive ? ` style="cursor:pointer;" onclick="togglePyramidDetail('${containerId}','${esc(r.label)}')"` : '';
    return `<div class="barrow"${clickAttr}><div class="name" style="${nameStyle}">${r.label}</div><div class="stack-track">${segs}</div><div class="num">${r.total}명</div></div>`;
  }).join('');
  if(!interactive){ document.getElementById(containerId).innerHTML = legend + body; return; }
  const placeholder = `<div class="pyramid-placeholder">클릭 시 해당 ${unitLabel} 피라미드 분포 상세 확인 가능</div>`;
  document.getElementById(containerId).innerHTML = legend + body + `<div id="${containerId}-detail">${placeholder}</div>`;
}
