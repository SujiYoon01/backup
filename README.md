renderDonut('jobContent', labels.map((l,i)=>({label:FAMILY_LABELS[l], value:counts[l], color:colors[i]})), '총원(명)', {forceLabels:true, size:460});
