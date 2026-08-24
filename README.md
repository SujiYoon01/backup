const NAME_TITLE_OVERRIDES = { '김정현':'R3(연구원)' };
function displayTitle(name, raw){ return NAME_TITLE_OVERRIDES[name] || raw; }
const NAME_TITLE_CHANGE_OVERRIDES = {
  '안정민': { before:'S6(차장)', after:'R6(수석연구원)' },
  '김수환': { before:'A3(사원)', after:'E3(연구원)' }
};
function displayTitleChange(name, raw, which){
  const o = NAME_TITLE_CHANGE_OVERRIDES[name];
  return o ? o[which] : raw;
}
