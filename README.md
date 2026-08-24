function exportSnapshot(){
  if(!state.curr){ alert('먼저 당월 명부 CSV를 업로드해주세요.'); return; }
  const dateVal = document.getElementById('reportDate').value || new Date().toISOString().slice(0,10);
  const clone = document.documentElement.cloneNode(true);
  ['.data-bar','#uploadErrorBanner'].forEach(sel=>{ const el=clone.querySelector(sel); if(el) el.remove(); });
  const clonedInput = clone.querySelector('#reportDate');
  if(clonedInput) clonedInput.setAttribute('value', dateVal);
  const dataScript = document.createElement('script');
  dataScript.textContent = 'window.__snapshotCurr = ' + JSON.stringify(state.curr) + ';' +
    'window.__snapshotPrev = ' + JSON.stringify(state.prev) + ';' +
    'window.addEventListener("DOMContentLoaded", function(){' +
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
