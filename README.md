function renderDonut(containerId,data,centerLabel,opts={}){
  const total = data.reduce((a,d)=>a+d.value,0);
  const size = opts.size || 260;
  const cx=size/2, cy=size/2, r=size*0.29;
  const toRad=d=>d*Math.PI/180, pt=(deg,radius)=>[cx+radius*Math.cos(toRad(deg)),cy+radius*Math.sin(toRad(deg))];
  let start=-90, paths='', labels='', labelIdx=0;
  data.forEach(d=>{
    const sweep = total?(d.value/total)*360:0; const end=start+sweep; const mid=start+sweep/2;
    const [x1,y1]=pt(start,r), [x2,y2]=pt(end,r); const large=sweep>180?1:0;
    paths += `<path d="M${cx},${cy} L${x1},${y1} A${r},${r} 0 ${large} 1 ${x2},${y2} Z" fill="${d.color}" stroke="#fff" stroke-width="1.5"/>`;
    const pct = total?Math.round(d.value/total*100):0;
    if(d.value>0 && (opts.forceLabels || pct>=8)){
      const isRight = Math.cos(toRad(mid))>=0;
      const stagger = labelIdx % 2 === 0 ? 0.09 : 0.19;
      const [lx1,ly1]=pt(mid,r), [lx2,ly2]=pt(mid,r+size*stagger);
      const tx = lx2+(isRight?7:-7), anchor=isRight?'start':'end';
      labels += `<line x1="${lx1}" y1="${ly1}" x2="${lx2}" y2="${ly2}" stroke="#98A2B3" stroke-width="1"/>
        <text x="${tx}" y="${ly2}" text-anchor="${anchor}" font-size="10.5" fill="#344054">
          <tspan x="${tx}" dy="-2">${d.label}</tspan><tspan x="${tx}" dy="13" font-weight="700">${d.value}명, ${pct}%</tspan></text>`;
      labelIdx++;
    }
    start = end;
  });
  const legend = data.map(d=>{ const pct=total?Math.round(d.value/total*100):0; return `<span class="pie-legend-item"><i style="background:${d.color}"></i>${d.label} ${d.value}명(${pct}%)</span>`; }).join('');
  document.getElementById(containerId).innerHTML = `<div class="pie-title">${centerLabel}</div><svg width="100%" height="${size}" viewBox="0 0 ${size} ${size}" style="overflow:visible;">${paths}${labels}</svg><div class="pie-legend">${legend}</div>`;
}
