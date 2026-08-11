'이동내역 입퇴사' 카드에서 퇴직에 써있는 사람들이 퇴직을 하면 화살표 오른족에 '-'로 뜨는걸 그냥 '퇴직'이라고 표시해.

'이동내역 조직/직급 변동' 중 '직급변동에서 안정민이 '수석연구원'이라고 쓰여있는걸 'R6(수석연구원)'으로 수정. 김수환이 '연구원'이라고 쓰여있는걸 'E3(연구원)'으로 수정.

전배, 사업장이동, 근무지이동에 김정현 사원이 '연구원'이라고 쓰여있는걸 'R3(연구원)'으로 수정.

'직군별 인원현황' 그래프에 글자들 안겹치게 간격을 펼쳐. 

'조직별 학력사항'에 임원 3명도 포함하고, '임원 3명 제외'라는 문구 지워.

'인력증감내역'에서 '유한성, 신재우, 김동한, 김범현, 황명하, 김도연, 한준환, 배준홍, 김성호, 이태윤, 남재영, 박성현, 전예찬, 박서린, 이선영, 윤여원, 김동혁, 심현준, 권세정, 최정렬, 오진우, 장영민'은 '전입'이 아닌 '채용' 막대로 이동시켜. '전입'에는 차세대단말기팀에서 위성탑재체3팀으로 온 김규형, 김동환, 배기형만 남겨. 좌측 하단의 '채용/전입은 입사일 연월 기준 자동 구분됩니다.' 이 문장 지워.

'이동내역·입퇴사', '이동내역·조직/직급 변동', '이동내역·휴직/파견' 이 세개의 워딩에서 '채용·퇴직', '전입(from타사업부)·전출(to타사업부)', '전배·직급변동·사업장이동·근무지이동'으로 세개의 카드로 바꿔. 이번달은 '채용'이 22명으로 좀 많은데 카드가 세로로 너무 늘어지지 않게 해. 

'사업장별 인원현황'에서 각 사업장 조각을 누르면 '용인'에는 '차세대위성체계팀, 위성체계팀, 위성본체팀, 위성탑재체1팀, 위성탑재체2팀, 위성지상체팀, 위성기계팀'이 뜨게 해. '서현;을 누르면 '위성탑재체3팀'이 뜨게 해. '제주' 누르면 '제주우주센터'가 뜨게 해. '서울2' 누르면 '우주사업전략팀, 우주사업단, 솔루션사업팀'이 뜨게 해. 그리고 카드 우측 상단에 '사업장 클릭시 해당 사업장 소속 부서 확인 가능'이런 워딩을 넣고 싶어. 

'부서별 레벨 피라미드'에서 클릭 시 해당 부서 피라미드 상세 보기에서 각 레벨을 클릭하면 그 부서 그 레벨에 해당하는 사람들 이름을 function renderEduCard(active){
  const groups={};
  active.forEach(r=>{ const g=groupOf(r[ORG_FIELD]); const e=eduSimple((r['최종학력']||'').trim()); if(!groups[g]) groups[g]={}; groups[g][e]=(groups[g][e]||0)+1; });
  const degrees=['박사','석사','학사이하']; const groupNames=GROUP_ORDER.filter(g=>groups[g]);
  let rows='<tr><th class="lbl">조직명</th>'+degrees.map(d=>`<th>${d}</th>`).join('')+'<th>계</th></tr>';
  const totals={박사:0,석사:0,학사이하:0};
  groupNames.forEach(g=>{ const gd=groups[g]||{}; let sum=0; rows+=`<tr><td class="lbl">${g}</td>`;
    degrees.forEach(d=>{ const v=gd[d]||0; sum+=v; totals[d]+=v; rows+=`<td>${v}</td>`; }); rows+=`<td>${sum}</td></tr>`; });
  const hqGroup = groups['미분류'] || {};
  degrees.forEach(d=>{ totals[d] += (hqGroup[d]||0); });
  const totalSum = degrees.reduce((a,d)=>a+totals[d],0);
  rows += `<tr class="total"><td class="lbl">합계</td>${degrees.map(d=>`<td>${totals[d]}</td>`).join('')}<td>${totalSum}</td></tr>`;
  document.getElementById('eduContent').innerHTML = `<div class="split"><div class="tbl-col"><table class="tbl">${rows}</table></div><div class="chart-col" id="eduPie"></div></div>`;
  const colors = donutPalette(degrees.length);
  renderDonut('eduPie', degrees.map((d,i)=>({label:d, value:totals[d], color:colors[i]})), '학력 분포');
}






