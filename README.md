<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>gazagym</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@500;700;900&family=IBM+Plex+Sans+Arabic:wght@400;500;600&family=JetBrains+Mono:wght@600;700&display=swap" rel="stylesheet">
<style>
:root{
  --bg:#12181F; --surface:#1B232C; --surface2:#232D38; --surface3:#2B3644;
  --line:#2E3944; --line-strong:#3C4956;
  --text:#F1F4F6; --text2:#93A0AC; --text3:#5E6A75;
  --green:#2FA37A; --green-dim:#1B3A30; --green-text:#7FD9B6;
  --amber:#E0A93E; --amber-dim:#40331A; --amber-text:#F0C876;
  --red:#D9503F; --red-dim:#3E211C; --red-text:#F09384;
  --radius:14px;
}
*{box-sizing:border-box;}
body{
  margin:0; background:var(--bg); color:var(--text);
  font-family:'IBM Plex Sans Arabic',sans-serif;
  min-height:100vh; padding-bottom:60px;
}
.mono{font-family:'JetBrains Mono',monospace;}
.display{font-family:'Tajawal',sans-serif;}
input,select,button,textarea{font-family:inherit;}

/* ===== قفل الدخول ===== */
#lockScreen{
  min-height:100vh; display:flex; align-items:center; justify-content:center;
  flex-direction:column; gap:22px; padding:24px; text-align:center;
}
.lock-icon{
  width:64px; height:64px; border-radius:50%; background:var(--surface2);
  display:flex; align-items:center; justify-content:center; font-size:28px;
  border:1px solid var(--line-strong);
}
.pin-dots{display:flex; gap:14px; justify-content:center; margin:8px 0;}
.pin-dot{width:16px; height:16px; border-radius:50%; border:2px solid var(--line-strong); transition:.15s;}
.pin-dot.filled{background:var(--green); border-color:var(--green);}
.keypad{display:grid; grid-template-columns:repeat(3,72px); gap:14px; margin-top:8px;}
.key{
  width:72px; height:72px; border-radius:50%; border:1px solid var(--line);
  background:var(--surface); color:var(--text); font-size:22px; font-weight:600;
  cursor:pointer; display:flex; align-items:center; justify-content:center;
}
.key:active{background:var(--surface3);}
.key.wide{background:transparent; border:none; font-size:15px; color:var(--text2);}
#lockError{color:var(--red-text); font-size:14px; min-height:20px;}
#lockSub{color:var(--text2); font-size:14px; max-width:280px;}

/* ===== التطبيق ===== */
#app{display:none; max-width:640px; margin:0 auto; padding:20px 16px;}
header{display:flex; align-items:center; justify-content:space-between; margin-bottom:18px;}
header h1{font-family:'Tajawal',sans-serif; font-size:22px; font-weight:900; margin:0;}
header .sub{font-size:13px; color:var(--text2); margin-top:2px;}
.icon-btn{
  background:var(--surface); border:1px solid var(--line); color:var(--text2);
  width:38px; height:38px; border-radius:10px; cursor:pointer; font-size:16px;
  display:flex; align-items:center; justify-content:center;
}

.stats{display:grid; grid-template-columns:repeat(3,1fr); gap:10px; margin-bottom:18px;}
.stat{background:var(--surface); border-radius:var(--radius); padding:14px 10px; text-align:center; border:1px solid var(--line);}
.stat .num{font-family:'JetBrains Mono',monospace; font-size:26px; font-weight:700;}
.stat .lbl{font-size:12px; color:var(--text2); margin-top:4px;}
.stat.warn .num{color:var(--amber-text);}
.stat.danger .num{color:var(--red-text);}
.stat.ok .num{color:var(--green-text);}

.alertbar{
  background:var(--red-dim); border:1px solid #5A2D24; border-radius:var(--radius);
  padding:12px 14px; margin-bottom:16px; font-size:13.5px; color:var(--red-text);
  display:flex; align-items:center; gap:10px;
}

.toolbar{display:flex; gap:8px; margin-bottom:14px;}
.toolbar input[type=search]{
  flex:1; background:var(--surface); border:1px solid var(--line); border-radius:10px;
  padding:10px 12px; color:var(--text); font-size:14px;
}
.toolbar input[type=search]:focus{outline:none; border-color:var(--line-strong);}
.addbtn{
  background:var(--green); color:#0C2019; border:none; border-radius:10px;
  padding:0 16px; font-size:14px; font-weight:600; cursor:pointer; white-space:nowrap;
}

.tabs{display:flex; gap:6px; margin-bottom:16px; overflow-x:auto;}
.tab{
  background:var(--surface); border:1px solid var(--line); color:var(--text2);
  padding:7px 14px; border-radius:20px; font-size:13px; cursor:pointer; white-space:nowrap;
}
.tab.active{background:var(--surface3); color:var(--text); border-color:var(--line-strong);}

.member{
  background:var(--surface); border:1px solid var(--line); border-radius:var(--radius);
  padding:14px; margin-bottom:10px; display:flex; align-items:center; gap:10px;
}
.member .avatar{
  width:44px; height:44px; border-radius:50%; flex-shrink:0; display:flex;
  align-items:center; justify-content:center; font-family:'Tajawal',sans-serif;
  font-weight:700; font-size:16px; color:#fff; overflow:hidden;
}
.member .avatar img{width:100%; height:100%; object-fit:cover;}
.member .badge{
  width:52px; height:52px; border-radius:50%; display:flex; flex-direction:column;
  align-items:center; justify-content:center; flex-shrink:0; border:1px solid;
}
.badge .d{font-family:'JetBrains Mono',monospace; font-size:15px; font-weight:700; line-height:1;}
.badge .u{font-size:8.5px; margin-top:2px;}
.badge.ok{background:var(--green-dim); border-color:#204838; color:var(--green-text);}
.badge.warn{background:var(--amber-dim); border-color:#5A4620; color:var(--amber-text);}
.badge.danger{background:var(--red-dim); border-color:#5A2D24; color:var(--red-text);}

.member .info{flex:1; min-width:0;}
.member .name{font-size:15px; font-weight:600; margin:0 0 3px; overflow:hidden; text-overflow:ellipsis; white-space:nowrap;}
.member .meta{font-size:12px; color:var(--text2); display:flex; gap:8px; flex-wrap:wrap;}
.member .actions{display:flex; flex-direction:column; gap:6px; flex-shrink:0;}
.member .actions button{
  background:var(--surface2); border:1px solid var(--line); color:var(--text2);
  width:30px; height:30px; border-radius:8px; cursor:pointer; font-size:13px;
}
.member .actions button.renew{color:var(--green-text); border-color:#204838;}
.empty{text-align:center; color:var(--text3); padding:50px 20px; font-size:14px;}

/* ===== صورة العضو في النموذج ===== */
.photo-picker{display:flex; align-items:center; gap:14px;}
.photo-preview{
  width:64px; height:64px; border-radius:50%; flex-shrink:0; display:flex;
  align-items:center; justify-content:center; font-family:'Tajawal',sans-serif;
  font-weight:700; font-size:22px; color:#fff; overflow:hidden; border:1px solid var(--line);
}
.photo-preview img{width:100%; height:100%; object-fit:cover;}
.photo-btns{display:flex; gap:8px;}
.photo-btns label, .photo-btns button{
  background:var(--surface2); border:1px solid var(--line); color:var(--text2);
  padding:8px 12px; border-radius:9px; font-size:12.5px; cursor:pointer;
}
#fPhotoInput{display:none;}

/* ===== النافذة المنبثقة ===== */
.overlay{
  display:none; position:fixed; inset:0; background:rgba(0,0,0,.55);
  align-items:flex-end; justify-content:center; z-index:50;
}
.overlay.show{display:flex;}
.sheet{
  background:var(--surface); width:100%; max-width:640px; border-radius:20px 20px 0 0;
  padding:22px 18px 26px; max-height:88vh; overflow-y:auto;
}
.sheet h2{font-family:'Tajawal',sans-serif; font-size:18px; margin:0 0 16px;}
.field{margin-bottom:14px;}
.field label{display:block; font-size:13px; color:var(--text2); margin-bottom:6px;}
.field input, .field select{
  width:100%; background:var(--surface2); border:1px solid var(--line); border-radius:10px;
  padding:11px 12px; color:var(--text); font-size:14.5px;
}
.field input:focus, .field select:focus{outline:none; border-color:var(--green);}
.durations{display:grid; grid-template-columns:repeat(3,1fr); gap:8px;}
.dur-opt{
  background:var(--surface2); border:1px solid var(--line); border-radius:10px;
  padding:10px 4px; text-align:center; font-size:13px; cursor:pointer; color:var(--text2);
}
.dur-opt.sel{background:var(--green-dim); border-color:var(--green); color:var(--green-text);}
.sheet-actions{display:flex; gap:10px; margin-top:18px;}
.btn{flex:1; padding:13px; border-radius:11px; border:none; font-size:14.5px; font-weight:600; cursor:pointer;}
.btn.primary{background:var(--green); color:#0C2019;}
.btn.ghost{background:var(--surface2); color:var(--text2); border:1px solid var(--line);}
.btn.danger-txt{background:transparent; color:var(--red-text); border:1px solid #5A2D24;}
</style>
</head>
<body>

<div id="lockScreen">
  <div class="lock-icon">🔒</div>
  <div>
    <div class="display" style="font-size:20px;font-weight:900;" id="lockTitle">أدخل رمز الدخول</div>
    <div id="lockSub">هذا الرمز يحمي سجل النادي من أي تعديل غير مصرح به</div>
  </div>
  <div class="pin-dots" id="pinDots"></div>
  <div id="lockError"></div>
  <div class="keypad" id="keypad"></div>
</div>

<div id="app">
  <header>
    <div>
      <h1 id="clubName">gazagym</h1>
      <div class="sub" id="todayLabel"></div>
    </div>
    <button class="icon-btn" onclick="lockApp()" aria-label="قفل" title="قفل">🔒</button>
  </header>

  <div class="stats">
    <div class="stat ok"><div class="num mono" id="statTotal">0</div><div class="lbl">إجمالي الأعضاء</div></div>
    <div class="stat warn"><div class="num mono" id="statSoon">0</div><div class="lbl">تنتهي قريباً</div></div>
    <div class="stat danger"><div class="num mono" id="statExpired">0</div><div class="lbl">منتهية</div></div>
  </div>

  <div id="alertbarWrap"></div>

  <div class="toolbar">
    <input type="search" id="searchBox" placeholder="ابحث بالاسم أو الرقم..." oninput="render()">
    <button class="addbtn" onclick="openForm()">+ تسجيل عضو</button>
  </div>

  <div class="tabs" id="tabs"></div>

  <div id="memberList"></div>
</div>

<div class="overlay" id="overlay">
  <div class="sheet">
    <h2 id="formTitle">تسجيل عضو جديد</h2>
    <div id="basicFields">
      <div class="field">
        <label>صورة العضو (اختياري)</label>
        <div class="photo-picker">
          <div class="photo-preview" id="photoPreview"></div>
          <div class="photo-btns">
            <label for="fPhotoInput">اختر صورة</label>
            <button type="button" onclick="removePhoto()">إزالة</button>
          </div>
          <input type="file" id="fPhotoInput" accept="image/*" onchange="handlePhotoSelect(event)">
        </div>
      </div>
      <div class="field">
        <label>اسم العضو</label>
        <input type="text" id="fName" placeholder="مثال: محمد ولد أحمد" oninput="updatePhotoPreview()">
      </div>
      <div class="field">
        <label>رقم الجوال</label>
        <input type="tel" id="fPhone" placeholder="مثال: 22123456">
      </div>
    </div>
    <div class="field" id="renewNote" style="display:none;">
      <div style="background:var(--surface2); border:1px solid var(--line); border-radius:10px; padding:10px 12px; font-size:13px; color:var(--text2);">
        🔄 تجديد اشتراك <b id="renewName" style="color:var(--text);"></b> — اختر المدة الجديدة وتاريخ البدء
      </div>
    </div>
    <div id="formError" style="display:none; color:var(--red-text); background:var(--red-dim); border:1px solid #5A2D24; border-radius:10px; padding:10px 12px; font-size:13px; margin-bottom:14px;"></div>
    <div class="field">
      <label>تاريخ التسجيل</label>
      <input type="date" id="fRegDate">
    </div>
    <div class="field">
      <label>مدة الاشتراك</label>
      <div class="durations" id="durOpts"></div>
    </div>
    <div class="field" id="customDateWrap" style="display:none;">
      <label>تاريخ الانتهاء</label>
      <input type="date" id="fCustomExpiry">
    </div>
    <div class="sheet-actions">
      <button class="btn ghost" onclick="closeForm()">إلغاء</button>
      <button class="btn primary" onclick="saveMember()">حفظ</button>
    </div>
    <div class="sheet-actions" id="deleteRow" style="display:none;">
      <button class="btn danger-txt" style="flex:1" onclick="askDeleteConfirm()">حذف العضو نهائياً</button>
    </div>
  </div>
</div>

<div class="overlay" id="confirmOverlay">
  <div class="sheet" style="max-width:400px;">
    <h2>تأكيد الحذف</h2>
    <p style="color:var(--text2); font-size:14px; margin:0 0 18px;">هل أنت متأكد من حذف <b id="confirmDeleteName" style="color:var(--text);"></b> نهائياً؟ لا يمكن التراجع عن هذا الإجراء.</p>
    <div class="sheet-actions">
      <button class="btn ghost" onclick="closeDeleteConfirm()">إلغاء</button>
      <button class="btn" style="background:var(--red); color:#fff;" onclick="confirmDelete()">نعم، احذف</button>
    </div>
  </div>
</div>

<script>
let members = [];
let pin = null;
let enteredPin = '';
let currentFilter = 'all';
let editingId = null;
let selectedDurationDays = 30;
let unlocked = false;
let renewMode = false;
let currentPhoto = null;

const avatarColors = ['#2FA37A','#3B7DD8','#C25FA6','#D9903F','#7B6FD9','#D9503F','#3FA0A8'];
function colorFor(name){
  let h = 0;
  for(let i=0;i<name.length;i++) h = name.charCodeAt(i) + ((h<<5)-h);
  return avatarColors[Math.abs(h) % avatarColors.length];
}
function firstLetter(name){
  return (name||'?').trim().charAt(0).toUpperCase() || '?';
}

const durations = [
  {label:'شهر', days:30},
  {label:'شهرين', days:60},
  {label:'3 أشهر', days:90},
  {label:'6 أشهر', days:180},
  {label:'سنة', days:365},
  {label:'تاريخ مخصص', days:'custom'}
];

function todayStr(){
  const d = new Date();
  return d.toISOString().slice(0,10);
}

function daysBetween(a,b){
  const A = new Date(a+'T00:00:00');
  const B = new Date(b+'T00:00:00');
  return Math.round((B-A)/86400000);
}

function fmtDate(iso){
  const d = new Date(iso+'T00:00:00');
  return d.toLocaleDateString('ar-EG',{day:'numeric',month:'short',year:'numeric'});
}

/* ===== تخزين دائم ===== */
async function loadData(){
  try{
    const r = await window.storage.get('members-list');
    members = r ? JSON.parse(r.value) : [];
  }catch(e){ members = []; }
  try{
    const r = await window.storage.get('admin-pin');
    pin = r ? r.value : null;
  }catch(e){ pin = null; }
}
async function saveMembers(){
  try{ await window.storage.set('members-list', JSON.stringify(members)); }
  catch(e){ console.error('فشل الحفظ', e); }
}
async function savePin(p){
  try{ await window.storage.set('admin-pin', p); }
  catch(e){ console.error('فشل حفظ الرمز', e); }
}

/* ===== شاشة القفل ===== */
function buildKeypad(){
  const kp = document.getElementById('keypad');
  kp.innerHTML = '';
  const layout = ['1','2','3','4','5','6','7','8','9','⌫','0','✓'];
  layout.forEach(k=>{
    const btn = document.createElement('button');
    btn.className = 'key' + (k==='⌫'||k==='✓' ? ' wide':'');
    btn.textContent = k;
    btn.onclick = ()=>keyPress(k);
    kp.appendChild(btn);
  });
}
function renderDots(){
  const wrap = document.getElementById('pinDots');
  wrap.innerHTML = '';
  for(let i=0;i<4;i++){
    const dot = document.createElement('div');
    dot.className = 'pin-dot' + (i<enteredPin.length?' filled':'');
    wrap.appendChild(dot);
  }
}
function keyPress(k){
  document.getElementById('lockError').textContent = '';
  if(k==='⌫'){ enteredPin = enteredPin.slice(0,-1); renderDots(); return; }
  if(k==='✓'){ submitPin(); return; }
  if(enteredPin.length<4){ enteredPin += k; renderDots(); }
  if(enteredPin.length===4){ setTimeout(submitPin, 150); }
}
async function submitPin(){
  if(enteredPin.length!==4) return;
  if(!pin){
    if(!window._settingUp){
      window._settingUp = enteredPin;
      document.getElementById('lockTitle').textContent = 'أعد إدخال الرمز للتأكيد';
      document.getElementById('lockSub').textContent = 'اكتب نفس الرمز مرة أخرى';
      enteredPin = ''; renderDots();
      return;
    } else {
      if(window._settingUp === enteredPin){
        pin = enteredPin;
        await savePin(pin);
        enterApp();
      } else {
        document.getElementById('lockError').textContent = 'الرمزان غير متطابقين، حاول من جديد';
        window._settingUp = null;
        document.getElementById('lockTitle').textContent = 'أدخل رمز الدخول';
        document.getElementById('lockSub').textContent = 'هذا الرمز يحمي سجل النادي من أي تعديل غير مصرح به';
        enteredPin = ''; renderDots();
      }
    }
  } else {
    if(enteredPin === pin){
      enterApp();
    } else {
      document.getElementById('lockError').textContent = 'رمز غير صحيح';
      enteredPin = ''; renderDots();
    }
  }
}
function enterApp(){
  unlocked = true;
  document.getElementById('lockScreen').style.display = 'none';
  document.getElementById('app').style.display = 'block';
  document.getElementById('todayLabel').textContent = fmtDate(todayStr());
  render();
}
function lockApp(){
  unlocked = false;
  enteredPin = '';
  window._settingUp = null;
  document.getElementById('lockTitle').textContent = 'أدخل رمز الدخول';
  document.getElementById('lockSub').textContent = 'هذا الرمز يحمي سجل النادي من أي تعديل غير مصرح به';
  renderDots();
  document.getElementById('app').style.display = 'none';
  document.getElementById('lockScreen').style.display = 'flex';
}

/* ===== المنطق الأساسي ===== */
function statusOf(m){
  const d = daysBetween(todayStr(), m.expiryDate);
  if(d < 0) return 'danger';
  if(d <= 3) return 'warn';
  return 'ok';
}
function daysLabel(m){
  const d = daysBetween(todayStr(), m.expiryDate);
  if(d < 0) return {n: Math.abs(d), u: 'يوم تأخير'};
  if(d === 0) return {n: 0, u: 'ينتهي اليوم'};
  return {n: d, u: 'يوم متبقي'};
}

function buildTabs(){
  const tabsData = [
    {k:'all', l:'الكل'},
    {k:'ok', l:'نشط'},
    {k:'warn', l:'قرب الانتهاء'},
    {k:'danger', l:'منتهي'}
  ];
  const wrap = document.getElementById('tabs');
  wrap.innerHTML = '';
  tabsData.forEach(t=>{
    const b = document.createElement('button');
    b.className = 'tab' + (currentFilter===t.k?' active':'');
    b.textContent = t.l;
    b.onclick = ()=>{ currentFilter = t.k; render(); };
    wrap.appendChild(b);
  });
}

function render(){
  buildTabs();
  const q = document.getElementById('searchBox').value.trim();
  let list = members.map(m=>({...m, _status: statusOf(m)}));

  const total = list.length;
  const soon = list.filter(m=>m._status==='warn').length;
  const expired = list.filter(m=>m._status==='danger').length;
  document.getElementById('statTotal').textContent = total;
  document.getElementById('statSoon').textContent = soon;
  document.getElementById('statExpired').textContent = expired;

  const alertWrap = document.getElementById('alertbarWrap');
  if(expired>0 || soon>0){
    alertWrap.innerHTML = `<div class="alertbar">⚠️ لديك ${expired>0?expired+' عضو منتهي الاشتراك':''}${expired>0&&soon>0?' و ':''}${soon>0?soon+' عضو قريب من الانتهاء':''}</div>`;
  } else {
    alertWrap.innerHTML = '';
  }

  if(currentFilter !== 'all') list = list.filter(m=>m._status===currentFilter);
  if(q) list = list.filter(m=> m.name.includes(q) || (m.phone||'').includes(q));

  list.sort((a,b)=> daysBetween(todayStr(),a.expiryDate) - daysBetween(todayStr(),b.expiryDate));

  const container = document.getElementById('memberList');
  if(list.length===0){
    container.innerHTML = `<div class="empty">لا يوجد أعضاء هنا حالياً<br>اضغط "تسجيل عضو" لإضافة أول اشتراك</div>`;
    return;
  }
  container.innerHTML = list.map(m=>{
    const dl = daysLabel(m);
    const avatarHtml = m.photo
      ? `<img src="${m.photo}" alt="">`
      : firstLetter(m.name);
    return `
    <div class="member">
      <div class="avatar" style="background:${colorFor(m.name)}">${avatarHtml}</div>
      <div class="badge ${m._status}">
        <div class="d">${dl.n}</div>
        <div class="u">${dl.u}</div>
      </div>
      <div class="info">
        <p class="name">${escapeHtml(m.name)}</p>
        <div class="meta">
          <span>📞 ${escapeHtml(m.phone||'—')}</span>
          <span>سُجّل: ${fmtDate(m.regDate)}</span>
          <span>ينتهي: ${fmtDate(m.expiryDate)}</span>
        </div>
      </div>
      <div class="actions">
        <button onclick="openForm('${m.id}')" title="تعديل" aria-label="تعديل">✏️</button>
        <button class="renew" onclick="openRenew('${m.id}')" title="تجديد الاشتراك" aria-label="تجديد">🔄</button>
      </div>
    </div>`;
  }).join('');
}

function escapeHtml(s){
  const d = document.createElement('div');
  d.textContent = s;
  return d.innerHTML;
}

/* ===== نموذج الإضافة/التعديل ===== */
function buildDurationOptions(){
  const wrap = document.getElementById('durOpts');
  wrap.innerHTML = '';
  durations.forEach(d=>{
    const el = document.createElement('div');
    el.className = 'dur-opt' + (d.days===selectedDurationDays?' sel':'');
    el.textContent = d.label;
    el.onclick = ()=>{
      selectedDurationDays = d.days;
      document.getElementById('customDateWrap').style.display = d.days==='custom' ? 'block':'none';
      buildDurationOptions();
    };
    wrap.appendChild(el);
  });
}

function updatePhotoPreview(){
  const preview = document.getElementById('photoPreview');
  const name = document.getElementById('fName').value;
  preview.style.background = colorFor(name || '?');
  if(currentPhoto){
    preview.innerHTML = `<img src="${currentPhoto}" alt="">`;
  } else {
    preview.innerHTML = firstLetter(name);
  }
}
function handlePhotoSelect(e){
  const file = e.target.files[0];
  if(!file) return;
  const reader = new FileReader();
  reader.onload = function(ev){
    const img = new Image();
    img.onload = function(){
      const size = 200;
      const canvas = document.createElement('canvas');
      canvas.width = size; canvas.height = size;
      const ctx = canvas.getContext('2d');
      const s = Math.min(img.width, img.height);
      const sx = (img.width - s)/2, sy = (img.height - s)/2;
      ctx.drawImage(img, sx, sy, s, s, 0, 0, size, size);
      currentPhoto = canvas.toDataURL('image/jpeg', 0.75);
      updatePhotoPreview();
    };
    img.src = ev.target.result;
  };
  reader.readAsDataURL(file);
}
function removePhoto(){
  currentPhoto = null;
  document.getElementById('fPhotoInput').value = '';
  updatePhotoPreview();
}

function openForm(id){
  editingId = id || null;
  renewMode = false;
  document.getElementById('formTitle').textContent = id ? 'تعديل بيانات العضو' : 'تسجيل عضو جديد';
  document.getElementById('deleteRow').style.display = id ? 'flex' : 'none';
  document.getElementById('basicFields').style.display = 'block';
  document.getElementById('renewNote').style.display = 'none';
  document.getElementById('formError').style.display = 'none';
  selectedDurationDays = 30;
  document.getElementById('customDateWrap').style.display = 'none';

  if(id){
    const m = members.find(x=>x.id===id);
    document.getElementById('fName').value = m.name;
    document.getElementById('fPhone').value = m.phone||'';
    document.getElementById('fRegDate').value = m.regDate;
    document.getElementById('fCustomExpiry').value = m.expiryDate;
    currentPhoto = m.photo || null;
    selectedDurationDays = 'custom';
  } else {
    document.getElementById('fName').value = '';
    document.getElementById('fPhone').value = '';
    document.getElementById('fRegDate').value = todayStr();
    document.getElementById('fCustomExpiry').value = '';
    currentPhoto = null;
  }
  document.getElementById('fPhotoInput').value = '';
  updatePhotoPreview();
  buildDurationOptions();
  document.getElementById('overlay').classList.add('show');
}

function openRenew(id){
  editingId = id;
  renewMode = true;
  const m = members.find(x=>x.id===id);
  document.getElementById('formTitle').textContent = 'تجديد الاشتراك';
  document.getElementById('deleteRow').style.display = 'none';
  document.getElementById('basicFields').style.display = 'none';
  document.getElementById('renewNote').style.display = 'block';
  document.getElementById('formError').style.display = 'none';
  document.getElementById('renewName').textContent = m.name;

  document.getElementById('fName').value = m.name;
  document.getElementById('fPhone').value = m.phone||'';
  currentPhoto = m.photo || null;
  document.getElementById('fRegDate').value = todayStr();
  document.getElementById('fCustomExpiry').value = '';
  selectedDurationDays = 30;
  document.getElementById('customDateWrap').style.display = 'none';
  buildDurationOptions();
  document.getElementById('overlay').classList.add('show');
}

function closeForm(){
  document.getElementById('overlay').classList.remove('show');
}

async function saveMember(){
  const errBox = document.getElementById('formError');
  errBox.style.display = 'none';
  const name = document.getElementById('fName').value.trim();
  const phone = document.getElementById('fPhone').value.trim();
  const regDate = document.getElementById('fRegDate').value || todayStr();
  if(!name){ errBox.textContent = 'اكتب اسم العضو أولاً'; errBox.style.display = 'block'; return; }

  let expiryDate;
  if(selectedDurationDays === 'custom'){
    expiryDate = document.getElementById('fCustomExpiry').value;
    if(!expiryDate){ errBox.textContent = 'اختر تاريخ انتهاء'; errBox.style.display = 'block'; return; }
  } else {
    const d = new Date(regDate+'T00:00:00');
    d.setDate(d.getDate() + selectedDurationDays);
    expiryDate = d.toISOString().slice(0,10);
  }

  if(editingId){
    const m = members.find(x=>x.id===editingId);
    m.regDate = regDate; m.expiryDate = expiryDate;
    if(!renewMode){
      m.name = name; m.phone = phone; m.photo = currentPhoto;
    }
  } else {
    members.push({
      id: 'm_' + Date.now() + '_' + Math.random().toString(36).slice(2,7),
      name, phone, regDate, expiryDate, photo: currentPhoto
    });
  }
  await saveMembers();
  closeForm();
  render();
}

function askDeleteConfirm(){
  if(!editingId) return;
  const m = members.find(x=>x.id===editingId);
  document.getElementById('confirmDeleteName').textContent = m ? m.name : '';
  document.getElementById('confirmOverlay').classList.add('show');
}
function closeDeleteConfirm(){
  document.getElementById('confirmOverlay').classList.remove('show');
}
async function confirmDelete(){
  if(!editingId) return;
  members = members.filter(x=>x.id!==editingId);
  await saveMembers();
  closeDeleteConfirm();
  closeForm();
  render();
}

/* ===== بدء التشغيل ===== */
(async function init(){
  buildKeypad();
  renderDots();
  await loadData();
  if(!pin){
    document.getElementById('lockSub').textContent = 'لا يوجد رمز بعد — أنشئ رمزاً من 4 أرقام لحماية السجل';
  }
})();
</script>
</body>
</html>
