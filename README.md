const nameStyle = r.label==='우주' ? 'color:var(--gold);font-weight:800;' : '';
return `<div class="barrow" style="cursor:pointer;" onclick="togglePyramidDetail('${containerId}','${esc(r.label)}')"><div class="name" style="${nameStyle}">${r.label}</div><div class="stack-track">${segs}</div><div class="num">${r.total}명</div></div>`;
