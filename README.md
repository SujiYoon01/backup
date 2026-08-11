renderDonut('buHeadcountContent', BU_LIST.map((b,i)=>({label:b, value:buGroups[b].count, color: b==='우주' ? 'var(--gold)' : colors[i]})), '전사 재직 인원(명)');
