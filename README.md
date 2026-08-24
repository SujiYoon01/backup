function showPart(n){
  document.getElementById('part1').style.display = n===1 ? 'block' : 'none';
  document.getElementById('part2').style.display = n===2 ? 'block' : 'none';
  const kpi1 = document.getElementById('kpiRowPart1'); if(kpi1) kpi1.style.display = n===1 ? 'flex' : 'none';
  const kpi2 = document.getElementById('kpiRowPart2'); if(kpi2) kpi2.style.display = n===2 ? 'flex' : 'none';
  const nav1 = document.getElementById('navPart1'); if(nav1) nav1.classList.toggle('active', n===1);
  const nav2 = document.getElementById('navPart2'); if(nav2) nav2.classList.toggle('active', n===2);
}
