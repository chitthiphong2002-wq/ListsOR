<!doctype html>
<html lang="th" class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>ListsOR</title>
  <script src="https://cdn.tailwindcss.com/3.4.17"></script>
  <script src="https://cdn.jsdelivr.net/npm/lucide@0.263.0/dist/umd/lucide.min.js"></script>
  <script src="/_sdk/element_sdk.js"></script>
  <script src="/_sdk/data_sdk.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&amp;family=Syne:wght@400;600;700;800&amp;display=swap" rel="stylesheet">
  <script>
tailwind.config = {
  theme: {
    extend: {
      fontFamily: {
        display: ['Syne', 'sans-serif'],
        mono: ['Space Mono', 'monospace']
      },
      colors: {
        neon: '#A855F7',
        lime: '#BFFF00',
        sunset: '#FF6B35',
        cream: '#FFFEF5',
        ink: '#1a1a1a'
      }
    }
  }
}
</script>
  <style>
html, body { height: 100%; margin: 0; }
.neo-border { border: 3px solid #1a1a1a; }
.neo-border-sm { border: 2px solid #1a1a1a; }
.neo-btn { border: 3px solid #1a1a1a; transition: all 0.1s; cursor: pointer; }
.neo-btn:active { transform: translate(1px, 1px); }
.drag-over { border-color: #A855F7 !important; background: rgba(168,85,247,0.1) !important; }
.dragging { opacity: 0.5; transform: scale(0.95); }
.step-item { transition: transform 0.15s, opacity 0.15s; }
@keyframes slideIn { from { opacity:0; transform: translateY(20px); } to { opacity:1; transform: translateY(0); } }
.animate-in { animation: slideIn 0.3s ease-out forwards; }
.color-dot { width: 28px; height: 28px; border-radius: 50%; border: 2px solid #1a1a1a; cursor: pointer; transition: transform 0.1s; }
.color-dot:hover { transform: scale(1.2); }
.color-dot.active { outline: 3px solid #A855F7; outline-offset: 2px; }
.modal-overlay { background: rgba(0,0,0,0.5); backdrop-filter: blur(4px); }
.toast { position: fixed; bottom: 24px; left: 50%; transform: translateX(-50%); z-index: 9999; padding: 12px 24px; border: 3px solid #1a1a1a; font-family: 'Space Mono', monospace; font-weight: 700; border-radius: 8px; animation: toastIn 0.3s ease-out, toastOut 0.3s 2s ease-in forwards; }
@keyframes toastIn { from { opacity:0; transform: translateX(-50%) translateY(20px); } to { opacity:1; transform: translateX(-50%) translateY(0); } }
@keyframes toastOut { to { opacity:0; transform: translateX(-50%) translateY(20px); } }
.folder-tab { border: 2px solid #1a1a1a; border-bottom: none; border-radius: 8px 8px 0 0; padding: 8px 16px; cursor: pointer; font-weight: 700; transition: all 0.15s; }
.folder-tab.active { background: #BFFF00; }
.folder-tab:not(.active):hover { background: #f0f0f0; }
input:focus, textarea:focus { outline: 3px solid #A855F7; outline-offset: 1px; }
</style>
  <style>body { box-sizing: border-box; }</style>
 </head>
 <body class="h-full bg-cream font-mono text-ink overflow-auto">
  <div id="app" class="w-full min-h-full flex flex-col">
   <!-- HEADER -->
   <header class="w-full px-4 sm:px-8 pt-6 pb-4">
    <div class="flex items-center justify-between flex-wrap gap-3">
     <div>
      <h1 id="mainTitle" class="font-display font-extrabold text-4xl sm:text-5xl md:text-6xl leading-none" style="background: linear-gradient(135deg, #A855F7, #FF6B35, #BFFF00); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text;">ListsOR</h1>
      <p id="mainSubtitle" class="font-mono text-sm mt-1 opacity-70">Surgical Template Manager</p>
     </div>
     <div class="flex items-center gap-2">
      <div class="relative">
       <input id="searchInput" type="text" placeholder="ค้นหา template..." class="neo-border-sm rounded-lg pl-10 pr-4 py-2 bg-white font-mono text-sm w-48 sm:w-64" oninput="handleSearch(this.value)"> <i data-lucide="search" class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 opacity-50"></i>
      </div><button onclick="openCreateModal()" class="neo-btn bg-lime rounded-lg px-4 py-2 font-display font-bold text-sm flex items-center gap-2"> <i data-lucide="plus" class="w-5 h-5"></i><span class="hidden sm:inline">สร้าง Template</span> </button>
     </div>
    </div>
   </header><!-- TEMPLATE LIST -->
   <section class="px-4 sm:px-8 py-4 flex-1">
    <div id="templateList" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
     <div id="emptyState" class="col-span-full flex flex-col items-center justify-center py-16 opacity-50">
      <i data-lucide="clipboard-list" class="w-16 h-16 mb-4"></i>
      <p class="font-display font-bold text-xl">ยังไม่มี Template</p>
      <p class="text-sm mt-1">กดปุ่ม "สร้าง Template" เพื่อเริ่มต้น</p>
     </div>
    </div>
   </section>
  </div><!-- CREATE/EDIT MODAL -->
  <div id="modal" class="fixed inset-0 z-50 hidden">
   <div class="modal-overlay absolute inset-0" onclick="closeModal()"></div>
   <div class="relative z-10 w-full h-full overflow-auto flex items-start justify-center py-8 px-4">
    <div class="neo-border rounded-2xl bg-cream w-full max-w-lg animate-in">
     <div class="p-5 border-b-2 border-ink flex items-center justify-between" style="background: linear-gradient(135deg, #A855F7 0%, #FF6B35 100%); border-radius: 14px 14px 0 0;">
      <div id="modalTitle" class="font-display font-extrabold text-xl text-white animate-in">
       สร้าง Template ใหม่
      </div><button onclick="closeModal()" class="text-white hover:scale-110 transition-transform"><i data-lucide="x" class="w-6 h-6"></i></button>
     </div>
     <div class="p-5 space-y-4 overflow-y-auto max-h-[60vh]">
      <div>
       <label for="inputSurgery" class="font-display font-bold text-sm block mb-1">ชื่อการผ่าตัด</label> <input id="inputSurgery" type="text" placeholder="เช่น Laparoscopic Cholecystectomy" class="w-full neo-border-sm rounded-lg px-3 py-2 bg-white text-sm">
      </div>
      <div>
       <label for="inputNotes" class="font-display font-bold text-sm block mb-1">หมายเหตุ (Notes)</label> <textarea id="inputNotes" placeholder="เพิ่มข้อมูลเพิ่มเติม..." class="w-full neo-border-sm rounded-lg px-3 py-2 bg-white text-sm h-20 resize-none"></textarea>
      </div>
      <div class="space-y-2">
       <p class="font-display font-bold text-sm">สีของ Template</p>
       <div id="colorPicker" class="flex flex-wrap gap-2">
        <!-- color dots rendered here -->
       </div>
      </div>
      <div class="space-y-2">
       <div class="flex items-center justify-between">
        <p class="font-display font-bold text-sm">ขั้นตอนการผ่าตัด</p><button onclick="addStep()" class="neo-btn bg-lime rounded-lg px-3 py-1 font-display font-bold text-xs flex items-center gap-1"><i data-lucide="plus" class="w-4 h-4"></i>เพิ่ม</button>
       </div>
       <div id="stepsList" class="space-y-2">
        <!-- steps rendered here -->
       </div>
      </div>
     </div>
     <div class="p-5 border-t-2 border-ink flex gap-2">
      <button onclick="closeModal()" class="neo-btn bg-white rounded-lg px-4 py-2 font-display font-bold text-sm flex-1">ยกเลิก</button> <button id="saveBtn" onclick="saveTemplate()" class="neo-btn bg-neon text-white rounded-lg px-4 py-2 font-display font-bold text-sm flex-1">บันทึก</button>
     </div>
    </div>
   </div><!-- DELETE CONFIRM -->
   <div id="deleteModal" class="fixed inset-0 z-50 hidden">
    <div class="modal-overlay absolute inset-0" onclick="closeDeleteModal()"></div>
    <div class="relative z-10 flex items-center justify-center h-full px-4">
     <div class="neo-border rounded-2xl bg-cream w-full max-w-sm p-6 animate-in fade-in-up text-center" style="animation-duration: 0.5s;">
      <i data-lucide="alert-triangle" class="w-12 h-12 text-sunset mx-auto mb-3"></i>
      <p class="font-display font-bold text-lg mb-1">ลบ Template นี้?</p>
      <p class="text-sm opacity-70 mb-4">การลบจะไม่สามารถย้อนกลับได้</p>
      <div class="flex gap-2">
       <button onclick="closeDeleteModal()" class="neo-btn bg-white rounded-lg px-4 py-2 font-display font-bold text-sm flex-1">ยกเลิก</button> <button id="confirmDeleteBtn" class="neo-btn bg-sunset text-white rounded-lg px-4 py-2 font-display font-bold text-sm flex-1">ลบเลย</button>
      </div>
     </div>
    </div>
   </div><!-- VIEW MODAL -->
   <div id="viewModal" class="fixed inset-0 z-50 hidden">
    <div class="modal-overlay absolute inset-0" onclick="closeViewModal()"></div>
    <div class="relative z-10 w-full h-full overflow-auto flex items-start justify-center py-8 px-4">
     <div class="neo-border rounded-2xl bg-cream w-full max-w-lg animate-in">
      <div id="viewHeader" class="p-5 border-b-2 border-ink flex items-center justify-between rounded-t-2xl" style="background: linear-gradient(135deg, #BFFF00, #A855F7);">
       <h2 id="viewTitle" class="font-display font-extrabold text-xl text-ink"></h2><button onclick="closeViewModal()" class="text-ink hover:scale-110 transition-transform"><i data-lucide="x" class="w-6 h-6"></i></button>
      </div>
      <div id="viewContent" class="p-5"></div>
      <div class="p-5 border-t-2 border-ink flex gap-2">
       <button onclick="closeViewModal()" class="neo-btn bg-white rounded-lg px-4 py-2 font-display font-bold text-sm flex-1">ปิด</button> <button id="viewEditBtn" class="neo-btn bg-neon text-white rounded-lg px-4 py-2 font-display font-bold text-sm flex-1">แก้ไข</button>
      </div>
     </div>
    </div>
   </div>
   <script>
// ===== STATE =====
let allTemplates = [];
let searchQuery = '';
let editingId = null;
let deletingRecord = null;
let modalSteps = [];
let selectedColor = '#A855F7';
let dragSrcIndex = null;

const COLORS = ['#A855F7','#BFFF00','#FF6B35','#3B82F6','#EF4444','#10B981','#F59E0B','#EC4899','#1a1a1a','#FFFFFF'];

// ===== DEFAULT CONFIG =====
const defaultConfig = {
  app_title: 'ListsOR',
  app_subtitle: 'Surgical Template Manager',
  background_color: '#FFFEF5',
  surface_color: '#FFFFFF',
  text_color: '#1a1a1a',
  primary_action_color: '#A855F7',
  secondary_action_color: '#BFFF00'
};

// ===== ELEMENT SDK =====
window.elementSdk.init({
  defaultConfig,
  onConfigChange: async (config) => {
    const t = config.app_title || defaultConfig.app_title;
    const s = config.app_subtitle || defaultConfig.app_subtitle;
    const bg = config.background_color || defaultConfig.background_color;
    const surf = config.surface_color || defaultConfig.surface_color;
    const txt = config.text_color || defaultConfig.text_color;
    const pri = config.primary_action_color || defaultConfig.primary_action_color;
    const sec = config.secondary_action_color || defaultConfig.secondary_action_color;

    document.getElementById('mainTitle').textContent = t;
    document.getElementById('mainSubtitle').textContent = s;
    document.body.style.backgroundColor = bg;
    document.body.style.color = txt;

    document.querySelectorAll('.neo-btn.bg-neon, .neo-border.bg-neon').forEach(el => el.style.backgroundColor = pri);
    document.querySelectorAll('.neo-btn.bg-lime').forEach(el => el.style.backgroundColor = sec);
  },
  mapToCapabilities: (config) => ({
    recolorables: [
      { get: () => config.background_color || defaultConfig.background_color, set: v => { config.background_color = v; window.elementSdk.setConfig({background_color:v}); }},
      { get: () => config.surface_color || defaultConfig.surface_color, set: v => { config.surface_color = v; window.elementSdk.setConfig({surface_color:v}); }},
      { get: () => config.text_color || defaultConfig.text_color, set: v => { config.text_color = v; window.elementSdk.setConfig({text_color:v}); }},
      { get: () => config.primary_action_color || defaultConfig.primary_action_color, set: v => { config.primary_action_color = v; window.elementSdk.setConfig({primary_action_color:v}); }},
      { get: () => config.secondary_action_color || defaultConfig.secondary_action_color, set: v => { config.secondary_action_color = v; window.elementSdk.setConfig({secondary_action_color:v}); }}
    ],
    borderables: [],
    fontEditable: {
      get: () => config.font_family || defaultConfig.font_family || 'Syne',
      set: v => { config.font_family = v; window.elementSdk.setConfig({font_family:v}); }
    },
    fontSizeable: undefined
  }),
  mapToEditPanelValues: (config) => new Map([
    ['app_title', config.app_title || defaultConfig.app_title],
    ['app_subtitle', config.app_subtitle || defaultConfig.app_subtitle]
  ])
});

// ===== DATA SDK =====
const dataHandler = {
  onDataChanged(data) {
    allTemplates = data;
    renderTemplates();
  }
};

(async () => {
  const r = await window.dataSdk.init(dataHandler);
  if (!r.isOk) console.error('Data SDK init failed');
})();

// ===== TOAST =====
function showToast(msg, bg = '#BFFF00') {
  const t = document.createElement('div');
  t.className = 'toast';
  t.style.background = bg;
  t.style.color = bg === '#BFFF00' || bg === '#FFFFFF' ? '#1a1a1a' : '#fff';
  t.textContent = msg;
  document.body.appendChild(t);
  setTimeout(() => t.remove(), 2500);
}

// ===== SEARCH =====
function handleSearch(q) {
  searchQuery = q.toLowerCase().trim();
  renderTemplates();
}

// ===== RENDER TEMPLATES =====
function renderTemplates() {
  const container = document.getElementById('templateList');
  let filtered = allTemplates;
  if (searchQuery) {
    filtered = filtered.filter(t =>
      (t.template_name || '').toLowerCase().includes(searchQuery) ||
      (t.surgery_name || '').toLowerCase().includes(searchQuery) ||
      (t.surgeon_name || '').toLowerCase().includes(searchQuery)
    );
  }

  const empty = document.getElementById('emptyState');
  if (filtered.length === 0) {
    container.querySelectorAll('.template-card').forEach(el => el.remove());
    if (!empty) {
      container.innerHTML += `<div id="emptyState" class="col-span-full flex flex-col items-center justify-center py-16 opacity-50">
        <i data-lucide="clipboard-list" class="w-16 h-16 mb-4"></i>
        <p class="font-display font-bold text-xl">ยังไม่มี Template</p>
        <p class="text-sm mt-1">กดปุ่ม "สร้าง Template" เพื่อเริ่มต้น</p>
      </div>`;
      lucide.createIcons();
    } else { empty.style.display = ''; }
    return;
  }

  if (empty) empty.style.display = 'none';

  const existingCards = {};
  container.querySelectorAll('.template-card').forEach(el => { existingCards[el.dataset.id] = el; });

  filtered.forEach((tmpl, idx) => {
    const id = tmpl.__backendId;
    let steps = [];
    try { steps = JSON.parse(tmpl.steps_json || '[]'); } catch(e){}
    const color = tmpl.color || '#A855F7';

    if (existingCards[id]) {
      // Update in place
      const card = existingCards[id];
      card.querySelector('.card-title').textContent = tmpl.template_name || 'Untitled';
      card.querySelector('.card-surgery').textContent = tmpl.surgery_name || '';
      card.querySelector('.card-steps-count').textContent = steps.length + ' ขั้นตอน';
      card.querySelector('.card-accent').style.background = color;
      delete existingCards[id];
    } else {
      const card = document.createElement('div');
      card.className = 'template-card neo-border rounded-xl bg-white overflow-hidden cursor-pointer hover:translate-y-[-4px] transition-transform animate-in';
      card.dataset.id = id;
      card.innerHTML = `
        <div class="card-accent h-2" style="background:${color}"></div>
        <div class="p-4">
          <div class="flex items-start justify-between mb-3">
            <h3 class="card-title font-display font-extrabold text-lg leading-tight flex-1">${esc(tmpl.template_name || 'Untitled')}</h3>
            <div class="flex gap-1 ml-2 shrink-0">
              <button onclick="event.stopPropagation();openEditModal('${id}')" class="p-1.5 rounded hover:bg-purple-200 text-purple-700 transition-colors" title="แก้ไข"><i data-lucide="settings" class="w-5 h-5"></i></button>
              <button onclick="event.stopPropagation();duplicateTemplate('${id}')" class="p-1.5 rounded hover:bg-gray-200 transition-colors" title="ทำสำเนา"><i data-lucide="copy" class="w-5 h-5"></i></button>
              <button onclick="event.stopPropagation();openDeleteModal('${id}')" class="p-1.5 rounded hover:bg-red-100 text-red-500 transition-colors" title="ลบ"><i data-lucide="trash-2" class="w-5 h-5"></i></button>
            </div>
          </div>
          <div class="flex items-center gap-2 text-xs">
            <span class="card-steps-count">${steps.length} ขั้นตอน</span>
          </div>
          ${tmpl.notes ? `<div class="card-notes text-xs mt-2 p-2 bg-gray-50 rounded opacity-70 italic border-l-2" style="border-color:${color}">"${esc(tmpl.notes)}"</div>` : ''}
        </div>`;
      card.onclick = () => viewTemplate(id);
      container.appendChild(card);
    }
  });

  // Remove cards no longer visible
  Object.values(existingCards).forEach(el => el.remove());
  lucide.createIcons();
}

function esc(s) { const d = document.createElement('div'); d.textContent = s; return d.innerHTML; }

// ===== MODAL =====
function openCreateModal() {
  editingId = null;
  document.getElementById('modalTitle').textContent = 'สร้าง Template ใหม่';
  document.getElementById('inputSurgery').value = '';
  document.getElementById('inputNotes').value = '';
  modalSteps = [];
  selectedColor = '#A855F7';
  document.getElementById('modal').classList.remove('hidden');
  setTimeout(() => {
    renderColorPicker();
    renderSteps();
    lucide.createIcons();
  }, 10);
}

function openEditModal(id) {
  const tmpl = allTemplates.find(t => t.__backendId === id);
  if (!tmpl) return;
  editingId = id;
  document.getElementById('modalTitle').textContent = 'แก้ไข Template';
  document.getElementById('inputSurgery').value = tmpl.template_name || tmpl.surgery_name || '';
  document.getElementById('inputNotes').value = tmpl.notes || '';
  try { modalSteps = JSON.parse(tmpl.steps_json || '[]'); } catch(e) { modalSteps = []; }
  selectedColor = tmpl.color || '#A855F7';
  document.getElementById('modal').classList.remove('hidden');
  setTimeout(() => {
    renderColorPicker();
    renderSteps();
    lucide.createIcons();
  }, 10);
}

function closeModal() { document.getElementById('modal').classList.add('hidden'); }

// ===== SAVE =====
async function saveTemplate() {
  const surgery = document.getElementById('inputSurgery').value.trim();
  if (!surgery) { showToast('กรุณาใส่ชื่อการผ่าตัด', '#EF4444'); return; }

  syncStepsFromDOM();

  const data = {
    type: 'template',
    template_name: surgery,
    surgery_name: surgery,
    surgeon_name: '',
    notes: document.getElementById('inputNotes').value.trim(),
    steps_json: JSON.stringify(modalSteps),
    color: selectedColor,
    updated_at: new Date().toISOString()
  };

  const btn = document.getElementById('saveBtn');
  btn.textContent = 'กำลังบันทึก...';
  btn.disabled = true;

  try {
    let result;
    if (editingId) {
      const existing = allTemplates.find(t => t.__backendId === editingId);
      if (existing) {
        result = await window.dataSdk.update({ ...existing, ...data });
      } else {
        throw new Error('ไม่พบ Template ที่ต้องการแก้ไข');
      }
    } else {
      if (allTemplates.length >= 999) { showToast('ถึงขีดจำกัดแล้ว (999)', '#EF4444'); btn.textContent = 'บันทึก'; btn.disabled = false; return; }
      data.created_at = new Date().toISOString();
      result = await window.dataSdk.create(data);
    }

    btn.textContent = 'บันทึก';
    btn.disabled = false;

    if (result && result.isOk) {
      showToast(editingId ? 'อัปเดตสำเร็จ!' : 'สร้างสำเร็จ!', '#BFFF00');
      closeModal();
    } else {
      const errMsg = result && result.error ? result.error.toString() : 'เกิดข้อผิดพลาด';
      showToast(errMsg, '#EF4444');
    }
  } catch (err) {
    btn.textContent = 'บันทึก';
    btn.disabled = false;
    showToast('เกิดข้อผิดพลาด: ' + (err.message || 'ลองใหม่'), '#EF4444');
  }
}

// ===== DUPLICATE =====
async function duplicateTemplate(id) {
  if (allTemplates.length >= 999) { showToast('ถึงขีดจำกัดแล้ว (999)', '#EF4444'); return; }
  const tmpl = allTemplates.find(t => t.__backendId === id);
  if (!tmpl) return;
  const dup = {
    type: 'template',
    template_name: (tmpl.template_name || '') + ' (สำเนา)',
    surgery_name: tmpl.surgery_name || '',
    surgeon_name: '',
    steps_json: tmpl.steps_json || '[]',
    notes: tmpl.notes || '',
    color: tmpl.color || '#A855F7',
    created_at: new Date().toISOString(),
    updated_at: new Date().toISOString()
  };
  const r = await window.dataSdk.create(dup);
  if (r.isOk) showToast('ทำสำเนาสำเร็จ!', '#BFFF00');
  else showToast('เกิดข้อผิดพลาด', '#EF4444');
}

// ===== DELETE =====
function openDeleteModal(id) {
  deletingRecord = allTemplates.find(t => t.__backendId === id);
  if (!deletingRecord) return;
  document.getElementById('deleteModal').classList.remove('hidden');
  document.getElementById('confirmDeleteBtn').onclick = async () => {
    const btn = document.getElementById('confirmDeleteBtn');
    btn.textContent = 'กำลังลบ...';
    btn.disabled = true;
    const r = await window.dataSdk.delete(deletingRecord);
    btn.textContent = 'ลบเลย';
    btn.disabled = false;
    if (r.isOk) { showToast('ลบสำเร็จ', '#FF6B35'); closeDeleteModal(); }
    else showToast('เกิดข้อผิดพลาด', '#EF4444');
  };
  lucide.createIcons();
}
function closeDeleteModal() { document.getElementById('deleteModal').classList.add('hidden'); }

// ===== VIEW =====
function viewTemplate(id) {
  const tmpl = allTemplates.find(t => t.__backendId === id);
  if (!tmpl) return;
  let steps = [];
  try { steps = JSON.parse(tmpl.steps_json || '[]'); } catch(e){}
  const color = tmpl.color || '#A855F7';

  document.getElementById('viewHeader').style.background = `linear-gradient(135deg, ${color}, ${shiftColor(color)})`;
  document.getElementById('viewTitle').textContent = tmpl.template_name || 'Untitled';
  document.getElementById('viewTitle').style.color = color === '#BFFF00' || color === '#FFFFFF' ? '#1a1a1a' : '#fff';
  document.getElementById('viewEditBtn').onclick = () => { closeViewModal(); openEditModal(id); };

  let html = `<div class="space-y-4">
    <div class="space-y-1">
      <p class="text-xs font-mono opacity-60 uppercase">ประเภท</p>
      <p class="font-display font-bold text-lg">${esc(tmpl.surgery_name || '-')}</p>
    </div>`;
  if (tmpl.notes) {
    html += `<div class="bg-blue-50 p-3 rounded border-l-4 border-blue-400 space-y-1">
      <p class="text-xs font-mono opacity-60 uppercase">หมายเหตุ</p>
      <p class="text-sm">${esc(tmpl.notes)}</p>
    </div>`;
  }
  if (steps.length) {
    html += `<div class="space-y-2 mt-4">
      <p class="text-xs font-mono opacity-60 uppercase">ขั้นตอน (${steps.length})</p>
      <ol class="space-y-2">`;
    steps.forEach((s, i) => {
      const sc = s.color || '#f3f4f6';
      html += `<li class="flex gap-3 items-start p-2 bg-gray-50 rounded">
        <span class="neo-border-sm rounded-full w-8 h-8 flex items-center justify-center text-xs font-bold shrink-0 flex-center" style="background:${sc}; color: #1a1a1a;">${i+1}</span>
        <div class="flex-1 min-w-0">
          <p class="text-sm font-medium">${esc(s.text || 'ขั้นตอนที่ ' + (i+1))}</p>
          ${s.note ? `<p class="text-xs mt-1 p-2 bg-yellow-50 rounded border-l-2 border-yellow-300 italic">${esc(s.note)}</p>` : ''}
        </div>
      </li>`;
    });
    html += '</ol></div>';
  } else {
    html += '<p class="text-sm opacity-50">ไม่มีขั้นตอนในเทมเพลตนี้</p>';
  }
  html += '</div>';
  document.getElementById('viewContent').innerHTML = html;
  document.getElementById('viewModal').classList.remove('hidden');
  lucide.createIcons();
}
function closeViewModal() { document.getElementById('viewModal').classList.add('hidden'); }

function shiftColor(hex) {
  // Simple hue shift for gradient
  const colors = { '#A855F7':'#EC4899', '#BFFF00':'#10B981', '#FF6B35':'#F59E0B', '#3B82F6':'#6366F1', '#EF4444':'#F97316', '#10B981':'#06B6D4', '#F59E0B':'#EF4444', '#EC4899':'#A855F7', '#1a1a1a':'#374151', '#FFFFFF':'#E5E7EB' };
  return colors[hex] || '#A855F7';
}

// ===== COLOR PICKER =====
function renderColorPicker() {
  const container = document.getElementById('colorPicker');
  container.innerHTML = '';
  COLORS.forEach(c => {
    const dot = document.createElement('div');
    dot.className = 'color-dot' + (c === selectedColor ? ' active' : '');
    dot.style.background = c;
    dot.onclick = () => { selectedColor = c; renderColorPicker(); };
    container.appendChild(dot);
  });
}

function ensureColorPickerRendered() {
  const container = document.getElementById('colorPicker');
  if (!container.children.length) renderColorPicker();
}

// ===== STEPS =====
function addStep() {
  syncStepsFromDOM();
  modalSteps.push({ text: '', color: '#f3f4f6', note: '', id: Date.now() + Math.random() });
  renderSteps();
}

function renderSteps() {
  const container = document.getElementById('stepsList');
  container.innerHTML = '';
  if (!modalSteps.length) {
    container.innerHTML = '<p class="text-xs opacity-50 text-center py-4">ยังไม่มีขั้นตอน — กดเพิ่มขั้นตอน</p>';
    return;
  }
  modalSteps.forEach((step, i) => {
    const div = document.createElement('div');
    div.className = 'step-item neo-border-sm rounded-lg bg-white overflow-hidden';
    div.draggable = true;
    div.dataset.index = i;
    div.innerHTML = `
      <div class="flex items-center gap-2 p-2 bg-gray-50 border-b">
        <div class="cursor-grab active:cursor-grabbing shrink-0 px-1 opacity-50 touch-none" data-drag-handle="true"><i data-lucide="grip-vertical" class="w-4 h-4"></i></div>
        <span class="font-bold text-xs shrink-0 w-5 text-center">${i+1}</span>
        <input type="text" value="${esc(step.text)}" placeholder="ขั้นตอนที่ ${i+1}" class="flex-1 text-sm border-0 bg-transparent focus:outline-none" data-step-text="${i}">
        <input type="color" value="${step.color || '#f3f4f6'}" class="w-6 h-6 rounded cursor-pointer border-0 shrink-0" data-step-color="${i}" title="เปลี่ยนสี">
        <button onclick="removeStep(${i})" class="shrink-0 p-1 rounded hover:bg-red-50 text-red-400"><i data-lucide="x" class="w-4 h-4"></i></button>
      </div>
      <textarea placeholder="เพิ่มหมายเหตุของขั้นตอนนี้..." class="w-full text-xs border-0 bg-white p-2 focus:outline-none resize-none h-16" data-step-note="${i}"></textarea>`;

    // Load existing note
    const noteTextarea = div.querySelector(`[data-step-note="${i}"]`);
    if (step.note) noteTextarea.value = step.note;

    // Drag events
    div.addEventListener('dragstart', (e) => {
      dragSrcIndex = i;
      div.classList.add('dragging');
      e.dataTransfer.effectAllowed = 'move';
    });
    div.addEventListener('dragend', () => { div.classList.remove('dragging'); });
    div.addEventListener('dragover', (e) => { e.preventDefault(); div.classList.add('drag-over'); });
    div.addEventListener('dragleave', () => { div.classList.remove('drag-over'); });
    div.addEventListener('drop', (e) => {
      e.preventDefault();
      div.classList.remove('drag-over');
      if (dragSrcIndex !== null && dragSrcIndex !== i) {
        syncStepsFromDOM();
        const moved = modalSteps.splice(dragSrcIndex, 1)[0];
        modalSteps.splice(i, 0, moved);
        renderSteps();
      }
    });

    // Touch drag
    let touchY = 0;
    const handle = div.querySelector('[data-drag-handle]');
    handle.addEventListener('touchstart', (e) => {
      dragSrcIndex = i;
      touchY = e.touches[0].clientY;
      div.classList.add('dragging');
    }, { passive: true });
    handle.addEventListener('touchmove', (e) => {
      e.preventDefault();
      const cy = e.touches[0].clientY;
      const items = container.querySelectorAll('.step-item');
      items.forEach((item, idx) => {
        const rect = item.getBoundingClientRect();
        if (cy > rect.top && cy < rect.bottom && idx !== dragSrcIndex) {
          item.classList.add('drag-over');
        } else {
          item.classList.remove('drag-over');
        }
      });
    }, { passive: false });
    handle.addEventListener('touchend', (e) => {
      div.classList.remove('dragging');
      const cy = e.changedTouches[0].clientY;
      const items = container.querySelectorAll('.step-item');
      let targetIdx = null;
      items.forEach((item, idx) => {
        item.classList.remove('drag-over');
        const rect = item.getBoundingClientRect();
        if (cy > rect.top && cy < rect.bottom) targetIdx = idx;
      });
      if (targetIdx !== null && targetIdx !== dragSrcIndex) {
        syncStepsFromDOM();
        const moved = modalSteps.splice(dragSrcIndex, 1)[0];
        modalSteps.splice(targetIdx, 0, moved);
        renderSteps();
      }
    });

    container.appendChild(div);
  });
  lucide.createIcons();
}

function removeStep(i) {
  syncStepsFromDOM();
  modalSteps.splice(i, 1);
  renderSteps();
}

function syncStepsFromDOM() {
  document.querySelectorAll('[data-step-text]').forEach(inp => {
    const idx = parseInt(inp.dataset.stepText);
    if (modalSteps[idx]) modalSteps[idx].text = inp.value;
  });
  document.querySelectorAll('[data-step-color]').forEach(inp => {
    const idx = parseInt(inp.dataset.stepColor);
    if (modalSteps[idx]) modalSteps[idx].color = inp.value;
  });
  document.querySelectorAll('[data-step-note]').forEach(textarea => {
    const idx = parseInt(textarea.dataset.stepNote);
    if (modalSteps[idx]) modalSteps[idx].note = textarea.value;
  });
}

// Init icons
lucide.createIcons();
</script>
  </div>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9f2f4050b511fd00',t:'MTc3NzMwNzg0MC4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
