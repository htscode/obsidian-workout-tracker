
```dataviewjs
// Collect LATPUL volume per workout date

const colors = [ "rgba(255, 99, 132, 0.2)",   // Red-pink (soft)
  "rgba(255, 159, 64, 0.2)",   // Orange
  "rgba(255, 205, 86, 0.2)",   // Yellow
  "rgba(75, 192, 192, 0.2)",   // Greenish cyan (your original)
  "rgba(54, 162, 235, 0.2)",   // Soft blue
  "rgba(153, 102, 255, 0.2)"   // Purple-lavender
];

const border_colors =  [
  "rgba(255, 99, 132, 1)",   // Red-pink border
  "rgba(255, 159, 64, 1)",   // Orange border
  "rgba(255, 205, 86, 1)",   // Yellow border
  "rgba(75, 192, 192, 1)",   // Cyan-teal border (your original)
  "rgba(54, 162, 235, 1)",   // Blue border
  "rgba(153, 102, 255, 1)"   // Purple-lavender border
];
// function to take date string and make it to YY-MM-DD format which is important to display the data in right date order
function convertDateFormat(dateStr) {
  // Match DD-MM-YYYY using regex adapt for american or other data format
  const match = dateStr.match(/^(\d{2})-(\d{2})-(\d{4})$/);
// Return original if format doesn't match
  if (!match) return dateStr; 

  const [, day, month, year] = match;
  return `${year}-${month}-${day}`;
}


// this depends on the your exercises in your template adapt
// I am no gym bro exercise names might reflect that
const exercise_names =["LATPUL","DRAGGY","DEADLIFT","SQUAT","BENCH","WEIGHTSHIGH"];
for (let i=0; i < exercise_names.length;i++) {

	let volumes = {};
	const exname = exercise_names[i];
	
	for (let page of dv.pages('"workouts"')) {
	  let date = page.workout_date;
	  date = convertDateFormat(date);
	  for (let ex of page.exercises ?? []) {
	    if (ex.name?.toUpperCase() === exname ) {
	      let sets = ex.sets ?? [];
	      let totalVolume = 0;
			// compute total volume across sets 
	      for (let set of sets) {
		    // with defaults to avoid errors
	        let reps = Number(set.reps ?? 1);
	        let weight = Number(set.weight ?? 0);
	        totalVolume += reps * weight;
	      }
	
	      volumes[date] = (volumes[date] ?? 0) + totalVolume;
	    }
	  }
	}
	// handle 0 entries (e.g exercise skipped that day)
	// Remove entries with null or 0 values
	for (let entry in volumes) {
		if (volumes[entry] === null || volumes[entry] === 0) {
			delete volumes[entry];
		}
	}

	// Sort by date note that dates work as keys here
	let sortedDates = Object.keys(volumes).sort();
	// map sorting on volume data
	let volumeData = sortedDates.map(d => volumes[d]);
// create figure specs
	const chart_input = {
	  type: "line",
	  data: {
	    labels: sortedDates,
	    datasets: [{
	      label: exname + " Volume (reps × weight)",
	      data: volumeData,
	      borderColor: border_colors[i],
	      fill: true,
	      backgroundColor: colors[i],
	    }]
	  },
	  options: {
	    responsive: true,
	    scales: {
	      y: {
	        beginAtZero: false,
	        title: { display: true, text: "Total Volume" }
	      },
	      x: {
	        title: { display: true, text: "Date" }
	      }
	    }
	}};
	
	
	window.renderChart(chart_input, this.container);
};
```


```dataviewjs
// Collect LATPUL volume per workout date

const colors = [ "rgba(255, 99, 132, 0.2)",   // Red-pink (soft)
  "rgba(255, 159, 64, 0.2)",   // Orange
  "rgba(255, 205, 86, 0.2)",   // Yellow
  "rgba(75, 192, 192, 0.2)",   // Greenish cyan (your original)
  "rgba(54, 162, 235, 0.2)",   // Soft blue
  "rgba(153, 102, 255, 0.2)"   // Purple-lavender
];

const border_colors =  [
  "rgba(255, 99, 132, 1)",   // Red-pink border
  "rgba(255, 159, 64, 1)",   // Orange border
  "rgba(255, 205, 86, 1)",   // Yellow border
  "rgba(75, 192, 192, 1)",   // Cyan-teal border (your original)
  "rgba(54, 162, 235, 1)",   // Blue border
  "rgba(153, 102, 255, 1)"   // Purple-lavender border
];

function convertDateFormat(dateStr) {
  // Match DD-MM-YYYY using regex
  const match = dateStr.match(/^(\d{2})-(\d{2})-(\d{4})$/);
  if (!match) return dateStr; // Return original if format doesn't match

  const [, day, month, year] = match;
  return `${year}-${month}-${day}`;
}


const exercise_names =["LATPUL","DRAGGY","DEADLIFT","SQUAT","BENCH","WEIGHTSHIGH"];
for (let i=0; i < exercise_names.length;i++) {
	let max_weights = {};
	const exname = exercise_names[i];
	
	for (let page of dv.pages('"workouts"')) {
	  let date = page.workout_date;
	  date = convertDateFormat(date);
	  for (let ex of page.exercises ?? []) {
	    if (ex.name?.toUpperCase() === exname ) {
			let sets = ex.sets ?? [];
		    let max_weight = 0;
		    for (let set of sets) {
		        let weight = Number(set.weight ?? 0);
		        if (weight > max_weight){
			        max_weight = weight;
	        }
	      }
	
	      max_weights[date] = (max_weights[date] ?? 0) + max_weight;
	    }
	  }
	}
	// clean to avoid null or 0 values in case an exercise wasnt done
	for (let entry in max_weights) {
		if (max_weights[entry] === null || max_weights[entry] === 0) {
			delete max_weights[entry];
		}
	}

	// Sort by date, dates work as keys
	let sortedDates = Object.keys(max_weights).sort();
	let max_weight = sortedDates.map(d => max_weights[d]);
	
	const chart_input = {
	  type: "line",
	  data: {
	    labels: sortedDates,
	    datasets: [{
	      label: exname + " Max weight",
	      data: max_weight,
	      borderColor: border_colors[i],
	      fill: true,
	      backgroundColor: colors[i],
	    }]
	  },
	  options: {
	    responsive: true,
	    scales: {
	      y: {
	        beginAtZero: false,
	        title: { display: true, text: "Max Weight" }
	      },
	      x: {
	        title: { display: true, text: "Date" }
	      }
	    }
	}};
	
	
	window.renderChart(chart_input, this.container);
};
```

```dataviewjs
// Collect LATPUL volume per workout date

const colors = [ "rgba(255, 99, 132, 0.2)",   // Red-pink (soft)
  "rgba(255, 159, 64, 0.2)",   // Orange
  "rgba(255, 205, 86, 0.2)",   // Yellow
  "rgba(75, 192, 192, 0.2)",   // Greenish cyan (your original)
  "rgba(54, 162, 235, 0.2)",   // Soft blue
  "rgba(153, 102, 255, 0.2)"   // Purple-lavender
];

const border_colors =  [
  "rgba(255, 99, 132, 1)",   // Red-pink border
  "rgba(255, 159, 64, 1)",   // Orange border
  "rgba(255, 205, 86, 1)",   // Yellow border
  "rgba(75, 192, 192, 1)",   // Cyan-teal border (your original)
  "rgba(54, 162, 235, 1)",   // Blue border
  "rgba(153, 102, 255, 1)"   // Purple-lavender border
];

function convertDateFormat(dateStr) {
  // Match DD-MM-YYYY using regex
  const match = dateStr.match(/^(\d{2})-(\d{2})-(\d{4})$/);
  if (!match) return dateStr; // Return original if format doesn't match

  const [, day, month, year] = match;
  return `${year}-${month}-${day}`;
}



const exercise_names =["LATPUL","DRAGGY","DEADLIFT","SQUAT","BENCH","WEIGHTSHIGH"];
for (let i=0; i < exercise_names.length;i++) {

	let rpms = {};
	
	const exname = exercise_names[i];
	
	for (let page of dv.pages('"workouts"')) {
		let rpm_max = 0
		let date = page.workout_date;
		date = convertDateFormat(date);
		for (let ex of page.exercises ?? []) {
	    if (ex.name?.toUpperCase() === exname ) {
	      let sets = ex.sets ?? [];
	      let totalVolume = 0;
	
	      for (let set of sets) {
	        let reps = Number(set.reps ?? 1);
	        let weight = Number(set.weight ?? 0);
	        totalVolume += reps * weight;
	        // estimate 1 rpm max 
	        let rpm_max_new = weight * (1 + reps / 30);
	        if (rpm_max_new > rpm_max){
		        rpm_max = rpm_max_new;
	        }
	      }
	
	      rpms[date] = rpm_max;
	    }
	  }
	}
	//clean nulls and 0
	for (let entry in rpms) {
		if (rpms[entry] === null || rpms[entry] === 0) {
			delete rpms[entry];
		}
	}

	

	// Sort by date, date is keys
	let sortedDates = Object.keys(rpms).sort();
	let rpmsData = sortedDates.map(d => rpms[d]);
	
	const chart_input = {
	  type: "line",
	  data: {
	    labels: sortedDates,
	    datasets: [{
	      label: exname + " estimated 1 rep max",
	      data: rpmsData,
	      borderColor: border_colors[i],
	      fill: true,
	      backgroundColor: colors[i],
	    }]
	  },
	  options: {
	    responsive: true,
	    scales: {
	      y: {
	        beginAtZero: false,
	        title: { display: true, text: "1 rep max" }
	      },
	      x: {
	        title: { display: true, text: "Date" }
	      }
	    }
	}};
	
	
	window.renderChart(chart_input, this.container);
};
```