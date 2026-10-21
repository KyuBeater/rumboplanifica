# rumboplanifica
App de viajes

<!DOCTYPE html>
<html lang="es" class="scroll-smooth">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Rumbo | Descubre tu próximo destino</title>
  
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  
  <!-- Google Fonts: Plus Jakarta Sans -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
  
  <!-- FontAwesome 6 -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['"Plus Jakarta Sans"', 'sans-serif'],
          },
          colors: {
            brand: {
              50: '#f0f9ff',
              100: '#e0f2fe',
              500: '#0284c7',
              600: '#0369a1',
              700: '#075985',
              900: '#0c4a6e',
            },
            coral: {
              500: '#f97316',
              600: '#ea580c',
            }
          }
        }
      }
    }
  </script>

  <style>
    /* Transiciones suaves y efectos visuales de elevación */
    .glass-nav {
      background: rgba(255, 255, 255, 0.85);
      backdrop-filter: blur(12px);
      -webkit-backdrop-filter: blur(12px);
    }
    .card-hover {
      transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    }
    .card-hover:hover {
      transform: translateY(-6px);
      box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 8px 10px -6px rgba(0, 0, 0, 0.05);
    }
    /* Toast animation */
    @keyframes slideUp {
      from { transform: translateY(100%); opacity: 0; }
      to { transform: translateY(0); opacity: 1; }
    }
    .animate-toast {
      animation: slideUp 0.3s ease-out forwards;
    }
  </style>
</head>
<body class="bg-slate-50 text-slate-800 font-sans antialiased min-h-screen flex flex-col selection:bg-brand-500 selection:text-white">

  <!-- NOTIFICACIÓN TOAST INTELIGENTE -->
  <div id="toast" class="fixed bottom-5 right-5 z-50 hidden animate-toast bg-slate-900 text-white px-5 py-3.5 rounded-2xl shadow-2xl flex items-center gap-3 border border-slate-700">
    <div id="toast-icon" class="w-8 h-8 rounded-full bg-brand-500/20 text-brand-400 flex items-center justify-center font-bold">
      <i class="fa-solid fa-check"></i>
    </div>
    <span id="toast-msg" class="text-sm font-medium">Acción completada</span>
  </div>

  <!-- 1. HEADER / NAVBAR -->
  <header class="sticky top-0 z-40 glass-nav border-b border-slate-200/80 transition-all">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-20 flex items-center justify-between">
      
      <!-- LOGO CON ICONO MODERNO DE BRÚJULA/AVIÓN -->
      <a href="#" onclick="switchTab('inicio')" class="flex items-center gap-2.5 group">
        <div class="w-11 h-11 rounded-2xl bg-gradient-to-tr from-brand-600 to-sky-400 flex items-center justify-center text-white shadow-md shadow-brand-500/20 group-hover:scale-105 transition-transform">
          <i class="fa-solid fa-compass text-xl group-hover:rotate-45 transition-transform duration-500"></i>
        </div>
        <span class="text-2xl font-extrabold tracking-tight bg-gradient-to-r from-slate-900 via-brand-900 to-brand-600 bg-clip-text text-transparent">
          Rumbo
        </span>
      </a>

      <!-- NAVEGACIÓN DESKTOP -->
      <nav class="hidden md:flex items-center gap-1 bg-slate-100/80 p-1.5 rounded-full border border-slate-200/60">
        <button onclick="switchTab('inicio')" id="nav-inicio" class="nav-btn px-5 py-2 rounded-full text-sm font-semibold text-brand-600 bg-white shadow-sm transition-all">
          Inicio
        </button>
        <button onclick="switchTab('explorar')" id="nav-explorar" class="nav-btn px-5 py-2 rounded-full text-sm font-medium text-slate-600 hover:text-slate-900 transition-all">
          Explorar
        </button>
        <button onclick="switchTab('favoritos')" id="nav-favoritos" class="nav-btn px-5 py-2 rounded-full text-sm font-medium text-slate-600 hover:text-slate-900 transition-all flex items-center gap-2">
          Favoritos
          <span id="fav-count-badge" class="bg-rose-500 text-white text-[10px] font-bold px-2 py-0.5 rounded-full hidden">0</span>
        </button>
        <button onclick="switchTab('mis-viajes')" id="nav-mis-viajes" class="nav-btn px-5 py-2 rounded-full text-sm font-medium text-slate-600 hover:text-slate-900 transition-all">
          Mis viajes
        </button>
      </nav>

      <!-- BOTÓN CTA "PLANIFICAR VIAJE" -->
      <div class="hidden sm:flex items-center gap-3">
        <button onclick="openPlanModal()" class="px-5 py-2.5 rounded-xl bg-gradient-to-r from-coral-500 to-amber-500 text-white font-bold text-sm shadow-md shadow-coral-500/20 hover:shadow-lg hover:shadow-coral-500/30 hover:scale-[1.02] active:scale-[0.98] transition-all flex items-center gap-2">
          <i class="fa-solid fa-paper-plane text-xs"></i>
          Planificar viaje
        </button>
      </div>

      <!-- MENÚ MÓVIL (HAMBURGUESA) -->
      <button id="menu-toggle-btn" onclick="toggleMobileMenu()" class="md:hidden w-10 h-10 rounded-xl bg-slate-100 border border-slate-200 flex items-center justify-center text-slate-700 hover:bg-slate-200 transition-colors">
        <i class="fa-solid fa-bars text-lg"></i>
      </button>

    </div>

    <!-- MENÚ DESPLEGABLE MÓVIL -->
    <div id="mobile-menu" class="hidden md:hidden bg-white border-b border-slate-200 px-4 py-4 space-y-2 shadow-xl">
      <button onclick="switchTab('inicio'); toggleMobileMenu();" class="w-full text-left px-4 py-2.5 rounded-xl text-sm font-semibold text-slate-800 hover:bg-slate-50">Inicio</button>
      <button onclick="switchTab('explorar'); toggleMobileMenu();" class="w-full text-left px-4 py-2.5 rounded-xl text-sm font-semibold text-slate-800 hover:bg-slate-50">Explorar</button>
      <button onclick="switchTab('favoritos'); toggleMobileMenu();" class="w-full text-left px-4 py-2.5 rounded-xl text-sm font-semibold text-slate-800 hover:bg-slate-50 flex items-center justify-between">
        Favoritos
        <span id="fav-count-mobile" class="bg-rose-500 text-white text-xs font-bold px-2 py-0.5 rounded-full">0</span>
      </button>
      <button onclick="switchTab('mis-viajes'); toggleMobileMenu();" class="w-full text-left px-4 py-2.5 rounded-xl text-sm font-semibold text-slate-800 hover:bg-slate-50">Mis viajes</button>
      <div class="pt-2 border-t border-slate-100">
        <button onclick="openPlanModal(); toggleMobileMenu();" class="w-full py-3 rounded-xl bg-coral-500 text-white font-bold text-center flex items-center justify-center gap-2">
          <i class="fa-solid fa-paper-plane text-xs"></i>
          Planificar viaje
        </button>
      </div>
    </div>
  </header>

  <!-- SECCIÓN CONTENEDORA PRINCIPAL -->
  <main class="flex-1">

    <!-- VISTA 1: INICIO -->
    <div id="view-inicio" class="view-panel space-y-16 pb-16">
      
      <!-- 2. HERO SECTION / BUSCADOR PRINCIPAL -->
      <section class="relative min-h-[520px] flex items-center justify-center px-4 py-20 overflow-hidden bg-slate-900">
        <!-- Imagen de Fondo Unsplash con overlay -->
        <img src="https://images.unsplash.com/photo-1488646953014-85cb44e25828?q=80&w=1920&auto=format&fit=crop" alt="Fondo Viajes" class="absolute inset-0 w-full h-full object-cover opacity-45 transform scale-105 animate-pulse duration-[10000ms]">
        <div class="absolute inset-0 bg-gradient-to-b from-slate-950/70 via-slate-900/60 to-slate-950/90"></div>

        <div class="relative z-10 max-w-4xl mx-auto text-center space-y-8">
          <div class="inline-flex items-center gap-2 px-4 py-2 rounded-full bg-white/10 backdrop-blur-md border border-white/20 text-white text-xs sm:text-sm font-medium">
            <span class="w-2 h-2 rounded-full bg-emerald-400 animate-ping"></span>
            ¡Más de 500 destinos esperando por ti!
          </div>

          <h1 class="text-4xl sm:text-5xl md:text-6xl font-extrabold text-white tracking-tight leading-tight">
            ¿Cuál será tu <span class="bg-gradient-to-r from-sky-400 via-teal-300 to-amber-300 bg-clip-text text-transparent">próximo destino?</span>
          </h1>

          <p class="text-slate-200 text-base sm:text-lg max-w-2xl mx-auto font-normal">
            Encuentra vuelos, itinerarios culturales, recomendaciones climáticas y organiza tu viaje perfecto con Rumbo.
          </p>

          <!-- BARRA DE BÚSQUEDA INTERACTIVA EN TIEMPO REAL -->
          <div class="max-w-2xl mx-auto relative group">
            <div class="bg-white p-2 rounded-2xl shadow-2xl border border-white/30 flex items-center gap-2 focus-within:ring-4 focus-within:ring-brand-500/30 transition-all">
              <div class="pl-4 text-slate-400 text-xl">
                <i class="fa-solid fa-magnifying-glass"></i>
              </div>
              <input 
                type="text" 
                id="hero-search-input" 
                oninput="onSearchInput(this.value)" 
                placeholder="Busca un país o destino (ej. Cartagena, París, México)..." 
                class="w-full py-3 px-2 text-slate-800 placeholder-slate-400 font-medium text-base sm:text-lg focus:outline-none bg-transparent"
              >
              <button onclick="scrollToDestinations()" class="hidden sm:flex px-6 py-3.5 bg-brand-600 hover:bg-brand-700 text-white font-bold rounded-xl transition-colors items-center gap-2 shrink-0">
                <span>Buscar</span>
                <i class="fa-solid fa-arrow-right text-xs"></i>
              </button>
            </div>

            <!-- Sugerencias rápidas de búsqueda -->
            <div class="flex flex-wrap items-center justify-center gap-2 pt-4 text-xs text-slate-300">
              <span class="font-medium text-slate-400">Populares:</span>
              <button onclick="quickFilter('Cartagena')" class="px-3 py-1 rounded-full bg-white/10 hover:bg-white/20 transition-colors">Cartagena</button>
              <button onclick="quickFilter('París')" class="px-3 py-1 rounded-full bg-white/10 hover:bg-white/20 transition-colors">París</button>
              <button onclick="quickFilter('México')" class="px-3 py-1 rounded-full bg-white/10 hover:bg-white/20 transition-colors">Ciudad de México</button>
            </div>
          </div>
        </div>
      </section>

      <!-- 3. SECCIÓN DESTINOS DESTACADOS -->
      <section id="destinos-section" class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex flex-col sm:flex-row sm:items-end justify-between mb-10 gap-4">
          <div>
            <div class="text-brand-600 font-bold text-xs uppercase tracking-wider mb-1">Explora el mundo</div>
            <h2 class="text-3xl sm:text-4xl font-extrabold text-slate-900">Destinos destacados</h2>
          </div>
          <div class="flex items-center gap-2">
            <span class="text-sm font-medium text-slate-500" id="results-count-text">Mostrando 3 destinos</span>
          </div>
        </div>

        <!-- GRID DE TARJETAS DE DESTINOS CON FOTOS REALES -->
        <div id="destinations-container" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
          <!-- Las tarjetas se generan dinámicamente con JS desde el array de datos -->
        </div>

        <!-- Sin Resultados -->
        <div id="no-search-results" class="hidden py-16 text-center space-y-4 bg-white rounded-3xl border border-slate-200">
          <div class="w-16 h-16 bg-slate-100 text-slate-400 rounded-full flex items-center justify-center mx-auto text-2xl">
            <i class="fa-solid fa-compass-slash"></i>
          </div>
          <h3 class="text-xl font-bold text-slate-800">No encontramos ningún destino que coincida</h3>
          <p class="text-slate-500 text-sm max-w-sm mx-auto">Prueba buscando con otra palabra como "Colombia", "Francia" o "México".</p>
          <button onclick="clearSearch()" class="px-5 py-2.5 bg-brand-600 text-white font-semibold rounded-xl text-sm">Ver todos los destinos</button>
        </div>
      </section>

      <!-- 4. SECCIÓN DE VALOR / CARACTERÍSTICAS ("PLANEA SIN COMPLICARTE") -->
      <section class="bg-gradient-to-b from-slate-100 to-slate-50 py-16 border-y border-slate-200/80">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 space-y-12">
          
          <div class="text-center max-w-2xl mx-auto space-y-3">
            <span class="px-3.5 py-1.5 rounded-full bg-brand-100 text-brand-700 font-bold text-xs uppercase tracking-wider">Tu asistente de viaje</span>
            <h2 class="text-3xl sm:text-4xl font-extrabold text-slate-900">Planea sin complicarte</h2>
            <p class="text-slate-600 text-base">Descubre destinos y organiza tu próximo viaje en cuestión de minutos con nuestras herramientas integradas.</p>
          </div>

          <!-- 3 TARJETAS DE FUNCIONALIDADES INTERACTIVAS -->
          <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
            
            <!-- Característica 1: Guardar Destinos -->
            <div onclick="switchTab('favoritos')" class="bg-white p-8 rounded-3xl border border-slate-200/80 shadow-sm card-hover cursor-pointer group flex flex-col justify-between">
              <div class="space-y-4">
                <div class="w-14 h-14 rounded-2xl bg-rose-50 text-rose-500 flex items-center justify-center text-2xl group-hover:bg-rose-500 group-hover:text-white transition-colors">
                  <i class="fa-solid fa-heart"></i>
                </div>
                <h3 class="text-xl font-bold text-slate-900">Guardar Destinos</h3>
                <p class="text-slate-600 text-sm leading-relaxed">
                  Guarda tus lugares favoritos haciendo clic en el corazón para consultarlos cuando quieras y compararlos fácilmente.
                </p>
              </div>
              <div class="pt-6 mt-6 border-t border-slate-100 flex items-center text-rose-600 font-semibold text-sm gap-2">
                <span>Ver guardados</span>
                <i class="fa-solid fa-chevron-right text-xs group-hover:translate-x-1 transition-transform"></i>
              </div>
            </div>

            <!-- Característica 2: Consulta Climas -->
            <div onclick="openWeatherWidget()" class="bg-white p-8 rounded-3xl border border-slate-200/80 shadow-sm card-hover cursor-pointer group flex flex-col justify-between">
              <div class="space-y-4">
                <div class="w-14 h-14 rounded-2xl bg-sky-50 text-sky-500 flex items-center justify-center text-2xl group-hover:bg-sky-500 group-hover:text-white transition-colors">
                  <i class="fa-solid fa-cloud-sun"></i>
                </div>
                <h3 class="text-xl font-bold text-slate-900">Consulta Climas</h3>
                <p class="text-slate-600 text-sm leading-relaxed">
                  Visualiza pronósticos meteorológicos en tiempo real para empacar la ropa adecuada y aprovechar cada momento del día.
                </p>
              </div>
              <div class="pt-6 mt-6 border-t border-slate-100 flex items-center text-sky-600 font-semibold text-sm gap-2">
                <span>Simular clima actual</span>
                <i class="fa-solid fa-chevron-right text-xs group-hover:translate-x-1 transition-transform"></i>
              </div>
            </div>

            <!-- Característica 3: Organiza Viajes -->
            <div onclick="openPlanModal()" class="bg-white p-8 rounded-3xl border border-slate-200/80 shadow-sm card-hover cursor-pointer group flex flex-col justify-between">
              <div class="space-y-4">
                <div class="w-14 h-14 rounded-2xl bg-amber-50 text-amber-500 flex items-center justify-center text-2xl group-hover:bg-amber-500 group-hover:text-white transition-colors">
                  <i class="fa-solid fa-list-check"></i>
                </div>
                <h3 class="text-xl font-bold text-slate-900">Organiza Viajes</h3>
                <p class="text-slate-600 text-sm leading-relaxed">
                  Crea itinerarios personalizados, asigna fechas y calcula tu presupuesto estimado sin enredos ni contratiempos.
                </p>
              </div>
              <div class="pt-6 mt-6 border-t border-slate-100 flex items-center text-amber-600 font-semibold text-sm gap-2">
                <span>Crear plan ahora</span>
                <i class="fa-solid fa-chevron-right text-xs group-hover:translate-x-1 transition-transform"></i>
              </div>
            </div>

          </div>

          <!-- 5. BANNER LLAMADA A LA ACCIÓN (CTA) -->
          <div class="relative rounded-3xl overflow-hidden bg-slate-900 p-8 sm:p-12 text-white shadow-xl">
            <img src="https://images.unsplash.com/photo-1469854523086-cc02fe5d8800?q=80&w=1600&auto=format&fit=crop" alt="Aventura Viaje" class="absolute inset-0 w-full h-full object-cover opacity-25">
            <div class="absolute inset-0 bg-gradient-to-r from-slate-950 via-slate-900/90 to-transparent"></div>

            <div class="relative z-10 max-w-xl space-y-4">
              <h3 class="text-2xl sm:text-3xl font-extrabold leading-tight">
                ¿Ya tienes un destino en mente?
              </h3>
              <p class="text-slate-300 text-sm sm:text-base font-normal">
                Empieza a organizar tu próximo viaje hoy mismo. Personaliza fechas, actividades y presupuesto en segundos.
              </p>
              <div class="pt-2">
                <button onclick="openPlanModal()" class="px-7 py-3.5 rounded-2xl bg-gradient-to-r from-coral-500 to-amber-500 hover:from-coral-600 hover:to-amber-600 text-white font-bold text-base shadow-lg shadow-coral-500/25 hover:scale-105 transition-all flex items-center gap-3">
                  <i class="fa-solid fa-calendar-plus"></i>
                  [ Planificar viaje ]
                </button>
              </div>
            </div>
          </div>

        </div>
      </section>

    </div>

    <!-- VISTA 2: EXPLORAR -->
    <div id="view-explorar" class="view-panel hidden max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12 space-y-8">
      <div>
        <h1 class="text-3xl font-extrabold text-slate-900">Catálogo Completo de Destinos</h1>
        <p class="text-slate-500 mt-1">Explora todos los destinos turísticos disponibles en Rumbo.</p>
      </div>

      <!-- Filtros de Categorías -->
      <div class="flex flex-wrap gap-2 pb-4 border-b border-slate-200">
        <button onclick="filterCategory('todos')" class="cat-btn px-4 py-2 rounded-xl text-sm font-semibold bg-brand-600 text-white">Todos</button>
        <button onclick="filterCategory('playa')" class="cat-btn px-4 py-2 rounded-xl text-sm font-semibold bg-slate-100 text-slate-600 hover:bg-slate-200">Playas & Mar</button>
        <button onclick="filterCategory('cultura')" class="cat-btn px-4 py-2 rounded-xl text-sm font-semibold bg-slate-100 text-slate-600 hover:bg-slate-200">Cultura & Historia</button>
        <button onclick="filterCategory('europa')" class="cat-btn px-4 py-2 rounded-xl text-sm font-semibold bg-slate-100 text-slate-600 hover:bg-slate-200">Europa</button>
        <button onclick="filterCategory('latam')" class="cat-btn px-4 py-2 rounded-xl text-sm font-semibold bg-slate-100 text-slate-600 hover:bg-slate-200">Latinoamérica</button>
      </div>

      <div id="explorar-container" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
        <!-- Renderizado dinámico -->
      </div>
    </div>

    <!-- VISTA 3: FAVORITOS -->
    <div id="view-favoritos" class="view-panel hidden max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12 space-y-8">
      <div>
        <h1 class="text-3xl font-extrabold text-slate-900 flex items-center gap-3">
          Mis Destinos Guardados
          <i class="fa-solid fa-heart text-rose-500"></i>
        </h1>
        <p class="text-slate-500 mt-1">Lista de lugares que has marcado como tus preferidos.</p>
      </div>

      <div id="favoritos-container" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
        <!-- Contenido dinámico -->
      </div>

      <div id="empty-favorites" class="py-20 text-center space-y-4 bg-white rounded-3xl border border-slate-200">
        <div class="w-16 h-16 bg-rose-50 text-rose-400 rounded-full flex items-center justify-center mx-auto text-2xl">
          <i class="fa-regular fa-heart"></i>
        </div>
        <h3 class="text-xl font-bold text-slate-800">Aún no tienes destinos guardados</h3>
        <p class="text-slate-500 text-sm max-w-sm mx-auto">Haz clic en el icono del corazón de cualquier tarjeta para guardarlo en tu lista.</p>
        <button onclick="switchTab('inicio')" class="px-5 py-2.5 bg-brand-600 text-white font-semibold rounded-xl text-sm">Explorar destinos ahora</button>
      </div>
    </div>

    <!-- VISTA 4: MIS VIAJES -->
    <div id="view-mis-viajes" class="view-panel hidden max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12 space-y-8">
      <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4">
        <div>
          <h1 class="text-3xl font-extrabold text-slate-900">Mis Viajes Planificados</h1>
          <p class="text-slate-500 mt-1">Gestiona tus próximos itinerarios y aventuras organizadas.</p>
        </div>
        <button onclick="openPlanModal()" class="px-5 py-2.5 bg-coral-500 text-white font-bold rounded-xl text-sm shadow-md flex items-center gap-2 self-start">
          <i class="fa-solid fa-plus"></i>
          Nuevo Plan de Viaje
        </button>
      </div>

      <div id="trips-container" class="grid grid-cols-1 md:grid-cols-2 gap-6">
        <!-- Renderizado de itinerarios -->
      </div>
    </div>

  </main>

  <!-- MODAL CLIMA / SIMULACIÓN -->
  <div id="weather-modal" class="fixed inset-0 z-50 bg-slate-900/60 backdrop-blur-sm hidden flex items-center justify-center p-4">
    <div class="bg-white rounded-3xl max-w-md w-full p-6 space-y-6 border border-slate-100 shadow-2xl">
      <div class="flex items-center justify-between border-b border-slate-100 pb-4">
        <h3 class="text-xl font-bold text-slate-900 flex items-center gap-2">
          <i class="fa-solid fa-cloud-sun text-sky-500"></i>
          Consulta de Climas
        </h3>
        <button onclick="closeWeatherWidget()" class="w-8 h-8 rounded-full bg-slate-100 text-slate-500 hover:bg-slate-200 flex items-center justify-center">
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>

      <div class="space-y-4">
        <label class="block text-xs font-bold uppercase text-slate-500">Selecciona Destino</label>
        <select id="weather-select" onchange="updateWeatherDisplay()" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-semibold text-slate-800">
          <option value="Cartagena">Cartagena, Colombia</option>
          <option value="Ciudad de México">Ciudad de México, México</option>
          <option value="París">París, Francia</option>
        </select>

        <div class="bg-gradient-to-br from-sky-400 to-brand-600 text-white p-6 rounded-2xl text-center space-y-2 shadow-lg">
          <div id="w-city" class="text-lg font-bold">Cartagena, Colombia</div>
          <div id="w-temp" class="text-5xl font-extrabold">31°C</div>
          <div id="w-desc" class="text-sm font-medium opacity-90">Soleado con brisa tropical</div>
          <div class="pt-4 flex justify-around text-xs border-t border-white/20 mt-4">
            <div>
              <span class="block opacity-75">Humedad</span>
              <strong id="w-hum">78%</strong>
            </div>
            <div>
              <span class="block opacity-75">Viento</span>
              <strong id="w-wind">18 km/h</strong>
            </div>
            <div>
              <span class="block opacity-75">Recomendación</span>
              <strong>Bloqueador</strong>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- MODAL INTERACTIVO DE "PLANIFICAR VIAJE" -->
  <div id="plan-modal" class="fixed inset-0 z-50 bg-slate-900/60 backdrop-blur-sm hidden flex items-center justify-center p-4">
    <div class="bg-white rounded-3xl max-w-lg w-full p-6 sm:p-8 space-y-6 border border-slate-100 shadow-2xl relative overflow-hidden">
      
      <div class="flex items-center justify-between border-b border-slate-100 pb-4">
        <div>
          <h3 class="text-2xl font-extrabold text-slate-900 flex items-center gap-2">
            <i class="fa-solid fa-paper-plane text-coral-500 text-xl"></i>
            Planifica tu viaje
          </h3>
          <p class="text-xs text-slate-500 mt-0.5">Ingresa los datos para crear tu itinerario personalizado en Rumbo.</p>
        </div>
        <button onclick="closePlanModal()" class="w-9 h-9 rounded-full bg-slate-100 text-slate-500 hover:bg-slate-200 flex items-center justify-center transition-colors">
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>

      <form id="plan-form" onsubmit="handleNewTripSubmit(event)" class="space-y-4">
        <div>
          <label class="block text-xs font-bold text-slate-700 uppercase tracking-wider mb-1.5">Destino Principal</label>
          <div class="relative">
            <i class="fa-solid fa-location-dot absolute left-3.5 top-1/2 -translate-y-1/2 text-slate-400"></i>
            <input type="text" id="trip-destination" required placeholder="Ej. Cartagena, París, Tokio..." class="w-full pl-10 pr-4 py-3 bg-slate-50 border border-slate-200 rounded-xl text-slate-900 font-medium text-sm focus:ring-2 focus:ring-brand-500 focus:bg-white focus:outline-none transition-all">
          </div>
        </div>

        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
          <div>
            <label class="block text-xs font-bold text-slate-700 uppercase tracking-wider mb-1.5">Fecha de Salida</label>
            <input type="date" id="trip-date" required class="w-full px-3.5 py-3 bg-slate-50 border border-slate-200 rounded-xl text-slate-900 font-medium text-sm focus:ring-2 focus:ring-brand-500 focus:bg-white focus:outline-none transition-all">
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 uppercase tracking-wider mb-1.5">Presupuesto Estimado ($)</label>
            <input type="number" id="trip-budget" required placeholder="1200" class="w-full px-3.5 py-3 bg-slate-50 border border-slate-200 rounded-xl text-slate-900 font-medium text-sm focus:ring-2 focus:ring-brand-500 focus:bg-white focus:outline-none transition-all">
          </div>
        </div>

        <div>
          <label class="block text-xs font-bold text-slate-700 uppercase tracking-wider mb-1.5">Notas / Actividades planeadas</label>
          <textarea id="trip-notes" rows="3" placeholder="Reservar hotel céntrico, visitar museos locales, probar gastronomía típica..." class="w-full px-3.5 py-3 bg-slate-50 border border-slate-200 rounded-xl text-slate-900 font-medium text-sm focus:ring-2 focus:ring-brand-500 focus:bg-white focus:outline-none transition-all"></textarea>
        </div>

        <div class="flex items-center justify-end gap-3 pt-4 border-t border-slate-100">
          <button type="button" onclick="closePlanModal()" class="px-5 py-2.5 rounded-xl font-bold text-slate-600 hover:bg-slate-100 text-sm transition-colors">
            Cancelar
          </button>
          <button type="submit" class="px-6 py-2.5 rounded-xl bg-gradient-to-r from-coral-500 to-amber-500 text-white font-bold text-sm shadow-md shadow-coral-500/20 hover:scale-[1.02] transition-all flex items-center gap-2">
            <i class="fa-solid fa-check"></i>
            Guardar Itinerario
          </button>
        </div>
      </form>

    </div>
  </div>

  <!-- 6. PIE DE PÁGINA (FOOTER) -->
  <footer class="bg-slate-950 text-white border-t border-slate-800 pt-16 pb-12">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 space-y-12">
      <div class="grid grid-cols-1 md:grid-cols-4 gap-8">
        
        <!-- Col 1: Marca -->
        <div class="space-y-4 md:col-span-1">
          <div class="flex items-center gap-2.5">
            <div class="w-9 h-9 rounded-xl bg-gradient-to-tr from-brand-500 to-sky-400 flex items-center justify-center text-white font-bold">
              <i class="fa-solid fa-compass"></i>
            </div>
            <span class="text-2xl font-extrabold tracking-tight text-white">Rumbo</span>
          </div>
          <p class="text-slate-400 text-sm leading-relaxed">
            Descubre tu próximo destino. La plataforma inteligente para buscar, guardar y organizar tus viajes alrededor del mundo.
          </p>
        </div>

        <!-- Col 2: Enlaces Rápidos -->
        <div class="space-y-3">
          <h4 class="text-sm font-bold uppercase tracking-wider text-slate-200">Navegación</h4>
          <ul class="space-y-2 text-sm text-slate-400">
            <li><button onclick="switchTab('inicio')" class="hover:text-white transition-colors">Inicio</button></li>
            <li><button onclick="switchTab('explorar')" class="hover:text-white transition-colors">Explorar Destinos</button></li>
            <li><button onclick="switchTab('favoritos')" class="hover:text-white transition-colors">Destinos Guardados</button></li>
            <li><button onclick="switchTab('mis-viajes')" class="hover:text-white transition-colors">Mis Viajes</button></li>
          </ul>
        </div>

        <!-- Col 3: Destinos Populares -->
        <div class="space-y-3">
          <h4 class="text-sm font-bold uppercase tracking-wider text-slate-200">Top Destinos</h4>
          <ul class="space-y-2 text-sm text-slate-400">
            <li><a href="#" onclick="quickFilter('Cartagena')" class="hover:text-white transition-colors">Cartagena, Colombia</a></li>
            <li><a href="#" onclick="quickFilter('México')" class="hover:text-white transition-colors">Ciudad de México</a></li>
            <li><a href="#" onclick="quickFilter('París')" class="hover:text-white transition-colors">París, Francia</a></li>
          </ul>
        </div>

        <!-- Col 4: Boletín -->
        <div class="space-y-3">
          <h4 class="text-sm font-bold uppercase tracking-wider text-slate-200">Boletín de Ofertas</h4>
          <p class="text-xs text-slate-400">Recibe descuentos exclusivos y sugerencias semanales.</p>
          <div class="flex items-center gap-2">
            <input type="email" placeholder="Tu correo electrónico" class="px-3 py-2 bg-slate-900 border border-slate-800 rounded-xl text-xs text-white focus:outline-none w-full">
            <button onclick="showToast('¡Te has suscrito al boletín de Rumbo!')" class="px-3 py-2 bg-brand-600 text-white rounded-xl text-xs font-bold hover:bg-brand-500">Unirme</button>
          </div>
        </div>

      </div>

      <div class="pt-8 border-t border-slate-900 flex flex-col sm:flex-row items-center justify-between text-xs text-slate-500 gap-4">
        <div>© 2026 Rumbo. Todos los derechos reservados.</div>
        <div class="flex items-center gap-6">
          <a href="#" class="hover:underline">Privacidad</a>
          <a href="#" class="hover:underline">Términos</a>
          <a href="#" class="hover:underline">Soporte</a>
        </div>
      </div>
    </div>
  </footer>

  <!-- SCRIPT CON TODA LA LÓGICA E INTERACTIVIDAD -->
  <script>
    // BASE DE DATOS DE DESTINOS
    const destinationsData = [
      {
        id: 'cartagena',
        name: 'Cartagena',
        country: 'Colombia',
        category: 'playa',
        region: 'latam',
        rating: 4.9,
        reviews: 320,
        image: 'https://images.unsplash.com/photo-1583531172005-814191b8b6c0?q=80&w=800&auto=format&fit=crop',
        desc: 'Ciudad colonial con murallas históricas, arquitectura caribeña de colores y hermosas playas tropicales.',
        isFav: false
      },
      {
        id: 'cdmx',
        name: 'Ciudad de México',
        country: 'México',
        category: 'cultura',
        region: 'latam',
        rating: 4.8,
        reviews: 410,
        image: 'https://images.unsplash.com/photo-1518638150340-f706e86654de?q=80&w=800&auto=format&fit=crop',
        desc: 'Centro histórico vibrante con impresionantes museos, murales icónicos y exquisita gastronomía tradicional.',
        isFav: false
      },
      {
        id: 'paris',
        name: 'París',
        country: 'Francia',
        category: 'cultura',
        region: 'europa',
        rating: 4.9,
        reviews: 650,
        image: 'https://images.unsplash.com/photo-1502602898657-3e91760cbb34?q=80&w=800&auto=format&fit=crop',
        desc: 'La capital del arte y el romance. Conoce la icónica Torre Eiffel, paseos junto al Sena y la gran cultura europea.',
        isFav: false
      }
    ];

    // ESTADO INICIAL
    let userPlans = [
      {
        id: 1,
        destination: 'Cartagena, Colombia',
        date: '2026-11-15',
        budget: '1500',
        notes: 'Hospedaje en Ciudad Amurallada, tour a Islas del Rosario.'
      }
    ];

    // INICIALIZACIÓN AL CÁRGAR
    window.addEventListener('DOMContentLoaded', () => {
      renderDestinations(destinationsData);
      renderExplorar(destinationsData);
      renderTrips();
      updateFavBadges();
    });

    // NAVEGACIÓN ENTRE PESTAÑAS (TABS)
    function switchTab(tabId) {
      document.querySelectorAll('.view-panel').forEach(panel => panel.classList.add('hidden'));
      document.getElementById(`view-${tabId}`)?.classList.remove('hidden');

      document.querySelectorAll('.nav-btn').forEach(btn => {
        btn.classList.remove('bg-white', 'text-brand-600', 'shadow-sm', 'font-semibold');
        btn.classList.add('text-slate-600', 'font-medium');
      });

      const activeNav = document.getElementById(`nav-${tabId}`);
      if (activeNav) {
        activeNav.classList.add('bg-white', 'text-brand-600', 'shadow-sm', 'font-semibold');
        activeNav.classList.remove('text-slate-600', 'font-medium');
      }

      window.scrollTo({ top: 0, behavior: 'smooth' });
    }

    // RENDERIZADO DE TARJETAS PRINCIPALES
    function renderDestinations(items) {
      const container = document.getElementById('destinations-container');
      const countText = document.getElementById('results-count-text');
      const noResults = document.getElementById('no-search-results');

      if (items.length === 0) {
        container.classList.add('hidden');
        noResults.classList.remove('hidden');
        countText.textContent = '0 destinos encontrados';
        return;
      }

      container.classList.remove('hidden');
      noResults.classList.add('hidden');
      countText.textContent = `Mostrando ${items.length} destinos`;

      container.innerHTML = items.map(dest => `
        <div class="bg-white rounded-3xl overflow-hidden border border-slate-200/80 shadow-sm card-hover flex flex-col justify-between group">
          <div>
            <div class="relative h-56 overflow-hidden">
              <img src="${dest.image}" alt="${dest.name}" class="w-full h-full object-cover group-hover:scale-110 transition-transform duration-700">
              <div class="absolute inset-0 bg-gradient-to-t from-slate-950/60 via-transparent to-transparent"></div>
              
              <button onclick="toggleFavorite('${dest.id}')" class="absolute top-4 right-4 w-10 h-10 rounded-full bg-white/80 backdrop-blur-md text-slate-700 hover:text-rose-500 flex items-center justify-center transition-all shadow-md">
                <i class="${dest.isFav ? 'fa-solid text-rose-500 scale-110' : 'fa-regular'} fa-heart text-lg"></i>
              </button>

              <span class="absolute bottom-4 left-4 px-3 py-1 rounded-full bg-slate-900/80 backdrop-blur-md text-white text-xs font-semibold">
                <i class="fa-solid fa-location-dot text-brand-400 mr-1"></i> ${dest.country}
              </span>
            </div>

            <div class="p-6 space-y-3">
              <div class="flex items-center justify-between">
                <h3 class="text-xl font-bold text-slate-900">${dest.name}</h3>
                <div class="flex items-center gap-1 text-amber-500 font-bold text-sm">
                  <i class="fa-solid fa-star text-xs"></i>
                  <span>${dest.rating}</span>
                  <span class="text-slate-400 font-normal text-xs">(${dest.reviews})</span>
                </div>
              </div>
              <p class="text-slate-600 text-sm leading-relaxed line-clamp-2">${dest.desc}</p>
            </div>
          </div>

          <div class="p-6 pt-0 flex items-center justify-between border-t border-slate-100 mt-4">
            <button onclick="openPlanForDestination('${dest.name}, ${dest.country}')" class="w-full py-3 rounded-xl bg-slate-100 hover:bg-brand-600 hover:text-white text-slate-800 font-bold text-xs uppercase tracking-wider transition-all flex items-center justify-center gap-2">
              <i class="fa-solid fa-paper-plane"></i>
              Planificar este viaje
            </button>
          </div>
        </div>
      `).join('');
    }

    // RENDERIZADO TAB EXPLORAR
    function renderExplorar(items) {
      const container = document.getElementById('explorar-container');
      container.innerHTML = items.map(dest => `
        <div class="bg-white rounded-2xl overflow-hidden border border-slate-200 p-4 flex gap-4 items-center shadow-sm">
          <img src="${dest.image}" class="w-24 h-24 rounded-xl object-cover" alt="${dest.name}">
          <div class="space-y-1 flex-1">
            <h4 class="font-bold text-slate-900">${dest.name}</h4>
            <p class="text-xs text-slate-500">${dest.country}</p>
            <div class="text-xs text-amber-500 font-bold"><i class="fa-solid fa-star"></i> ${dest.rating}</div>
            <button onclick="toggleFavorite('${dest.id}')" class="text-xs font-semibold text-rose-500 hover:underline">
              ${dest.isFav ? 'Quitar de guardados' : 'Guardar a favoritos'}
            </button>
          </div>
        </div>
      `).join('');
    }

    // BÚSQUEDA EN TIEMPO REAL
    function onSearchInput(query) {
      const term = query.toLowerCase().trim();
      const filtered = destinationsData.filter(d => 
        d.name.toLowerCase().includes(term) || 
        d.country.toLowerCase().includes(term) ||
        d.desc.toLowerCase().includes(term)
      );
      renderDestinations(filtered);
    }

    function quickFilter(term) {
      const searchInput = document.getElementById('hero-search-input');
      if (searchInput) searchInput.value = term;
      onSearchInput(term);
      scrollToDestinations();
    }

    function clearSearch() {
      const searchInput = document.getElementById('hero-search-input');
      if (searchInput) searchInput.value = '';
      renderDestinations(destinationsData);
    }

    function scrollToDestinations() {
      document.getElementById('destinos-section')?.scrollIntoView({ behavior: 'smooth' });
    }

    // GESTIÓN DE FAVORITOS
    function toggleFavorite(id) {
      const item = destinationsData.find(d => d.id === id);
      if (item) {
        item.isFav = !item.isFav;
        renderDestinations(destinationsData);
        renderExplorar(destinationsData);
        renderFavoritesView();
        updateFavBadges();
        
        showToast(item.isFav ? `¡${item.name} guardado en favoritos!` : `Eliminado de favoritos`);
      }
    }

    function updateFavBadges() {
      const count = destinationsData.filter(d => d.isFav).length;
      const badge = document.getElementById('fav-count-badge');
      const mobileBadge = document.getElementById('fav-count-mobile');

      if (count > 0) {
        badge.textContent = count;
        badge.classList.remove('hidden');
        if (mobileBadge) mobileBadge.textContent = count;
      } else {
        badge.classList.add('hidden');
        if (mobileBadge) mobileBadge.textContent = '0';
      }
    }

    function renderFavoritesView() {
      const favs = destinationsData.filter(d => d.isFav);
      const container = document.getElementById('favoritos-container');
      const emptyState = document.getElementById('empty-favorites');

      if (favs.length === 0) {
        container.classList.add('hidden');
        emptyState.classList.remove('hidden');
      } else {
        container.classList.remove('hidden');
        emptyState.classList.add('hidden');
        container.innerHTML = favs.map(dest => `
          <div class="bg-white rounded-3xl overflow-hidden border border-slate-200 p-5 space-y-4 shadow-sm flex flex-col justify-between">
            <div class="flex items-center gap-4">
              <img src="${dest.image}" class="w-20 h-20 rounded-2xl object-cover" alt="${dest.name}">
              <div>
                <h3 class="font-bold text-slate-900 text-lg">${dest.name}</h3>
                <p class="text-xs text-slate-500">${dest.country}</p>
                <div class="text-xs text-amber-500 font-bold mt-1"><i class="fa-solid fa-star"></i> ${dest.rating}</div>
              </div>
            </div>
            <div class="flex items-center justify-between border-t border-slate-100 pt-3">
              <button onclick="toggleFavorite('${dest.id}')" class="text-xs text-rose-600 font-bold hover:underline">Eliminar</button>
              <button onclick="openPlanForDestination('${dest.name}, ${dest.country}')" class="px-4 py-2 bg-brand-600 text-white rounded-xl text-xs font-bold">Planificar</button>
            </div>
          </div>
        `).join('');
      }
    }

    // MIS VIAJES (PLANES)
    function renderTrips() {
      const container = document.getElementById('trips-container');
      if (userPlans.length === 0) {
        container.innerHTML = `<p class="text-slate-500 text-sm">No tienes itinerarios aún.</p>`;
        return;
      }

      container.innerHTML = userPlans.map(plan => `
        <div class="bg-white p-6 rounded-3xl border border-slate-200/80 shadow-sm space-y-4 relative">
          <div class="flex items-center justify-between border-b border-slate-100 pb-3">
            <div class="flex items-center gap-3">
              <div class="w-10 h-10 rounded-xl bg-brand-50 text-brand-600 flex items-center justify-center font-bold">
                <i class="fa-solid fa-plane-departure"></i>
              </div>
              <div>
                <h3 class="font-bold text-slate-900 text-lg">${plan.destination}</h3>
                <p class="text-xs text-slate-500">Fecha: ${plan.date}</p>
              </div>
            </div>
            <span class="px-3 py-1 rounded-full bg-emerald-50 text-emerald-700 font-bold text-xs">$${plan.budget} USD</span>
          </div>
          <p class="text-sm text-slate-600">${plan.notes || 'Sin notas adicionales.'}</p>
          <div class="flex justify-end">
            <button onclick="deleteTrip(${plan.id})" class="text-xs text-slate-400 hover:text-rose-600 font-medium">Cancelar itinerario</button>
          </div>
        </div>
      `).join('');
    }

    function handleNewTripSubmit(e) {
      e.preventDefault();
      const dest = document.getElementById('trip-destination').value;
      const date = document.getElementById('trip-date').value;
      const budget = document.getElementById('trip-budget').value;
      const notes = document.getElementById('trip-notes').value;

      userPlans.push({
        id: Date.now(),
        destination: dest,
        date: date,
        budget: budget,
        notes: notes
      });

      renderTrips();
      closePlanModal();
      showToast('¡Nuevo itinerario guardado en Mis Viajes!');
      switchTab('mis-viajes');
      e.target.reset();
    }

    function deleteTrip(id) {
      userPlans = userPlans.filter(p => p.id !== id);
      renderTrips();
      showToast('Itinerario eliminado');
    }

    // MODAL CONTROL
    function openPlanModal() {
      document.getElementById('plan-modal').classList.remove('hidden');
    }

    function closePlanModal() {
      document.getElementById('plan-modal').classList.add('hidden');
    }

    function openPlanForDestination(destName) {
      openPlanModal();
      const input = document.getElementById('trip-destination');
      if (input) input.value = destName;
    }

    function openWeatherWidget() {
      document.getElementById('weather-modal').classList.remove('hidden');
      updateWeatherDisplay();
    }

    function closeWeatherWidget() {
      document.getElementById('weather-modal').classList.add('hidden');
    }

    function updateWeatherDisplay() {
      const city = document.getElementById('weather-select').value;
      const weatherData = {
        'Cartagena': { temp: '31°C', desc: 'Soleado con brisa tropical', hum: '78%', wind: '18 km/h', full: 'Cartagena, Colombia' },
        'Ciudad de México': { temp: '22°C', desc: 'Parcialmente nublado', hum: '52%', wind: '12 km/h', full: 'Ciudad de México, México' },
        'París': { temp: '16°C', desc: 'Templado con lluvia ligera', hum: '82%', wind: '22 km/h', full: 'París, Francia' }
      };

      const data = weatherData[city] || weatherData['Cartagena'];
      document.getElementById('w-city').textContent = data.full;
      document.getElementById('w-temp').textContent = data.temp;
      document.getElementById('w-desc').textContent = data.desc;
      document.getElementById('w-hum').textContent = data.hum;
      document.getElementById('w-wind').textContent = data.wind;
    }

    function toggleMobileMenu() {
      document.getElementById('mobile-menu').classList.toggle('hidden');
    }

    function showToast(msg) {
      const toast = document.getElementById('toast');
      const toastMsg = document.getElementById('toast-msg');
      toastMsg.textContent = msg;
      toast.classList.remove('hidden');
      setTimeout(() => {
        toast.classList.add('hidden');
      }, 3000);
    }
  </script>
</body>
</html>
