function exportSnapshot(){
  if(!state.curr){ alert('먼저 당월 명부 CSV를 업로드해주세요.'); return; }
  const dateVal = document.getElementById('reportDate').value || new Date().toISOString().slice(0,10);
  const clone = document.documentElement.cloneNode(true);
  ['.data-bar','#uploadErrorBanner','.part-nav'].forEach(sel=>{ const el=clone.querySelector(sel); if(el) el.remove(); });
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
  clone.querySelectorAll('#part1,#part2,#kpiRowPart1,#kpiRowPart2').forEach(el=>{ el.style.display = el.id.includes('2') ? 'none' : (el.id.startsWith('kpi')?'flex':'block'); });
  const html = '<!DOCTYPE html>\n' + clone.outerHTML;
  const blob = new Blob([html], {type:'text/html;charset=utf-8'});
  const url = URL.createObjectURL(blob); const a = document.createElement('a');
  a.href=url; a.download=`우주사업부_인원월보_${dateVal.replace(/-/g,'')}.html`;
  document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
}
