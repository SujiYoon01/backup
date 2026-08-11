function renderJobCard(active){
  const FAMILY_ORDER = ['임원','A','S','R','E','M','T'];
  const FAMILY_LABELS = {'임원':'임원','A':'A직군(경영지원)','S':'S직군(영업)','R':'R직군(연구개발)','E':'E직군(기술)','M':'M직군(제조)','T':'T직군(제조전문)'};
  const counts={};
  active.forEach(r=>{ const {family}=classifyJobCode(r); if(FAMILY_ORDER.includes(family)) counts[family]=(counts[family]||0)+1; });
  const labels = FAMILY_ORDER.filter(f=>counts[f]>0);
  const colors = donutPalette(labels.length);
 renderDonut('jobContent', labels.map((l,i)=>({label:FAMILY_LABELS[l], value:counts[l], color:colors[i]})), '총원(명)', {forceLabels:true, size:460});
  document.getElementById('jobNote').textContent = `직급코드 기준, 총 ${active.length}명`;
}
function renderEduCard(active){
  const EDU_FIXED = {
    '직속조직': {박사:9, 석사:26, 학사이하:65},
    '우주연구소': {박사:32, 석사:116, 학사이하:102}
  };
  const degrees=['박사','석사','학사이하']; const groupNames=GROUP_ORDER;
  let rows='<tr><th class="lbl">조직명</th>'+degrees.map(d=>`<th>${d}</th>`).join('')+'<th>계</th></tr>';
  const totals={박사:0,석사:0,학사이하:0};
  groupNames.forEach(g=>{ const gd=EDU_FIXED[g]||{}; let sum=0; rows+=`<tr><td class="lbl">${g}</td>`;
    degrees.forEach(d=>{ const v=gd[d]||0; sum+=v; totals[d]+=v; rows+=`<td>${v}</td>`; }); rows+=`<td>${sum}</td></tr>`; });
  const totalSum = degrees.reduce((a,d)=>a+totals[d],0);
  rows += `<tr class="total"><td class="lbl">합계</td>${degrees.map(d=>`<td>${totals[d]}</td>`).join('')}<td>${totalSum}</td></tr>`;
  document.getElementById('eduContent').innerHTML = `<div class="split"><div class="tbl-col"><table class="tbl">${rows}</table></div><div class="chart-col" id="eduPie"></div></div>`;
  const colors = donutPalette(degrees.length);
  renderDonut('eduPie', degrees.map((d,i)=>({label:d, value:totals[d], color:colors[i]})), '학력 분포');
}
