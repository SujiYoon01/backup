const NAME_TITLE_OVERRIDES = { '안정민':'R6(수석연구원)', '김수환':'E3(연구원)', '김정현':'R3(연구원)' };
function displayTitle(name, raw){ return NAME_TITLE_OVERRIDES[name] || raw; }
const FORCE_TRANSFER_IN = ['김규형','김동환','배기형']; // 이 셋만 '전입', 나머지 신규 등장자는 '채용'

function renderComparison(){
  const prevMap = new Map(state.prev.map(r=>[r['사번'],r]));
  const currMap = new Map(state.curr.map(r=>[r['사번'],r]));
  const ym = (()=>{ const d=baseDate(); return String(d.getFullYear())+String(d.getMonth()+1).padStart(2,'0'); })();
  const flow={채용:0,복직:0,전입:0,퇴직:0,휴직:0,전출:0}; const flowNames={채용:[],복직:[],전입:[],퇴직:[],휴직:[],전출:[]};
  const changeRows=[];

  currMap.forEach((row,id)=>{
    if(!isSpace(row[ORG_FIELD])) return;
    const prev = prevMap.get(id);
    if(!prev){
      const type = FORCE_TRANSFER_IN.includes(row['성명']) ? '전입' : '채용';
      flow[type]++; flowNames[type].push(row['성명']);
      changeRows.push({name:row['성명'], changes:[{type:type==='채용'?'신규채용':'전입', before:'-', after:row[ORG_FIELD], tag:'hire', position:displayTitle(row['성명'], row['직급명'])}]});
      return;
    }
    if(!isSpace(prev[ORG_FIELD])){
      flow['전입']++; flowNames['전입'].push(row['성명']);
      changeRows.push({name:row['성명'], changes:[{type:'전입(타사업부)', before:prev[ORG_FIELD], after:row[ORG_FIELD], tag:'transfer-in', position:displayTitle(prev['성명'], prev['직급명'])}]});
      return;
    }
    const changes=[];
    if(prev['재직상태']==='재직' && row['재직상태']==='휴직'){ flow['휴직']++; flowNames['휴직'].push(row['성명']); changes.push({type:'휴직', before:'재직', after:row['휴직구분명']||'휴직', tag:'leave', position:displayTitle(prev['성명'], prev['직급명'])}); }
    if(prev['재직상태']==='휴직' && row['재직상태']==='재직'){ flow['복직']++; flowNames['복직'].push(row['성명']); changes.push({type:'복직', before:'휴직', after:'재직', tag:'promo', position:displayTitle(prev['성명'], prev['직급명'])}); }
    if(prev[ORG_FIELD] !== row[ORG_FIELD]) changes.push({type:'전배', before:prev[ORG_FIELD], after:row[ORG_FIELD], tag:'move', position:displayTitle(row['성명'], row['직급명'])});
    if(prev['직급명'] !== row['직급명']) changes.push({type:'직급변동', before:displayTitle(row['성명'], prev['직급명']), after:displayTitle(row['성명'], row['직급명']), tag:'promo', position:displayTitle(row['성명'], row['직급명'])});
    if(prev['사업장'] !== row['사업장']) changes.push({type:'사업장이동', before:prev['사업장'], after:row['사업장'], tag:'move', position:displayTitle(row['성명'], row['직급명'])});
    if(prev['근무지역'] !== row['근무지역']) changes.push({type:'근무지이동', before:prev['근무지역'], after:row['근무지역'], tag:'move', position:displayTitle(row['성명'], row['직급명'])});
    const prevDispatched = (prev['파견조직코드명']||'').trim()!=='';
    const currDispatched = (row['파견조직코드명']||'').trim()!=='';
    if(!prevDispatched && currDispatched) changes.push({type:'파견시작', before:prev[ORG_FIELD], after:row['파견조직코드명'], tag:'dispatch', position:displayTitle(row['성명'], row['직급명'])});
    if(prevDispatched && !currDispatched) changes.push({type:'파견복귀', before:prev['파견조직코드명'], after:'원소속 복귀', tag:'dispatch', position:displayTitle(row['성명'], row['직급명'])});
    if(changes.length) changeRows.push({name:row['성명'], changes});
  });
  prevMap.forEach((row,id)=>{
    if(!isSpace(row[ORG_FIELD])) return;
    const curr = currMap.get(id);
    if(!curr){ flow['퇴직']++; flowNames['퇴직'].push(row['성명']); changeRows.push({name:row['성명'], changes:[{type:'퇴직', before:row[ORG_FIELD], after:'퇴직', tag:'retire', position:row['직급명']}]}); return; }
    if(!isSpace(curr[ORG_FIELD])){ flow['전출']++; flowNames['전출'].push(row['성명']); changeRows.push({name:row['성명'], changes:[{type:'전출(타사업부)', before:row[ORG_FIELD], after:curr[ORG_FIELD], tag:'transfer-out', position:row['직급명']}]}); }
  });
  renderFlowCard(flow,flowNames); renderChangeTable(changeRows);
