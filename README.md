const totalSum = degrees.reduce((a,d)=>a+totals[d],0);
rows += `<tr class="total"><td class="lbl">전사 합계</td>${degrees.map(d=>`<td>${totals[d]}</td>`).join('')}<td>${totalSum}</td></tr>`;
document.getElementById('buEduContent').innerHTML = `<table class="tbl">${rows}</table>`;
