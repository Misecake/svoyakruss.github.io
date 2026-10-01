# svoyakruss.github.io
<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>Своя игра</title>
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  body{
    font-family:'Segoe UI',Roboto,Arial,sans-serif;
    background:radial-gradient(circle at 50% 0%,#2a0f4a,#05010f 70%);
    color:#fff;min-height:100vh;padding:18px;overflow-x:hidden;
  }
  h1{text-align:center;font-size:clamp(20px,3vw,36px);letter-spacing:3px;
     text-transform:uppercase;color:#ff4dd2;text-shadow:0 0 30px rgba(255,77,210,.6);
     margin-bottom:4px;font-weight:900}
  .sub{text-align:center;color:#b48cff;font-size:13px;margin-bottom:20px;letter-spacing:3px}

  .wrap{max-width:1500px;margin:0 auto}

  #cats{display:grid;grid-template-columns:repeat(8,1fr);gap:6px;margin-bottom:6px}
  .cat{background:linear-gradient(180deg,#4a1a8a,#1d0940);border:1px solid #7b3fd8;
       border-radius:8px;padding:12px 4px;text-align:center;font-weight:700;
       font-size:clamp(9px,1vw,14px);text-transform:uppercase;letter-spacing:.5px;
       color:#e0c8ff;min-height:64px;display:flex;align-items:center;justify-content:center}

  #board{display:grid;grid-template-rows:repeat(5,1fr);grid-auto-flow:column;gap:6px;height:52vh;min-height:340px}
  .cell{background:linear-gradient(180deg,#2a0f5c,#130530);border:1px solid #6b2fd8;
        border-radius:8px;display:flex;align-items:center;justify-content:center;
        font-size:clamp(16px,2.2vw,32px);font-weight:800;color:#ff4dd2;cursor:pointer;
        transition:.15s;user-select:none}
  .cell:hover{background:linear-gradient(180deg,#4a1a9e,#2a0f5c);transform:scale(1.03);
              box-shadow:0 0 22px rgba(255,77,210,.6)}
  .cell.used{background:#05010f;border-color:#1a0a30;color:#1a0a30;cursor:default;
             transform:none;box-shadow:none}
  .cell.used:hover{transform:none;box-shadow:none;background:#05010f}

  .special{display:flex;gap:12px;justify-content:center;margin-top:16px;flex-wrap:wrap}
  .special button{background:linear-gradient(180deg,#c41a8a,#6a0a4a);border:1px solid #ff4dd2;
    color:#fff;padding:12px 22px;border-radius:10px;font-size:15px;font-weight:700;
    cursor:pointer;letter-spacing:.5px;transition:.15s}
  .special button:hover{transform:translateY(-2px);box-shadow:0 6px 22px rgba(255,77,210,.5)}

  #scores{display:flex;gap:14px;justify-content:center;margin-top:22px;flex-wrap:wrap}
  .team{background:rgba(255,255,255,.04);border:1px solid #6b2fd8;border-radius:12px;
        padding:12px 18px;min-width:200px;text-align:center}
  .team .name{font-size:12px;color:#b48cff;text-transform:uppercase;letter-spacing:2px;margin-bottom:6px}
  .team .pts{font-size:30px;font-weight:900;color:#ff4dd2;margin-bottom:8px}
  .team .btns{display:flex;gap:5px;justify-content:center}
  .team .btns button{height:32px;border-radius:6px;border:1px solid #6b2fd8;
    background:#1d0940;color:#e0c8ff;font-weight:800;cursor:pointer;font-size:13px;padding:0 10px}
  .team .btns button:hover{background:#4a1a9e}
  .team .btns .minus{color:#ff7b7b;border-color:#7a2a2a}
  .team .btns .minus:hover{background:#3a0a0a}

  #reset{display:block;margin:18px auto 0;background:transparent;border:1px solid #444;
    color:#888;padding:8px 18px;border-radius:8px;cursor:pointer;font-size:13px}
  #reset:hover{color:#fff;border-color:#888}

  #modal{position:fixed;inset:0;background:rgba(2,0,8,.97);display:none;
         align-items:center;justify-content:center;padding:30px;z-index:100}
  #modal.on{display:flex}
  .card{max-width:1150px;width:100%;background:linear-gradient(180deg,#1d0940,#0a0220);
        border:2px solid #7b3fd8;border-radius:18px;padding:clamp(22px,3.5vw,48px);
        box-shadow:0 0 80px rgba(123,63,216,.6);text-align:center;
        max-height:92vh;overflow-y:auto;position:relative}
  .tag{display:inline-block;background:#ff4dd2;color:#0a0220;font-weight:800;font-size:12px;
       padding:5px 16px;border-radius:20px;letter-spacing:2px;text-transform:uppercase;margin-bottom:16px}
  #qText{font-size:clamp(17px,2.4vw,32px);line-height:1.5;font-weight:600;margin-bottom:24px}

  /* Таймер */
  #timerWrap{display:none;margin-bottom:22px}
  #timerWrap.on{display:block}
  #timerBar{height:8px;background:#2a0f5c;border-radius:4px;overflow:hidden;margin-bottom:8px}
  #timerFill{height:100%;width:100%;background:linear-gradient(90deg,#3cc878,#ffd34d);
             transition:width 1s linear, background .3s}
  #timerFill.danger{background:linear-gradient(90deg,#ff4d4d,#ff9500)}
  #timerNum{font-size:15px;color:#b48cff;font-weight:700;letter-spacing:2px}

  #aBox{display:none;background:rgba(60,200,120,.12);border:1px solid #3cc878;
        border-radius:12px;padding:22px;margin-bottom:26px}
  #aBox.on{display:block;animation:pop .3s ease}
  @keyframes pop{from{opacity:0;transform:scale(.95)}to{opacity:1;transform:scale(1)}}
  #aBox .lbl{color:#3cc878;font-size:12px;letter-spacing:3px;text-transform:uppercase;margin-bottom:10px;font-weight:800}
  #aText{font-size:clamp(15px,2vw,26px);line-height:1.55;color:#d8ffe8;font-weight:600;text-align:left}

  .card .acts{display:flex;gap:12px;justify-content:center;flex-wrap:wrap}
  .card .acts button{padding:14px 28px;border-radius:10px;font-size:15px;font-weight:700;
    cursor:pointer;border:none;transition:.15s;letter-spacing:.5px}
  .btn-reveal{background:linear-gradient(180deg,#3cc878,#1f8a4e);color:#fff}
  .btn-reveal:hover{transform:translateY(-2px);box-shadow:0 8px 24px rgba(60,200,120,.45)}
  .btn-start{background:linear-gradient(180deg,#ff4dd2,#8a1a6a);color:#fff}
  .btn-start:hover{transform:translateY(-2px);box-shadow:0 8px 24px rgba(255,77,210,.45)}
  .btn-back{background:#2a0f5c;color:#e0c8ff;border:1px solid #6b2fd8}
  .btn-back:hover{background:#4a1a9e}
</style>
</head>
<body>
<div class="wrap">
  <h1>Своя игра</h1>
  <div class="sub">РУССКИЙ ЯЗЫК</div>

  <div id="cats"></div>
  <div id="board"></div>

  <div class="special">
    <button onclick="special('cat')">🐱 Кот в мешке</button>
    <button onclick="special('auc')">💰 Аукцион</button>
  </div>

  <div id="scores"></div>
  <button id="reset" onclick="resetGame()">Сбросить игру</button>
</div>

<div id="modal">
  <div class="card">
    <div class="tag" id="tag">—</div>
    <div id="timerWrap">
      <div id="timerBar"><div id="timerFill"></div></div>
      <div id="timerNum">30</div>
    </div>
    <div id="qText"></div>
    <div id="aBox">
      <div class="lbl">Ответ</div>
      <div id="aText"></div>
    </div>
    <div class="acts">
      <button class="btn-start" id="startBtn" onclick="startTimer()">▶ Запустить таймер</button>
      <button class="btn-reveal" id="revealBtn" onclick="reveal()">Показать ответ</button>
      <button class="btn-back" onclick="closeModal()">← К полю</button>
    </div>
  </div>
</div>

<script>
const DATA = [
  { title:"Орфография", q:[
    {v:100, q:"Вставьте Н/НН: кова..ый сундук, кова..ый мастером сундук, жёва..ый лист, нежда..ый гость.", a:"кованый (отглаг. прил.), кованный мастером (есть зав. слово), жёваный (несов. вид, нет зав. слов), нежданный (исключение)"},
    {v:200, q:"Вставьте Н/НН: ране..ый боец, ране..ый в бою боец, изране..ый боец, он был ране.. в бою.", a:"раненый, раненный в бою, израненный, ранен. Краткая форма причастия — одна Н; «ранен» — исключение-прилагательное"},
    {v:300, q:"Вставьте Н/НН: смышлё..ый, назва..ый брат, назва..ый выше, посажё..ый отец.", a:"смышлёный (искл., одна Н), названый брат (прил.), названный выше (прич.), посажёный отец (прил., одна Н)"},
    {v:400, q:"Слитно, раздельно или дефис: (не)смотря на дождь, (не)взирая на лица, (не)доумевая, (не)годуя.", a:"несмотря на (предлог — слитно), невзирая на (предлог — слитно), недоумевая (слитно, без НЕ не употр.), негодуя (слитно)"},
    {v:500, q:"Слитно/раздельно/дефис: точь(в)точь, (бок)(о)бок, (по)одиночке, (в)одиночку, (по)двое, (в)двоём.", a:"точь-в-точь (дефис), бок о бок (раздельно), поодиночке (слитно), в одиночку (раздельно), по двое (раздельно), вдвоём (слитно)"}
  ]},
  { title:"Пунктуация", q:[
    {v:100, q:"Расставьте знаки: «Он шёл не смотря под ноги а глядя на звёзды».", a:"Он шёл, не смотря под ноги, а глядя на звёзды. (деепричастие «смотря» — раздельно; запятая перед «а»)"},
    {v:200, q:"Расставьте знаки: «Я как ты знаешь не люблю когда меня перебивают».", a:"Я, как ты знаешь, не люблю, когда меня перебивают. (вводное + придаточное)"},
    {v:300, q:"Расставьте знаки: «Он сказал что если бы не дождь то мы бы уже пришли».", a:"Он сказал, что если бы не дождь, то мы бы уже пришли. (перед «то» запятой НЕТ — составной союз «если… то»)"},
    {v:400, q:"Расставьте знаки: «Дом где жил поэт стоял на холме и был виден издалека».", a:"Дом, где жил поэт, стоял на холме и был виден издалека. (придаточное в середине — с двух сторон)"},
    {v:500, q:"Расставьте знаки: «Он не то что не любил её а просто устал от этих отношений которые тянулись годами и не приносили радости ни ему ни ей».", a:"Он не то что не любил её, а просто устал от этих отношений, которые тянулись годами и не приносили радости ни ему, ни ей."}
  ]},
  { title:"Орфоэпия", q:[
    {v:100, q:"бАловать или баловАть?", a:"баловАть (и: балУясь, балОванный)"},
    {v:200, q:"дОговор или договОр?", a:"договОр, мн.ч. договОры. «дОговор» — просторечие"},
    {v:300, q:"Ударение: христианин, вероисповедание, знамение, мышление.", a:"христианИн, вероисповЕдание, знАмение, мышлЕние"},
    {v:400, q:"Ударение: оптовый, кухонный, украинский, одновременно.", a:"оптОвый, кУхонный, украИнский, одновремЕнно (разг. одноврЕменно)"},
    {v:500, q:"Ударение: апартаменты, бунгало, гастрономия, диспансер, жалюзи, каучук, некролог, пасквиль, феерия, ходатайство.", a:"апартАменты, бунгАло, гастронОмия, диспансЕр, жалюзИ, каучУк, некролОг, пАсквиль, феЕрия, ходАтайство"}
  ]},
  { title:"Морфология", q:[
    {v:100, q:"Как правильно: «в двух тысячах двадцать четвёртом году» или «в две тысячи двадцать четвёртом году»?", a:"в две тысячи двадцать четвёртом году (в составном порядковом числительном склоняется только последнее слово)"},
    {v:200, q:"С пятьюстами рублями или с пятистами рублями?", a:"с пятьюстами рублями"},
    {v:300, q:"Р.п. мн.ч.: граммы, килограммы, гектары, апельсины, помидоры, чулки, носки, рельсы.", a:"граммов, килограммов, гектаров, апельсинов, помидоров, ЧУЛОК, НОСКОВ, рельсов (исключения: чулок, носков)"},
    {v:400, q:"Определите род: тюль, толь, шампунь, тушь, мозоль, рояль, бандероль, вуаль.", a:"тюль — м.р., толь — м.р., шампунь — м.р., тушь — ж.р., мозоль — ж.р., рояль — м.р., бандероль — ж.р., вуаль — ж.р."},
    {v:500, q:"Склоняются ли фамилии: Шевченко, Гюго, Жюль Верн, Александр Дюма, Дюма-сын?", a:"Шевченко — не склон., Гюго — не склон., Жюль Верн — оба склон., Александр Дюма — оба склон., Дюма-сын — только «сын» (Дюма-сына и т.д.)"}
  ]},
  { title:"Синтаксис", q:[
    {v:100, q:"Определите тип односоставного: «Вот и рассвет».", a:"Назывное (номинативное)"},
    {v:200, q:"Найдите ошибку: «Согласно распоряжения директора студенты были отпущены».", a:"Согласно распоряжениЮ директора (дательный падеж при «согласно»)"},
    {v:300, q:"Найдите ошибку: «Те, кто не сдал зачёт, не будет допущен к экзамену».", a:"Те, кто не сдал зачёт, не будУТ допущенЫ к экзамену (при «те» — мн.ч.)"},
    {v:400, q:"Расставьте и объясните: «Он пришёл не потому что хотел а потому что должен был».", a:"Он пришёл не потому, что хотел, а потому, что должен был. (Запятая перед «что» в каждой части — расчленённый союз «не потому… а потому»)"},
    {v:500, q:"Расставьте и объясните: «Я знаю что он сделал это так как ему было велено».", a:"Я знаю, что он сделал это так, как ему было велено. (перед «что» — придаточное; перед «как» — сравнительное/придаточное образа действия)"}
  ]},
  { title:"Лексика", q:[
    {v:100, q:"Значение: «притча во языцех».", a:"Предмет всеобщего обсуждения, насмешек"},
    {v:200, q:"Значение: «сизифов труд».", a:"Бесполезная, бесконечная работа"},
    {v:300, q:"Значение: «прокрустово ложе».", a:"Мерка, под которую насильно подгоняют"},
    {v:400, q:"Значение: «тришкин кафтан».", a:"Положение, при котором устранение одних недостатков порождает другие"},
    {v:500, q:"Значение: «валтасаров пир».", a:"Весёлое, роскошное пиршество накануне бедствия. Из Библии (книга Даниила)"}
  ]},
  { title:"Стилистика", q:[
    {v:100, q:"Тип ошибки: «играть значение».", a:"Смешение паронимов: правильно — играть РОЛЬ или иметь ЗНАЧЕНИЕ"},
    {v:200, q:"Тип ошибки: «большая половина студентов».", a:"Логическая ошибка: половина не может быть большей/меньшей"},
    {v:300, q:"Тип ошибки: «Он вернулся с Москвы».", a:"Правильно: из Москвы (предлог «с» — неверный)"},
    {v:400, q:"Тип ошибки: «Молодой юноша учился в вузе».", a:"Плеоназм (юноша и так молодой)"},
    {v:500, q:"Тип ошибки: «Группа студентов во главе со старостой приняли решение».", a:"Правильно: принялА (согласование с «группа», а не со «студентов»)"}
  ]},
  { title:"Рандом", q:[
    {v:100, q:"Какие буквы исключили из алфавита в 1918 году? Назовите три.", a:"I (и десятеричное), Ѣ (ять), Ѳ (фита), а также Ѵ (ижица)"},
    {v:200, q:"Что такое «палатализация»?", a:"Смягчение согласных (например, перед гласными переднего ряда)"},
    {v:300, q:"Откуда взялось слово «сорок» вместо «четыре десятка»?", a:"От древнерусского «сорокъ» — связка из 40 шкурок соболя (мера счёта)"},
    {v:400, q:"Что такое «аканье» и «оканье»?", a:"Тип произношения безударных гласных: аканье — мАлАко (центр/юг), оканье — мОлОко (север)"},
    {v:500, q:"Что такое «неполногласие»? Пример.", a:"Сочетание «ра», «ла» в старославянском: град, глад, млеко — соответствуют русским полногласным город, голод, молоко"}
  ]}
];

const SPECIALS = {
  cat:{ tag:"🐱 Кот в мешке", q:"Расставьте знаки и объясните: «Он не то чтобы умён но и не глуп».", a:"Он не то чтобы умён, но и не глуп. (перед «но» запятая, внутри «не то чтобы» — нет)" },
  auc:{ tag:"💰 Аукцион", q:"Назовите ВСЕ случаи, когда после шипящих в корне пишется Ё (а не О).", a:"Только при чередовании с Е в однокоренных словах или формах: шёпот — шепчет, жёлудь — желудей, щёлка — щель, чёрт — черти, шёл — шедший. В заимствованиях и без чередования — О: шов, шорник, крыжовник, трущоба, чащоба, обжора" }
};

const catsEl = document.getElementById('cats');
const boardEl = document.getElementById('board');
const modal = document.getElementById('modal');
const tagEl = document.getElementById('tag');
const qEl = document.getElementById('qText');
const aBox = document.getElementById('aBox');
const aEl = document.getElementById('aText');
const revealBtn = document.getElementById('revealBtn');
const startBtn = document.getElementById('startBtn');
const timerWrap = document.getElementById('timerWrap');
const timerFill = document.getElementById('timerFill');
const timerNum = document.getElementById('timerNum');

let currentCell = null;
let timerId = null;
let timeLeft = 30;
const TOTAL_TIME = 30;

DATA.forEach(c=>{
  const h = document.createElement('div');
  h.className='cat'; h.textContent=c.title;
  catsEl.appendChild(h);
});

DATA.forEach(cat=>{
  cat.q.forEach(item=>{
    const d = document.createElement('div');
    d.className='cell';
    d.textContent = item.v;
    d.onclick = ()=>openQ(item, d, cat.title);
    boardEl.appendChild(d);
  });
});

// ---- Звуки через Web Audio ----
function beep(freq, dur, vol){
  try{
    const ctx = new (window.AudioContext || window.webkitAudioContext)();
    const o = ctx.createOscillator();
    const g = ctx.createGain();
    o.frequency.value = freq;
    o.type = 'sine';
    g.gain.value = vol || 0.15;
    o.connect(g); g.connect(ctx.destination);
    o.start();
    o.stop(ctx.currentTime + dur);
  }catch(e){}
}
function soundDing(){ beep(880,0.15,0.15); setTimeout(()=>beep(1320,0.25,0.12),120); }
function soundTick(){ beep(440,0.06,0.08); }
function soundTimeout(){ beep(220,0.4,0.2); setTimeout(()=>beep(180,0.5,0.18),400); }

function openQ(item, cell, catTitle){
  if(cell.classList.contains('used')) return;
  currentCell = cell;
  tagEl.textContent = catTitle + ' · ' + item.v;
  qEl.textContent = item.q;
  aEl.textContent = item.a;
  aBox.classList.remove('on');
  revealBtn.style.display = 'inline-block';
  startBtn.style.display = 'inline-block';
  timerWrap.classList.remove('on');
  stopTimer();
  modal.classList.add('on');
}

function startTimer(){
  stopTimer();
  timeLeft = TOTAL_TIME;
  timerWrap.classList.add('on');
  timerFill.style.width = '100%';
  timerFill.classList.remove('danger');
  timerNum.textContent = timeLeft;
  startBtn.style.display = 'none';
  timerId = setInterval(()=>{
    timeLeft--;
    timerNum.textContent = timeLeft;
    timerFill.style.width = (timeLeft/TOTAL_TIME*100)+'%';
    if(timeLeft <= 5){ timerFill.classList.add('danger'); soundTick(); }
    if(timeLeft <= 0){
      stopTimer();
      soundTimeout();
      timerNum.textContent = 'ВРЕМЯ!';
    }
  },1000);
}

function stopTimer(){
  if(timerId){ clearInterval(timerId); timerId = null; }
}

function reveal(){
  aBox.classList.add('on');
  revealBtn.style.display = 'none';
  startBtn.style.display = 'none';
  stopTimer();
  soundDing();
}

function closeModal(){
  stopTimer();
  modal.classList.remove('on');
  if(currentCell) currentCell.classList.add('used');
  currentCell = null;
}

function special(type){
  const s = SPECIALS[type];
  currentCell = null;
  tagEl.textContent = s.tag;
  qEl.textContent = s.q;
  aEl.textContent = s.a;
  aBox.classList.remove('on');
  revealBtn.style.display = 'inline-block';
  startBtn.style.display = 'inline-block';
  timerWrap.classList.remove('on');
  stopTimer();
  modal.classList.add('on');
}

document.addEventListener('keydown', e=>{
  if(e.key === 'Escape') closeModal();
  if(e.key === ' ' && modal.classList.contains('on')){
    e.preventDefault();
    if(!aBox.classList.contains('on')) reveal();
  }
  if(e.key === 'Enter' && modal.classList.contains('on') && startBtn.style.display !== 'none'){
    e.preventDefault();
    startTimer();
  }
});

// ---- Счёт команд ----
const TEAMS = ['Команда 1','Команда 2','Команда 3'];
const scoresEl = document.getElementById('scores');
let scores = [0,0,0];

function renderScores(){
  scoresEl.innerHTML = '';
  TEAMS.forEach((name,i)=>{
    const d = document.createElement('div');
    d.className='team';
    d.innerHTML = '<div class="name">'+name+'</div>'+
                  '<div class="pts">'+scores[i]+'</div>'+
                  '<div class="btns">'+
                    '<button class="minus" data-i="'+i+'" data-d="-100">−100</button>'+
                    '<button data-i="'+i+'" data-d="100">+100</button>'+
                    '<button data-i="'+i+'" data-d="200">+200</button>'+
                  '</div>';
    scoresEl.appendChild(d);
  });
  scoresEl.querySelectorAll('button').forEach(b=>{
    b.onclick = ()=>{
      scores[+b.dataset.i] += +b.dataset.d;
      renderScores();
    };
  });
}
renderScores();

function resetGame(){
  scores = [0,0,0];
  renderScores();
  document.querySelectorAll('.cell').forEach(c=>c.classList.remove('used'));
}
</script>
</body>
</html>
