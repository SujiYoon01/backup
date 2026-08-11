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
