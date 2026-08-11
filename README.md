const SITE_SUMMARY_ORGS = {
  '용인': ['차세대위성체계팀','위성체계팀','위성본체팀','위성탑재체1팀','위성탑재체2팀','위성지상체팀','위성기계팀'],
  '서현': ['위성탑재체3팀'],
  '제주': ['제주우주센터'],
  '서울2': ['우주사업전략팀','우주사업단','솔루션사업팀']
};
function renderSiteCard(active){
  const counts={};
  active.forEach(r=>{ const s=r['사업장']||'미기재'; counts[s]=(counts[s]||0)+1; });
  const labels = Object.keys(counts).sort((a,b)=>counts[b]-counts[a]);
  const colors = donutPalette(labels.length);
  renderDonut('siteContent', labels.map((l,i)=>({label:l, value:counts[l], color:colors[i]})), '사업장별 인원(명)');
  attachSiteSummaryTooltip(labels);
}
function attachSiteSummaryTooltip(labels){
  const svg = document.querySelector('#siteContent svg');
  if(!svg) return;
  svg.querySelectorAll('path').forEach((path,i)=>{
    const site = labels[i];
    if(!SITE_SUMMARY_ORGS[site]) return;
    path.style.cursor='pointer';
    path.addEventListener('click', e=>{ e.stopPropagation(); toggleSiteSummaryTooltip(e,site); });
  });
}
function toggleSiteSummaryTooltip(e,site){
  let tip=document.getElementById('siteSummaryTooltip');
  if(!tip){ tip=document.createElement('div'); tip.id='siteSummaryTooltip'; tip.className='flow-tooltip'; document.body.appendChild(tip); }
  if(tip.style.display==='block' && tip.dataset.site===site){ tip.style.display='none'; delete tip.dataset.site; return; }
  const orgs = SITE_SUMMARY_ORGS[site]||[];
  tip.innerHTML = `<div style="font-weight:700;margin-bottom:4px;">${site} 소속 부서</div>` + orgs.map(o=>`<div>${o}</div>`).join('');
  tip.dataset.site = site;
  tip.style.left=(e.pageX+12)+'px'; tip.style.top=(e.pageY+12)+'px'; tip.style.display='block';
}
document.addEventListener('click', e=>{ const tip=document.getElementById('siteSummaryTooltip'); if(tip && e.target.tagName!=='path'){ tip.style.display='none'; delete tip.dataset.site; } });
