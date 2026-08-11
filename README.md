rows += `<tr><td class="lbl">비율(%)</td>${degrees.map(d=>`<td>${totalSum?Math.round(totals[d]/totalSum*100):0}%</td>`).join('')}<td>100%</td></tr>`;
document.getElementById('buEduContent').innerHTML = `<div class="split"><div class="tbl-col"><table class="tbl">${rows}</table></div><div class="chart-col" id="buEduPie"></div></div>`;
const buEduColors = donutPalette(degrees.length);
renderDonut('buEduPie', degrees.map((d,i)=>({label:d, value:totals[d], color:buEduColors[i]})), '전사 학력 분포');
