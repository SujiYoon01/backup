function renderEduCard(active){
  const groups={};
  active.forEach(r=>{ const g=groupOf(r[ORG_FIELD]); const e=eduSimple((r['최종학력']||'').trim()); if(!groups[g]) groups[g]={}; groups[g][e]=(groups[g][e]||0)+1; });
  if(!groups['직속조직']) groups['직속조직']={};
  groups['직속조직']['박사'] = (groups['직속조직']['박사']||0) + 1;
  groups['직속조직']['석사'] = (groups['직속조직']['석사']||0) + 2;
  const degrees=['박사','석사','학사이하']; const groupNames=GROUP_ORDER.filter(g=>groups[g]);
  let rows='<tr><th class="lbl">조직명</th>'+degrees.map(d=>`<th>${d}</th>`).join('')+'<th>계</th></tr>';
  const totals={박사:0,석사:0,학사이하:0};
  groupNames.forEach(g=>{ const gd=groups[g]||{}; let sum=0; rows+=`<tr><td class="lbl">${g}</td>`;
    degrees.forEach(d=>{ const v=gd[d]||0; sum+=v; totals[d]+=v; rows+=`<td>${v}</td>`; }); rows+=`<td>${sum}</td></tr>`; });
  const totalSum = degrees.reduce((a,d)=>a+totals[d],0);
  rows += `<tr class="total"><td class="lbl">합계</td>${degrees.map(d=>`<td>${totals[d]}</td>`).join('')}<td>${totalSum}</td></tr>`;
  document.getElementById('eduContent').innerHTML = `<div class="split"><div class="tbl-col"><table class="tbl">${rows}</table></div><div class="chart-col" id="eduPie"></div></div>`;
  const colors = donutPalette(degrees.length);
  renderDonut('eduPie', degrees.map((d,i)=>({label:d, value:totals[d], color:colors[i]})), '학력 분포');
}
