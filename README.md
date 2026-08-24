const EMBEDDED_CURR_CSV = `여기에 당월 CSV 내용 그대로 붙여넣기`;
const EMBEDDED_PREV_CSV = `여기에 전월 CSV 내용 그대로 붙여넣기 (없으면 이 줄과 아래 관련 줄 생략)`;

state.curr = parseCsvText(EMBEDDED_CURR_CSV);
state.prev = parseCsvText(EMBEDDED_PREV_CSV);
renderAllFromCurrent();
renderBUSection();
if(state.curr && state.prev) renderComparison();

