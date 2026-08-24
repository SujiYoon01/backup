function showPart(n){
  document.getElementById('part1').style.display = n===1 ? 'block' : 'none';
  document.getElementById('part2').style.display = n===2 ? 'block' : 'none';
  const kpi1 = document.getElementById('kpiRowPart1'); if(kpi1) kpi1.style.display = n===1 ? 'flex' : 'none';
  const kpi2 = document.getElementById('kpiRowPart2'); if(kpi2) kpi2.style.display = n===2 ? 'flex' : 'none';
  const nav1 = document.getElementById('navPart1'); if(nav1) nav1.classList.toggle('active', n===1);
  const nav2 = document.getElementById('navPart2'); if(nav2) nav2.classList.toggle('active', n===2);
}

function exportSnapshot(){
  if(!state.curr){ alert('먼저 당월 명부 CSV를 업로드해주세요.'); return; }
  const dateVal = document.getElementById('reportDate').value || new Date().toISOString().slice(0,10);
  const clone = document.documentElement.cloneNode(true);
  ['.data-bar','#uploadErrorBanner'].forEach(sel=>{ const el=clone.querySelector(sel); if(el) el.remove(); });
  const metaEl = clone.querySelector('.meta'); if(metaEl) metaEl.textContent = '기준일 ' + dateVal + ' (저장 시점 고정)';
  const dataScript = document.createElement('script');
  dataScript.textContent = 'window.__snapshotCurr = ' + JSON.stringify(state.curr) + ';' +
    'window.__snapshotPrev = ' + JSON.stringify(state.prev) + ';' +
    'window.__snapshotDate = ' + JSON.stringify(dateVal) + ';' +
    'window.addEventListener("DOMContentLoaded", function(){' +
      'document.getElementById("reportDate").value = window.__snapshotDate;' +
      'state.curr = window.__snapshotCurr;' +
      'state.prev = window.__snapshotPrev;' +
      'renderAllFromCurrent();' +
      'renderBUSection();' +
      'if(state.curr && state.prev) renderComparison();' +
    '});';
  clone.querySelector('body').appendChild(dataScript);
  const html = '<!DOCTYPE html>\n' + clone.outerHTML;
  const blob = new Blob([html], {type:'text/html;charset=utf-8'});
  const url = URL.createObjectURL(blob); const a = document.createElement('a');
  a.href=url; a.download=`우주사업부_인원월보_${dateVal.replace(/-/g,'')}.html`;
  document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
}

