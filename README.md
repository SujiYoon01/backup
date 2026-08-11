function renderLevelPyramid(active){
  const rows = [];
  GROUP_ORDER.forEach(g=>{
    ORG_GROUPS[g].forEach(org=>{
      const counts={}; const names={}; let total=0;
      active.forEach(r=>{
        if(r[ORG_FIELD]!==org) return;
        const {level}=classifyJobCode(r);
        const nl = normalizeLevel(level);
        if(nl==='미분류') return;
        counts[nl]=(counts[nl]||0)+1; total++;
        (names[nl]=names[nl]||[]).push(r['성명']);
      });
      if(total>0) rows.push({label:org, counts, names, total});
    });
  });
  renderLevelPyramidCard('levelPyramidContent', rows, PYRAMID_LEVELS, '부서');
}
