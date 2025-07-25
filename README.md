<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Malla Curricular Medicina - UdelaR</title>
<style>
  :root {
    --violeta-50: #f6f2ff;
    --violeta-100: #ede7f6;
    --violeta-200: #d1c4e9;
    --violeta-300: #b39ddb;
    --violeta-400: #9575cd;
    --violeta-500: #7e57c2;
    --violeta-600: #673ab7;
    --verde-aprobada: #a5d6a7;
    --rojo-bloqueada: #ef9a9a;
    --gris-texto: #333;
    --radius: 8px;
    --pad: 0.75rem;
    --gap: 1rem;
    --trans-fast: 0.15s;
    --font-base: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  }

  html, body {
    margin: 0;
    padding: 0;
    font-family: var(--font-base);
    background: var(--violeta-50);
    color: var(--gris-texto);
    line-height: 1.4;
  }

  h1 {
    text-align: center;
    margin: 1.5rem 0 0.25rem;
    font-weight: 600;
    color: var(--violeta-600);
  }

  #progress-summary {
    text-align: center;
    margin-bottom: 1.5rem;
    font-size: 0.95rem;
    color: var(--violeta-500);
  }

  .grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(220px,1fr));
    gap: var(--gap);
    padding: 0 var(--gap) 4rem;
    max-width: 1400px;
    margin: 0 auto;
  }

  .year-column {
    background: var(--violeta-100);
    padding: var(--pad);
    border-radius: var(--radius);
    border: 1px solid var(--violeta-200);
    box-shadow: 0 1px 2px rgba(0,0,0,0.05);
  }

  .year-column h2 {
    text-align: center;
    font-size: 1.1rem;
    margin: 0 0 0.75rem;
    color: var(--violeta-600);
  }

  .course {
    position: relative;
    margin-bottom: 0.5rem;
    padding: 0.5rem 0.75rem 0.5rem 2rem;
    font-size: 0.95rem;
    border-radius: var(--radius);
    border: 1px solid transparent;
    background: var(--violeta-200);
    cursor: pointer;
    transition: background var(--trans-fast), border var(--trans-fast), opacity var(--trans-fast), transform var(--trans-fast);
    user-select: none;
  }
  .course:last-child { margin-bottom: 0; }

  .course[data-status="bloqueada"] {
    background: var(--violeta-100);
    border-color: var(--violeta-200);
    cursor: not-allowed;
    opacity: 0.6;
  }

  .course[data-status="aprobada"] {
    background: var(--verde-aprobada);
    border-color: #81c784;
    text-decoration: line-through;
  }

  .course[data-status="disponible"] {
    background: var(--violeta-300);
    border-color: var(--violeta-400);
  }
  .course[data-status="disponible"]:hover {
    transform: scale(1.01);
  }

  .course-icon {
    position: absolute;
    left: 0.5rem;
    top: 50%;
    transform: translateY(-50%);
    font-size: 1rem;
    line-height: 1;
  }

  /* Tooltip */
  .course[data-tooltip]:hover::after {
    content: attr(data-tooltip);
    position: absolute;
    left: 0;
    top: 100%;
    margin-top: 4px;
    width: max-content;
    max-width: 260px;
    z-index: 10;
    padding: 0.4rem 0.6rem;
    font-size: 0.8rem;
    background: var(--rojo-bloqueada);
    color: #000;
    border-radius: var(--radius);
    box-shadow: 0 2px 4px rgba(0,0,0,0.2);
    white-space: normal;
  }

  /* Legend */
  #legend {
    max-width: 600px;
    margin: 0 auto 2rem;
    display: flex;
    justify-content: center;
    gap: 1rem;
    flex-wrap: wrap;
    font-size: 0.85rem;
  }
  #legend span {
    display: inline-flex;
    align-items: center;
    gap: 0.25rem;
    padding: 0.25rem 0.5rem;
    border-radius: var(--radius);
    border: 1px solid var(--violeta-400);
    background: var(--violeta-200);
  }
  #legend .leg-aprobada { background: var(--verde-aprobada); border-color: #81c784; }
  #legend .leg-disponible { background: var(--violeta-300); border-color: var(--violeta-400); }
  #legend .leg-bloqueada { background: var(--violeta-100); border-color: var(--violeta-200); opacity: 0.7; }

  footer {
    text-align: center;
    font-size: 0.75rem;
    color: #666;
    margin: 3rem 0 1rem;
  }
</style>
</head>
<body>
<h1>Malla Curricular Medicina - UdelaR</h1>
<div id="progress-summary"></div>
<div id="legend">
  <span class="leg-aprobada">✔ Aprobada</span>
  <span class="leg-disponible">• Disponible</span>
  <span class="leg-bloqueada">🔒 Bloqueada</span>
</div>
<div class="grid" id="grid"></div>
<footer>
  <p>Estado guardado localmente en tu navegador (localStorage). Puedes borrar el progreso desde las herramientas del navegador.</p>
</footer>

<script>
(() => {
  /* =============================================
   *  Datos de cursos
   * ============================================= */
  const Y1 = 1, Y2 = 2, Y3 = 3, Y4 = 4, Y5 = 5, Y6 = 6, Y7 = 7;

  const courses = [
    // Primer año
    {id:'intro_biol_mol', year:Y1, nombre:'Introducción a la biología celular y molecular', all:[], any:[]},
    {id:'intro_biostat', year:Y1, nombre:'Introducción a la bioestadística', all:[], any:[]},
    {id:'salud_hum_bioetica', year:Y1, nombre:'Salud y humanidades y bioética', all:[], any:[]},
    {id:'aprend_terr1', year:Y1, nombre:'Aprendizaje en territorio 1', all:[], any:[]},
    {id:'biol_mol', year:Y1, nombre:'Biología celular y molecular', all:['intro_biol_mol'], any:[]},
    {id:'aprend_terr2', year:Y1, nombre:'Aprendizaje en territorio 2', all:['aprend_terr1'], any:[]},

    // Segundo año
    {id:'anatomia', year:Y2, nombre:'Anatomía', all:['salud_hum_bioetica'], any:[]},
    {id:'histologia_biofisica', year:Y2, nombre:'Histología y biofísica', all:['biol_mol'], any:[]},
    {id:'histologia_neuro_cardio', year:Y2, nombre:'Histología de neuro y cardio', all:['biol_mol'], any:[]},
    {id:'neuro', year:Y2, nombre:'Neurociencias', all:['biol_mol'], any:[]},
    {id:'cardio_resp', year:Y2, nombre:'Cardiovascular y respiratorio', all:['biol_mol'], any:[]},

    // Tercer año
    {id:'digestivo_renal', year:Y3, nombre:'Digestivo renal endoc. y metab. y repr.', all:['anatomia','biol_mol'], any:[]},
    {id:'hematologia_inmuno', year:Y3, nombre:'Hematología inmunobiología', all:['biol_mol'], any:[]},
    {id:'metodo_cient1', year:Y3, nombre:'Metodología científica 1', all:['intro_biostat'], any:['neuro','cardio_resp','digestivo_renal']},

    // Cuarto año (requiere todo 1º + 2º + 3º)
    {id:'medicina_pna', year:Y4, nombre:'Medicina en el primer nivel de atención', groupAll:'first_trienio'},
    {id:'bases_patologia', year:Y4, nombre:'Bases científicas de la patología', groupAll:'first_trienio'},
    {id:'pediatria', year:Y4, nombre:'Pediatría', groupAll:'first_trienio'},
    {id:'gineco_neo', year:Y4, nombre:'Ginecología y neonatología', groupAll:'first_trienio'},

    // Quinto año
    {id:'clinica_medica', year:Y5, nombre:'Clínica médica', groupAll:'first_trienio', all:['bases_patologia','medicina_pna'], any:[]},
    {id:'patologia_med_terap', year:Y5, nombre:'Patología médica y terapéutica', groupAll:'first_trienio', all:['bases_patologia'], any:[]},

    // Sexto año
    {id:'clinica_quirurgica', year:Y6, nombre:'Clínica quirúrgica', groupAll:'first_trienio', all:['bases_patologia','medicina_pna'], any:[]},
    {id:'patologia_quirurgica', year:Y6, nombre:'Patología quirúrgica', groupAll:'first_trienio', all:['bases_patologia'], any:[]},
    {id:'mfc_salud_mental', year:Y6, nombre:'MFC-Salud mental en comunidad-psicología médica', groupAll:'first_trienio', all:['medicina_pna'], any:[]},
    {id:'metodo_cient2', year:Y6, nombre:'Metodología científica 2', groupAll:'first_trienio', all:['bases_patologia','medicina_pna'], any:['pediatria','gineco_neo','clinica_medica','clinica_quirurgica','mfc_salud_mental']},

    // Séptimo año
    {id:'internado', year:Y7, nombre:'Internado obligatorio', groupAll:'all'},
  ];

  /* =====================================================
   *  Construir grupos dinámicamente
   * ===================================================== */
  const idsPorAnio = courses.reduce((acc,c)=>{(acc[c.year]=acc[c.year]||[]).push(c.id); return acc;},{});
  const groupFirstTrienio = [...(idsPorAnio[Y1]||[]),...(idsPorAnio[Y2]||[]),...(idsPorAnio[Y3]||[])];
  const groupAll = courses.map(c=>c.id);

  const groups = {
    first_trienio: groupFirstTrienio,
    all: groupAll
  };

  // Expandir groupAll->all arrays into actual all[] for each course
  courses.forEach(c => {
    if (!c.all) c.all = [];
    if (!c.any) c.any = [];
    if (c.groupAll) {
      c.all = [...c.all, ...groups[c.groupAll]];
    }
  });

  // Mapa id->curso
  const courseMap = Object.fromEntries(courses.map(c=>[c.id,c]));

  // Persistencia
  const STORAGE_KEY = 'malla_medicina_udelar_v1';

  function loadState(){
    try {
      const raw = localStorage.getItem(STORAGE_KEY);
      if(!raw) return {};
      const parsed = JSON.parse(raw);
      return (parsed && typeof parsed === 'object') ? parsed : {};
    } catch(e){
      console.warn('Error loading state', e);
      return {};
    }
  }

  function saveState(state){
    try {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(state));
    } catch(e){
      console.warn('Error saving state', e);
    }
  }

  let state = loadState(); // {id:true}

  function isApproved(id){ return !!state[id]; }

  // Determina si un curso cumple requisitos
  function checkUnlocked(course){
    // Requisitos ALL: todos aprobados
    const faltanAll = course.all.filter(id => !isApproved(id));

    // Requisitos ANY: se necesita al menos uno aprobado; si lista vacía => ok
    let faltaAny = false;
    let faltantesAny = [];
    if(course.any && course.any.length){
      const algunoAprobado = course.any.some(id => isApproved(id));
      if(!algunoAprobado){
        faltaAny = true;
        faltantesAny = [...course.any];
      }
    }

    const unlocked = (faltanAll.length === 0) && !faltaAny;
    return {unlocked, faltanAll, faltaAny, faltantesAny};
  }

  function buildTooltip(course, check){
    if(check.unlocked) return '';
    const partes = [];
    if(check.faltanAll.length){
      const nombres = check.faltanAll.map(id=>courseMap[id]?.nombre||id);
      partes.push('Falta aprobar: '+nombres.join(', '));
    }
    if(check.faltaAny){
      const nombres = check.faltantesAny.map(id=>courseMap[id]?.nombre||id);
      partes.push('Necesitas al menos UNA de: '+nombres.join(', '));
    }
    return partes.join(' \n ');
  }

  // Renderizado
  const gridEl = document.getElementById('grid');

  function render(){
    gridEl.innerHTML = '';

    // Ordenar por año, luego según orden en array original
    const years = [...new Set(courses.map(c=>c.year))].sort((a,b)=>a-b);

    years.forEach(year => {
      const col = document.createElement('div');
      col.className = 'year-column';
      col.dataset.year = year;
      const h = document.createElement('h2');
      h.textContent = ${year}° año;
      col.appendChild(h);

      courses.filter(c=>c.year===year).forEach(course => {
        const check = checkUnlocked(course);
        const status = isApproved(course.id) ? 'aprobada' : (check.unlocked ? 'disponible' : 'bloqueada');
        const div = document.createElement('div');
        div.className = 'course';
        div.dataset.id = course.id;
        div.dataset.status = status;
        if(status==='bloqueada'){
          div.dataset.tooltip = buildTooltip(course, check);
        } else {
          delete div.dataset.tooltip;
        }
        div.textContent = course.nombre;

        const icon = document.createElement('span');
        icon.className = 'course-icon';
        icon.textContent = status==='aprobada' ? '✔' : (status==='bloqueada' ? '🔒' : '•');
        div.appendChild(icon);

        if(status!=='bloqueada'){
          div.addEventListener('click', ()=>{
            state[course.id] = !state[course.id];
            if(!state[course.id]) delete state[course.id];
            saveState(state);
            render();
          });
        }
        col.appendChild(div);
      });

      gridEl.appendChild(col);
    });

    updateSummary();
  }

  function countApproved(){
    return Object.values(state).filter(Boolean).length;
  }

  function updateSummary(){
    const total = courses.length;
    const aprob = countApproved();
    const percent = Math.round((aprob/total)*100);
    const ps = document.getElementById('progress-summary');
    ps.textContent = Aprobadas ${aprob} de ${total} (${percent}%);
  }

  // Primera renderización
  render();
})();
</script>
</body>
</html>
