const EMBEDDED_CURR_CSV = `여기에 붙여넣기`;
const EMBEDDED_PREV_CSV = `여기에 붙여넣기`;

state.curr = parseCsvText(EMBEDDED_CURR_CSV);
state.prev = parseCsvText(EMBEDDED_PREV_CSV);
renderAllFromCurrent();
renderBUSection();
if(state.curr && state.prev) renderComparison();
