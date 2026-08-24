ƒ renderAllFromCurrent(){
  const spaceAll = state.curr.filter(r=>isSpace(r[ORG_FIELD]));
  const active = spaceAll.filter(r=>r['재직상태']==='재직');

  document.getElementById('kpiTotalNum').textContent = …
