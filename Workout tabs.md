```dataviewjs
let rows = [];
function convertDateFormat(dateStr) {
  // Match DD-MM-YYYY using regex adapt for american or other data format
  const match = dateStr.match(/^(\d{2})-(\d{2})-(\d{4})$/);
// Return original if format doesn't match
  if (!match) return dateStr; 

  const [, day, month, year] = match;
  return `${year}-${month}-${day}`;
}

for (let page of dv.pages('"workouts"')) {
  let date = page.workout_date;
  date = convertDateFormat(date);
  for (let ex of page.exercises ?? []) {
    let sets = ex.sets ?? [];

    let set1 = sets[0] ? `${sets[0].reps ?? ""} x ${sets[0].weight ?? ""}` : "";
    let set2 = sets[1] ? `${sets[1].reps ?? ""} x ${sets[1].weight ?? ""}` : "";
    let set3 = sets[2] ? `${sets[2].reps ?? ""} x ${sets[2].weight ?? ""}` : "";

    rows.push([
      date,
      ex.name,
      set1,
      set2,
      set3,
      ex.note ?? ""
    ]);
  }
}

dv.table(["Date", "Exercise", "Set 1", "Set 2", "Set 3", "Note"], rows);
```



