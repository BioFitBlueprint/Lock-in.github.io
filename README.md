<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<meta name="theme-color" content="#1c1c1e">
<meta name="apple-mobile-web-app-capable" content="yes">
<title>Gym Routine</title>
<style>
:root{
  --char:#1c1c1e; --char2:#262628; --char3:#333336;
  --beige:#d9c9ae; --beige-dim:#a89b85; --cream:#efe6d4;
  --line:rgba(217,201,174,.18);
}
*{box-sizing:border-box;margin:0;padding:0;-webkit-tap-highlight-color:transparent}
html{background:var(--char)}
body{
  font-family:-apple-system,"SF Pro Display","Helvetica Neue",Arial,sans-serif;
  background:var(--char);color:var(--cream);line-height:1.4;
  padding:max(20px,env(safe-area-inset-top)) 16px max(40px,env(safe-area-inset-bottom));
  max-width:560px;margin:0 auto;
}
header{padding:12px 4px 20px}
h1{font-size:2.6rem;font-weight:800;letter-spacing:-.03em;color:var(--beige);line-height:1}
header p{color:var(--beige-dim);margin-top:8px;font-size:.95rem}

.tabs{display:flex;gap:8px;overflow-x:auto;padding:4px 0 16px;position:sticky;top:0;background:var(--char);z-index:5;scrollbar-width:none}
.tabs::-webkit-scrollbar{display:none}
.tab{
  flex:0 0 auto;border:1px solid var(--line);background:var(--char2);color:var(--beige-dim);
  font:600 .9rem inherit;font-family:inherit;padding:10px 16px;border-radius:999px;cursor:pointer;
}
.tab.on{background:var(--beige);color:var(--char);border-color:var(--beige)}
.tab.today:not(.on){border-color:var(--beige);color:var(--beige)}

.day{display:none}
.day.on{display:block}
.title{font-size:1.7rem;font-weight:800;letter-spacing:-.02em;color:var(--beige)}
.focus{color:var(--beige-dim);font-size:.9rem;margin:6px 0 18px}

.group{background:var(--char2);border:1px solid var(--line);border-radius:18px;margin-bottom:14px;overflow:hidden}
.group h3{font-size:.8rem;font-weight:700;letter-spacing:.04em;color:var(--beige-dim);padding:14px 18px 6px}
.ex{display:flex;justify-content:space-between;align-items:center;gap:12px;padding:14px 18px;border-top:1px solid var(--line);cursor:pointer}
.group h3+.ex{border-top:none}
.ex .name{font-weight:600;font-size:1rem}
.ex .sets{font-weight:800;color:var(--beige);white-space:nowrap;font-variant-numeric:tabular-nums}
.ex.done{opacity:.4}
.ex.done .name{text-decoration:line-through}
.ex::before{
  content:"";flex:0 0 22px;height:22px;border-radius:50%;border:2px solid var(--beige-dim);order:-1;
}
.ex.done::before{background:var(--beige);border-color:var(--beige)}
.ex .name{flex:1}

.cardio{background:var(--beige);color:var(--char);border-radius:18px;padding:18px;display:flex;justify-content:space-between;align-items:center;font-weight:700}
.cardio span:last-child{font-size:1.5rem;font-weight:800}

.rest{text-align:center;padding:64px 20px;background:var(--char2);border:1px solid var(--line);border-radius:18px}
.rest p{font-size:2rem;font-weight:800;color:var(--beige)}
.rest small{display:block;margin-top:8px;color:var(--beige-dim)}
.reset{display:block;margin:20px auto 0;background:none;border:1px solid var(--line);color:var(--beige-dim);padding:10px 18px;border-radius:999px;font:inherit;font-size:.85rem;cursor:pointer}
</style>
</head>
<body>
<header>
  <h1>Gym Routine</h1>
  <p>Tap an exercise to check it off.</p>
</header>

<nav class="tabs" id="tabs"></nav>
<main id="days"></main>

<script>
const plan = [
 {d:"Mon",n:"Monday",t:"Abs & Lower Body A",f:"Quads, Hamstrings, Glutes, Calves, Inner Thighs & Core",
  g:[{h:"Strength",e:[["Leg Extensions","3 x 12"],["Leg Press","3 x 12"],["Bulgarian Split Squats","3 x 10"],["Hamstring Curls","3 x 12"]]},
     {h:"Calves & Core",e:[["Lifted Calf Raises","4 x 12"],["Wide Dumbbell Sumo Squats","3 x 10"],["Ab Crunches","6 x 20"],["Plank","1 min"],["Russian Twists","4 x 30 sec"]]},
     {h:"Cooldown",e:[["Stretch",""],["Roll Out",""]]}],
  c:["Treadmill Inclined Walking","30 min"]},
 {d:"Tue",n:"Tuesday",t:"Upper Body A",f:"Chest, Back, Shoulders, Biceps, Triceps & Forearms",
  g:[{h:"Push & Pull",e:[["Chest Press","4 x 12"],["Wide-Grip Lat Pulldown","3 x 12"],["Seated Cable Row","3 x 12"],["Shoulder Press","4 x 10"],["Lateral Raises","3 x 12"]]},
     {h:"Arms & Rear Delts",e:[["Machine Bicep Curls","4 x 10"],["Overhead Tricep Extensions","3 x 12"],["Hammer Curls","4 x 10"],["Dumbbell Bent-Over Reverse Flies","3 x 12"]]},
     {h:"Cooldown",e:[["Stretch",""],["Roll Out",""]]}],
  c:["Treadmill Inclined Walking","45 min"]},
 {d:"Wed",n:"Wednesday",t:"Cardio + Abs + Lower Body B",f:"Cardio, Glutes, Hamstrings, Quads, Calves & Core",
  g:[{h:"Strength",e:[["Single-Legged Leg Press","3 x 12"],["Hamstring Curls","3 x 12"],["Weighted Lunges","3 x 5 laps"]]},
     {h:"Core",e:[["Side Plank","30 sec"],["Plank","1 min"]]},
     {h:"Cooldown",e:[["Stretch",""],["Roll Out",""]]}],
  c:["Treadmill Inclined Walking","30 min"]},
 {d:"Thu",n:"Thursday",rest:1},
 {d:"Fri",n:"Friday",t:"Cardio + Upper Body B",f:"Cardio, Chest, Back, Shoulders, Biceps, Triceps & Forearms",
  g:[{h:"Push & Pull",e:[["Chest Press","3 x 10"],["Close Inside-Grip Lat Pulldowns","3 x 12"],["Rope Lat Pulldowns","3 x 12"],["Shoulder Press","3 x 10"]]},
     {h:"Back & Arms",e:[["Bent-Over Dumbbell Rows","3 x 12"],["Dumbbell Bent-Over Reverse Flies","3 x 12"],["Shoulder Shrugs","3 x 15"],["Reverse Dumbbell Curls","4 x 10"],["Kettlebell Wrist Curls","4 x 12"]]},
     {h:"Cooldown",e:[["Stretch",""],["Foam Roll",""]]}],
  c:["Treadmill Inclined Walking","1 hour"]},
 {d:"Sat",n:"Saturday",rest:1},
 {d:"Sun",n:"Sunday",rest:1}
];

const tabs=document.getElementById("tabs"), days=document.getElementById("days");
const todayIdx=(new Date().getDay()+6)%7;
const esc=s=>s.replace(/&/g,"&amp;").replace(/</g,"&lt;");
let store={};
try{store=JSON.parse(localStorage.getItem("gym-done")||"{}")}catch(e){}
const save=()=>{try{localStorage.setItem("gym-done",JSON.stringify(store))}catch(e){}};

plan.forEach((p,i)=>{
  const b=document.createElement("button");
  b.className="tab"+(i===todayIdx?" today":"");
  b.textContent=p.d; b.onclick=()=>show(i);
  tabs.appendChild(b);

  const s=document.createElement("section");
  s.className="day";
  if(p.rest){
    s.innerHTML=`<h2 class="title">${p.n}</h2><div class="rest" style="margin-top:18px"><p>Rest Day</p><small>Recover. Eat. Sleep.</small></div>`;
  }else{
    let h=`<h2 class="title">${p.t}</h2><p class="focus">${esc(p.f)}</p>`;
    p.g.forEach(g=>{
      h+=`<div class="group"><h3>${g.h}</h3>`;
      g.e.forEach((x,j)=>{
        const k=`${i}-${g.h}-${j}`;
        h+=`<div class="ex${store[k]?" done":""}" data-k="${k}"><span class="name">${esc(x[0])}</span><span class="sets">${x[1]}</span></div>`;
      });
      h+=`</div>`;
    });
    h+=`<div class="cardio"><span>${p.c[0]}</span><span>${p.c[1]}</span></div>`;
    h+=`<button class="reset" data-day="${i}">Reset day</button>`;
    s.innerHTML=h;
  }
  days.appendChild(s);
});

function show(i){
  [...tabs.children].forEach((t,j)=>t.classList.toggle("on",j===i));
  [...days.children].forEach((d,j)=>d.classList.toggle("on",j===i));
  tabs.children[i].scrollIntoView({inline:"center",block:"nearest"});
}
days.addEventListener("click",e=>{
  const ex=e.target.closest(".ex");
  if(ex){const k=ex.dataset.k;store[k]=!store[k];ex.classList.toggle("done",store[k]);save();return}
  const r=e.target.closest(".reset");
  if(r){const d=r.dataset.day;Object.keys(store).forEach(k=>{if(k.startsWith(d+"-"))delete store[k]});save();
    r.closest("section").querySelectorAll(".ex").forEach(x=>x.classList.remove("done"))}
});
show(todayIdx);
</script>
</body>
</html>
