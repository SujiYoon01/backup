const cleaned = rows.filter(r =>
  !EXCLUDE_구분.includes(String(r['구분']).trim()) &&
  !EXCLUDE_직급.includes(String(r['직급']).trim())
);
