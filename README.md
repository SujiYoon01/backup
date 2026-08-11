renderDonut('buExecContent', BU_LIST.map((b,i)=>({label:b, value:buGroups[b].exec, color: b==='우주' ? 'var(--gold)' : colors[i]})), '전사 임원(명)', {forceLabels:true, size:400});
