<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';
import AppNavbar from './components/AppNavbar.vue';
import AppFooter from './components/AppFooter.vue';
import AppButton from './components/AppButton.vue';
import ExperienceCard from './components/ExperienceCard.vue';
import QuoterSidebar from './components/QuoterSidebar.vue';

// Estado de Diseño Activo (INTEGRACIÓN MULTI-MOCKUP)
const activeVariant = ref<'modern' | 'traditional'>('modern');

// Router simulado para el Mockup SPA: 'home' | 'catalog' | 'about' | 'contact' | 'experience'
const currentRoute = ref('home'); 
const selectedExperienceId = ref(1);

// Lógica de carrusel de imágenes/videos para la ficha técnica
const currentMediaIndex = ref(0);

// Estado de reproducción del video activo en el carrusel
const isVideoPlaying = ref(false);

// Estado del modal de Checkout/Pago integrado
const isCheckoutModalOpen = ref(false);
const isPaymentProcessing = ref(false);
const isPaymentSuccess = ref(false);

// Datos reales de las 6 Experiencias oficiales extraídas del sitio web
const experiences = [
  { 
    id: 1, 
    name: 'Encuentro con la Naturaleza y la Cultura / Encounter with Nature and Culture', 
    shortName: 'Caminatas Trawünko / Trawünko Spiritual Walk',
    duration: '4 horas (10:00 a 14:00 hrs)', 
    price: 35000,
    groupPrice: 240000,
    desc: 'Experiencia de Vida y Abundancia en la Naturaleza y el Conocimiento Mapuche por medio de una caminata interpretativa en la Reserva Natural Trawünko.',
    longDescParagraphs: [
      'Qué se vive: Vivirás una inmersión profunda y mística en la Reserva Natural Trawünko, caminando entre árboles nativos centenarios y helechos gigantes mientras escuchas el relajante murmullo de un traitraiko (caída de agua). Es un viaje al corazón de la biodiversidad local guiado por personas de nuestra comunidad que heredaron la sabiduría ancestral de la tierra.',
      'Qué se aprende: Aprenderás a reconocer árboles majestuosos y arbustos de nuestra medicina tradicional (Lawen), entendiendo la profunda relación de equilibrio armónico (Kümefelen) que une al che (persona) con el Mawizantu (Bosque Nativo). Comprenderás la historia de resistencia, resiliencia y el significado espiritual de los elementos naturales en nuestra cosmovisión.',
      'Qué se siente: Sentirás una reconexión espiritual inigualable, respirando aire puro y deteniendo el ritmo acelerado de la modernidad. El bosque te acogerá con su paz natural, y experimentarás la calidez de nuestro saludo tradicional (Chaliwun) y el compartir un alimento tradicional en comunidad (Mizawün).'
    ],
    img: '/images/caminatas.webp',
    media: [
      { type: 'video', youtubeId: 'Y9jep7SAtz0', url: '/images/caminatas.webp' }, // Video real de naturaleza del sur de Chile
      { type: 'image', url: '/images/caminatas.webp' },
      { type: 'image', url: '/images/talleres.webp' },
      { type: 'image', url: '/images/kumefelen.webp' },
      { type: 'image', url: '/images/lonko.webp' },
      { type: 'image', url: '/images/talleres.webp' }
    ],
    category: 'naturaleza',
    location: 'Trawünko',
    includes: [
      'Guía - anfitrión local en castellano (soporte bilingüe)',
      'Desayuno con degustación de alimentos tradicionales mapuche',
      'Almuerzo tradicional Mapuche en la comunidad',
      'Protocolo de inmersión y acceso a senderos y baños'
    ],
    excludes: [
      'Transporte al Sector Voipir',
      'Propinas y souvenirs',
      'Seguro de accidentes de trayecto'
    ],
    minParticipants: 8,
    maxParticipants: 20,
    guideLanguage: 'Español e Inglés',
    difficulty: 'Baja',
    reviews: [
      { author: 'María Eliana Leóz', origin: 'Argentina', rating: 5, comment: 'La caminata en el bosque nativo y el sonido de la caída de agua transmiten una paz increíble. El almuerzo estuvo exquisito.' },
      { author: 'John Davis', origin: 'Estados Unidos', rating: 5, comment: 'An absolute highlight of our trip to Chile. Learning about Lawen medicine directly from the source was eye-opening.' }
    ]
  },
  { 
    id: 2, 
    name: 'Salida Pedagógica: Naturaleza y Cultura / Pedagogical Outing: Nature and Culture', 
    shortName: 'Educación Ambiental / Environmental Education',
    duration: '5 horas (09:00 a 14:00 hrs)', 
    price: 15000, 
    groupPrice: 120000, // Precio grupal para grupos escolares
    desc: 'Programa orientado a Colegios que buscan generar instancias de aprendizaje fuera del aula, 100% vivencial en la Reserva Natural Trawünko.',
    longDescParagraphs: [
      'Qué se vive: Una salida de terreno totalmente interactiva y lúdica donde los estudiantes de enseñanza básica o media exploran de primera mano la biodiversidad de la selva valdiviana. Es un aula abierta en medio de la Reserva Trawünko, donde el bosque nativo enseña con sus formas, colores y sonidos.',
      'Qué se aprende: Se aprenden objetivos científicos y ecológicos alineados con el currículum escolar, tales como los ciclos biogeoquímicos, interdependencia de las especies, flora y fauna autóctona, y la importancia vital de la conservación ecológica desde el punto de vista de la cultura mapuche.',
      'Qué se siente: Los estudiantes experimentarán una estimulación profunda de todos los sentidos a través del tacto con cortezas, el aroma de hojas nativas, y el asombro del avistamiento de aves silvestres, fomentando un profundo respeto por el medio ambiente y el trabajo cooperativo.'
    ],
    img: '/images/talleres.webp',
    media: [
      { type: 'video', youtubeId: 'Y9jep7SAtz0', url: '/images/talleres.webp' },
      { type: 'image', url: '/images/talleres.webp' },
      { type: 'image', url: '/images/caminatas.webp' },
      { type: 'image', url: '/images/kumefelen.webp' },
      { type: 'image', url: '/images/lonko.webp' },
      { type: 'image', url: '/images/talleres.webp' }
    ],
    category: 'educacion',
    location: 'Trawünko',
    includes: [
      'Guía - anfitrión local educador en castellano',
      'Alimentación: Desayuno con alimentos tradicionales mapuche',
      'Protocolo de inmersión y caminata interpretativa por senderos',
      'Dinámicas escolares grupales e individuales, uso de baños'
    ],
    excludes: [
      'Transporte escolar de ida y vuelta',
      'Almuerzo completo de los estudiantes',
      'Materiales escolares no especificados'
    ],
    minParticipants: 15,
    maxParticipants: 35,
    guideLanguage: 'Español (soporte Inglés)',
    difficulty: 'Baja',
    reviews: [
      { author: 'Prof. Ana María', origin: 'Villarrica', rating: 5, comment: 'Alex Cumian y su equipo tienen un trato increíble con los jóvenes. Lograron una jornada educativa dinámica y muy significativa.' },
      { author: 'Prof. Marcos Andrade', origin: 'Temuco', rating: 5, comment: 'Excelente salida pedagógica. Los alumnos quedaron fascinados con los juegos interactivos de inmersión al bosque nativo.' }
    ]
  },
  { 
    id: 3, 
    name: 'Salida Pedagógica: Visita al Lonko y la Cultura / Pedagogical Visit to the Lonko', 
    shortName: 'Visita al Lonko / Visit to the Lonko',
    duration: '5 horas (09:00 a 14:00 hrs)', 
    price: 15000, 
    groupPrice: 120000,
    desc: 'Experiencia educativa en Kura Ruka, un Centro de Cultura y Educación Mapuche, donde les recibirá el Lonko Francisco Huilipan.',
    longDescParagraphs: [
      'Qué se vive: Los alumnos ingresarán a Kura Ruka, un espacio sagrado de preservación cultural. Vivirán una inmersión didáctica al ser recibidos por el Lonko Francisco Huilipan en una Ruka tradicional, compartiendo alimentos tradicionales y escuchando epeu (cuentos) u otras actividades lúdicas cerca del fuego.',
      'Qué se aprende: Se aprende de manera directa sobre la historia, principios mapuche, valores, y normas de convivencia tradicionales. Conocerán además el reglamento, el valor espiritual, el saludo ceremonial (Chaliwun) y la práctica del Palin (chueca).',
      'Qué se siente: Una profunda cercanía y respeto hacia la cultura originaria viva del Wallmapu. Se siente la hospitalidad incondicional, la calidez del hogar ancestral y el dinamismo lúdico del juego colectivo en equipo.'
    ],
    img: '/images/lonko.webp',
    media: [
      { type: 'video', youtubeId: 'Y9jep7SAtz0', url: '/images/lonko.webp' },
      { type: 'image', url: '/images/lonko.webp' },
      { type: 'image', url: '/images/caminatas.webp' },
      { type: 'image', url: '/images/talleres.webp' },
      { type: 'image', url: '/images/kumefelen.webp' },
      { type: 'image', url: '/images/lonko.webp' }
    ],
    category: 'educacion',
    location: 'Kura Ruka',
    includes: [
      'Recibimiento y relatos guiados por el Lonko Francisco Huilipan',
      'Guía - anfitrión local en castellano',
      'Alimentación: Desayuno con alimentos tradicionales en la Ruka',
      'Práctica reglamentada y materiales para el juego de Palin'
    ],
    excludes: [
      'Transporte escolar',
      'Almuerzo completo',
      'Propinas o compras personales en el centro Kura Ruka'
    ],
    minParticipants: 15,
    maxParticipants: 30,
    guideLanguage: 'Español',
    difficulty: 'Baja',
    reviews: [
      { author: 'Prof. Sonia Baeza', origin: 'Santiago', rating: 5, comment: 'Llevar a los niños a la ruka y jugar palin es algo que no se olvida jamás. Un aprendizaje vivencial insustituible.' },
      { author: 'Prof. Andrés Ramos', origin: 'Valdivia', rating: 5, comment: 'Una experiencia cultural de primer nivel. El Lonko Francisco Huilipan transmite sabiduría con una humildad conmovedora.' }
    ]
  },
  { 
    id: 4, 
    name: 'Salida a terreno Carreras de Estudios Superiores / Field Outing Higher Education', 
    shortName: 'Estudios Superiores / Higher Education',
    duration: '8 horas (09:00 a 17:00 hrs)', 
    price: 25000, 
    groupPrice: 350000,
    desc: 'Programa orientado a Universidades y Centros de Estudio que buscan lugares idóneos para salidas a terreno de diversas carreras en el Wallmapu.',
    longDescParagraphs: [
      'Qué se vive: Una jornada intensiva de campo que une la Reserva Natural Trawünko (bosque nativo) y Kura Ruka (centro de saberes). Diseñada para carreras de Turismo, Agronomía, Forestal, Sociología, Antropología, Pedagogías y Ciencias.',
      'Qué se aprende: Se estudian de manera crítica y comparativa las relaciones socio-ecológicas del Wallmapu. El director Alex Cumian guiará la interpretación del bosque valdiviano, mientras el Lonko Francisco Huilipan encabezará un Nütram (conversación) en el fogón sobre el patrimonio cultural mapuche.',
      'Qué se siente: Una profunda conexión intelectual y humana. Se siente el vigor de la naturaleza viva en la caminata, la calidez y hospitalidad en el fogón de la ruka, y un espacio propicio para la discusión académica y el asombro frente a la biodiversidad.'
    ],
    img: '/images/caminatas.webp',
    media: [
      { type: 'video', youtubeId: 'Y9jep7SAtz0', url: '/images/caminatas.webp' },
      { type: 'image', url: '/images/caminatas.webp' },
      { type: 'image', url: '/images/lonko.webp' },
      { type: 'image', url: '/images/kumefelen.webp' }
    ],
    category: 'educacion',
    location: 'Trawünko',
    includes: [
      'Guía - anfitrión local e interpretación mapuche por directores del centro',
      'Desayuno y Almuerzo tradicional Mapuche',
      'Acceso a la Reserva Natural y al Centro Kura Ruka, uso de baños',
      'Nütram (conversatorio) en el Fogón de la Ruka'
    ],
    excludes: [
      'Transporte de la delegación universitaria',
      'Materiales específicos de laboratorio o medición'
    ],
    minParticipants: 15,
    maxParticipants: 25,
    guideLanguage: 'Español (soporte Inglés)',
    difficulty: 'Baja',
    reviews: [
      { author: 'Luis Zúñiga', origin: 'U. de La Frontera', rating: 5, comment: 'Nuestros alumnos de antropología vivieron una experiencia inigualable. El relato del Lonko y el recorrido ecológico de Alex Cumian son excepcionales.' }
    ]
  },
  { 
    id: 5, 
    name: 'Encuentro con el Lonko y la Cultura / Encounter with the Lonko and Culture', 
    shortName: 'Encuentro con el Lonko / Meet the Lonko',
    duration: '4 horas (10:00 a 14:00 hrs)', 
    price: 35000, 
    groupPrice: 240000,
    desc: 'Experiencia con la Cultura viva Mapuche compartiendo con el Lonko Francisco Huilipan (autoridad tradicional Mapuche) en Kura Ruka.',
    longDescParagraphs: [
      'Qué se vive: Vivirás un encuentro íntimo y emotivo en Kura Ruka. El Lonko Francisco Huilipan te abrirá las puertas de su ruka tradicional para compartir relatos sobre la memoria histórica mapuche, sus principios, valores y el tejido de la tradición comunitaria.',
      'Qué se aprende: Aprenderás sobre la Cosmovisión Mapuche de boca de su propia autoridad ancestral. Conocerás y tocarás instrumentos musicales tradicionales (Ayekan) y aprenderás las reglas de cortesía, el Palin o chueca (deporte ancestral) y la importancia del respeto mutuo.',
      'Qué se siente: Sentirás una profunda emoción y una sensación de paz junto al fuego del fogón en la ruka. El sonido de los instrumentos tradicionales y la solemnidad de las palabras del Lonko te envolverán en una atmósfera de autenticidad cultural.'
    ],
    img: '/images/lonko.webp',
    media: [
      { type: 'video', youtubeId: 'Y9jep7SAtz0', url: '/images/lonko.webp' },
      { type: 'image', url: '/images/lonko.webp' },
      { type: 'image', url: '/images/kumefelen.webp' }
    ],
    category: 'cultura',
    location: 'Kura Ruka',
    includes: [
      'Guía - anfitrión local bilingüe',
      'Desayuno y almuerzo tradicionales mapuches en la Ruka',
      'Taller y práctica lúdica reglamentada de Palin (chueca)',
      'Acceso guiado a instrumentos tradicionales'
    ],
    excludes: [
      'Transporte',
      'Gastos personales'
    ],
    minParticipants: 8,
    maxParticipants: 18,
    guideLanguage: 'Español (soporte Inglés)',
    difficulty: 'Baja',
    reviews: [
      { author: 'Camila Rojas', origin: 'Santiago', rating: 5, comment: 'Escuchar al Lonko Francisco Huilipan cerca del fogón es algo que te toca el alma. El almuerzo tradicional estuvo increíble.' }
    ]
  },
  { 
    id: 6, 
    name: 'Encuentro con el Sabor y Conocimiento Mapuche / Meet Mapuche Flavour and Knowledge', 
    shortName: 'Secretos de la Ñaña / Culinary Secrets',
    duration: '4 horas (10:00 a 14:00 hrs)', 
    price: 35000, 
    groupPrice: 240000,
    desc: 'Es una Experiencia en la que se mezcla el sabor con el conocimiento cultural mapuche. Deleitate con los secretos gastronómicos de la lamgen María Eliana Catrilef.',
    longDescParagraphs: [
      'Qué se vive: Ingresarás a la ruka tradicional Huisca (Secretos de la Ñaña) donde serás recibido por la lamgen María Eliana Catrilef, heredera y guardiana de las recetas culinarias tradicionales mapuches. Compartirás un Mizawün sublime para deleitar tu paladar mientras escuchas relatos familiares.',
      'Qué se aprende: Aprenderás sobre el origen de los ingredientes locales, el huerto orgánico, el tostado tradicional del trigo en el fogón y las técnicas culinarias de recolección y conservación del bosque valdiviano en armonía ecológica.',
      'Qué se siente: Una inigualable experiencia al paladar, llena de texturas rústicas y aromas tradicionales cerca del fuego. Sentirás la calidez familiar de la ñaña, compartiendo saberes culinarios e historias ancestrales que transforman el acto de comer en un ritual místico.'
    ],
    img: '/images/talleres.webp',
    media: [
      { type: 'video', youtubeId: 'Y9jep7SAtz0', url: '/images/talleres.webp' },
      { type: 'image', url: '/images/talleres.webp' },
      { type: 'image', url: '/images/kumefelen.webp' }
    ],
    category: 'gastronomia',
    location: 'Secretos de la Ñaña',
    includes: [
      'Guía - anfitrión local y clase culinaria oral con María Eliana Catrilef',
      'Desayuno y Almuerzo de gastronomía ancestral mapuche premium',
      'Taller práctico de tostado de trigo en fogón y visita a huerto',
      'Acceso a ruka tradicional y servicios sanitarios'
    ],
    excludes: [
      'Transporte',
      'Licores y preparaciones fuera del menú tradicional'
    ],
    minParticipants: 8,
    maxParticipants: 15,
    guideLanguage: 'Español',
    difficulty: 'Baja',
    reviews: [
      { author: 'Cristóbal Muñoz', origin: 'Concepción', rating: 5, comment: 'La lamgen María Eliana te abre las puertas de su vida y cocina. El tostado de trigo es espectacular y el almuerzo exquisito.' }
    ]
  }
];

const locaciones = [
  { name: 'Trawünko', img: '/images/caminatas.webp', desc: 'Reserva Natural y Senderos' },
  { name: 'Kiñewün', img: '/images/kumefelen.webp', desc: 'Centro de Retiro y Bienestar' },
  { name: 'Secretos de la Ñaña', img: '/images/talleres.webp', desc: 'Gastronomía Ancestral' },
  { name: 'Kura Ruka', img: '/images/lonko.webp', desc: 'Centro de Cultura y Educación' }
];

// State for active reservation
const activeBooking = ref({
  experienceId: 1,
  adults: 1,
  students: 0,
  date: '',
  includePhotos: false
});

// Catalog Filters State
const filterCategory = ref('todos');
const filterLocation = ref('todos');
const maxPrice = ref(50000);
const sortBy = ref('defecto');

const filteredExperiences = computed(() => {
  return experiences
    .filter(exp => {
      const matchCat = filterCategory.value === 'todos' || exp.category === filterCategory.value;
      const matchLoc = filterLocation.value === 'todos' || exp.location === filterLocation.value;
      const matchPrice = exp.price <= maxPrice.value;
      return matchCat && matchLoc && matchPrice;
    })
    .sort((a, b) => {
      if (sortBy.value === 'precio-asc') return a.price - b.price;
      if (sortBy.value === 'precio-desc') return b.price - a.price;
      return 0;
    });
});

const activeExperience = computed(() => experiences.find(e => e.id === selectedExperienceId.value));

const totalPrice = computed(() => {
  const exp = experiences.find(e => e.id === activeBooking.value.experienceId);
  if (!exp) return 0;
  const adultTotal = activeBooking.value.adults * exp.price;
  const studentTotal = activeBooking.value.students * (exp.price * 0.8);
  let total = adultTotal + studentTotal;
  if (activeBooking.value.includePhotos) {
    total += 25000;
  }
  return total;
});

const formatCLP = (value: number) => {
  return new Intl.NumberFormat('es-CL', { style: 'currency', currency: 'CLP' }).format(value);
};

const formatUSD = (value: number) => {
  return new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD' }).format(value / 950);
};

// Checkout state
const checkoutForm = ref({
  fullName: '',
  email: '',
  phone: '',
  country: '',
  language: 'es',
  observations: '',
  paymentMethod: 'webpay'
});

// Navigation function
const navigateTo = (route: string, id?: number) => {
  currentRoute.value = route;
  if (id) {
    selectedExperienceId.value = id;
    activeBooking.value.experienceId = id;
    // Reset control variables for the sheet
    currentMediaIndex.value = 0;
    isVideoPlaying.value = false;
  }
  window.scrollTo({ top: 0, behavior: 'smooth' });
};

// Al confirmar en el cotizador lateral, abrimos el modal directamente (REQUERIMIENTO: Todo integrado en la misma vista)
const handleQuoterBooking = (data: { experienceId: number; adults: number; students: number; date: string; includePhotos: boolean }) => {
  activeBooking.value = data;
  selectedExperienceId.value = data.experienceId;
  
  // Abrimos el modal de pago integrado
  isPaymentSuccess.value = false;
  isPaymentProcessing.value = false;
  isCheckoutModalOpen.value = true;
};

// Simulación de procesamiento de pago
const processPaymentMock = () => {
  isPaymentProcessing.value = true;
  setTimeout(() => {
    isPaymentProcessing.value = false;
    isPaymentSuccess.value = true;
  }, 2500); // 2.5s de simulación de pasarela Webpay/Stripe
};

// Cerrar modal de checkout
const closeCheckoutModal = () => {
  isCheckoutModalOpen.value = false;
  isPaymentSuccess.value = false;
  isPaymentProcessing.value = false;
};

// Image gallery controllers
const nextMedia = () => {
  if (activeExperience.value) {
    currentMediaIndex.value = (currentMediaIndex.value + 1) % activeExperience.value.media.length;
    isVideoPlaying.value = false;
  }
};

const prevMedia = () => {
  if (activeExperience.value) {
    currentMediaIndex.value = (currentMediaIndex.value - 1 + activeExperience.value.media.length) % activeExperience.value.media.length;
    isVideoPlaying.value = false;
  }
};

const selectMedia = (idx: number) => {
  currentMediaIndex.value = idx;
  isVideoPlaying.value = false;
};

const formattedBookingDate = computed(() => {
  if (!activeBooking.value.date) return '';
  const [year, month, day] = activeBooking.value.date.split('-');
  return `${day}/${month}/${year}`;
});

// Función de Alternar Tema (SUBRUTA / PARAMETRO MOCK)
const toggleVariant = (variant: 'modern' | 'traditional') => {
  activeVariant.value = variant;
  const newUrl = `${window.location.origin}${window.location.pathname}?variant=${variant}`;
  window.history.pushState({ path: newUrl }, '', newUrl);
};

// Leer parámetro en carga inicial para simular subruta
onMounted(() => {
  const urlParams = new URLSearchParams(window.location.search);
  const variantParam = urlParams.get('variant');
  if (variantParam === 'traditional' || variantParam === 'modern') {
    activeVariant.value = variantParam;
  }
});
</script>

<template>
  <!-- Envoltura principal reactiva de Tema -->
  <div 
    class="min-h-screen transition-all duration-500 flex flex-col font-sans relative"
    :class="activeVariant === 'traditional' ? 'theme-traditional bg-(--color-crema-newen) wood-texture text-gray-900' : 'theme-modern bg-gray-50 text-gray-800'"
  >
    
    <!-- Header/Navbar modularizado y reactivo al tema -->
    <AppNavbar :current-route="currentRoute" :active-variant="activeVariant" @navigate="navigateTo" />

    <main class="flex-grow">
    <!-- ============================================== -->
    <!-- VIEW: HOME (FRONT PAGE)                        -->
    <!-- ============================================== -->
    <template v-if="currentRoute === 'home'">
      <!-- Hero Section -->
      <section class="relative bg-gray-900 h-[75vh] flex items-center justify-center text-center px-4 overflow-hidden">
        <div class="absolute inset-0">
          <img src="/images/caminatas.webp" class="w-full h-full object-cover opacity-50 scale-105" alt="Araucanía" />
        </div>
        <div class="relative z-10 max-w-4xl mx-auto mt-10">
          <span 
            class="font-bold tracking-widest uppercase text-sm mb-4 block drop-shadow-md"
            :class="activeVariant === 'traditional' ? 'text-(--color-gold-newen) font-serif-artisanal' : 'text-(--color-gold-newen)'"
          >Sector Voipir, Villarrica - La Araucanía</span>
          
          <h1 
            class="text-5xl md:text-7xl font-extrabold text-white tracking-tight mb-6 drop-shadow-md"
            :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''"
          >
            Turismo Regenerativo <br/> 
            <span class="text-white/90" :class="activeVariant === 'traditional' ? 'italic' : 'font-serif italic'">Mapuche</span>
          </h1>
          
          <p class="text-lg md:text-xl text-gray-100 mb-10 max-w-2xl mx-auto font-light drop-shadow-lg leading-relaxed">
            Únete a nosotros en un viaje inolvidable al corazón de la cultura mapuche. Ven a conocer la riqueza cultural del Wallmapu.
          </p>
          <div class="flex justify-center">
            <AppButton variant="primary" :active-variant="activeVariant" size="lg" @click="navigateTo('catalog')">
              Reservar Ahora
            </AppButton>
          </div>
        </div>
      </section>

      <!-- Divisor Geométrico Mapuche si es Tradicional -->
      <div v-if="activeVariant === 'traditional'" class="winkul-divider my-4"></div>

      <!-- Sección de Cosmovisión Mapuche (Diferenciador Exclusivo Propuesta 1) -->
      <section v-if="activeVariant === 'traditional'" id="cosmovision" class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-16 text-center">
        <div class="bg-(--color-green-newen)/5 border-2 border-(--color-gold-newen)/20 p-10 rounded-3xl max-w-4xl mx-auto space-y-6">
          <span class="text-(--color-gold-newen) font-bold tracking-widest uppercase text-xs">SABIDURÍA DE LA TIERRA</span>
          <h2 class="text-3xl md:text-4xl font-bold text-gray-900 font-serif-artisanal">Cosmovisión y Conexión con el Wallmapu</h2>
          <p class="text-gray-700 text-lg leading-relaxed max-w-2xl mx-auto italic">
            "Para el pueblo Mapuche, el ser humano no es dueño de la naturaleza, sino parte de ella. Cada cerro, río y bosque tiene su fuerza (Ngen) con la que debemos coexistir en armonía y respeto profundo."
          </p>
          <div class="flex justify-center gap-4 text-xs font-bold text-gray-500 uppercase tracking-widest pt-4">
            <span>Kümefelen (Equilibrio)</span>
            <span>·</span>
            <span>Mawizantu (Bosque)</span>
            <span>·</span>
            <span>Che (Persona)</span>
          </div>
        </div>
      </section>

      <div v-if="activeVariant === 'traditional'" class="winkul-divider my-4"></div>

      <!-- Preview de Experiencias (Sección 2.1 - Contenedor Preview) -->
      <section class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-20 border-b border-gray-200">
        <div class="flex justify-between items-end mb-12">
          <div>
            <h2 
              class="text-4xl font-extrabold text-gray-900 mb-4"
              :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''"
            >Experiencias Destacadas</h2>
            <p class="text-gray-600 max-w-2xl">Un vistazo a nuestras principales experiencias culturales de Wallmapu.</p>
          </div>
          <AppButton variant="primary" :active-variant="activeVariant" @click="navigateTo('catalog')">
            Ver todas
          </AppButton>
        </div>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
          <ExperienceCard 
            v-for="exp in experiences.slice(0, 3)" 
            :key="exp.id" 
            :experience="exp" 
            :active-variant="activeVariant"
            @view-detail="(id) => navigateTo('experience', id)" 
          />
        </div>
      </section>

      <!-- Locaciones -->
      <section 
        class="py-20 border-b border-gray-200"
        :class="activeVariant === 'traditional' ? '' : 'bg-gray-100'"
      >
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div class="text-center mb-12">
            <h2 
              class="text-3xl font-extrabold text-gray-900 mb-4"
              :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''"
            >Nuestras Locaciones</h2>
            <p class="text-gray-600 max-w-2xl mx-auto">Lugares llenos de historia and naturaleza para vivir la cultura mapuche.</p>
          </div>
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
            <div v-for="loc in locaciones" :key="loc.name" class="bg-white rounded-2xl overflow-hidden shadow-sm border border-gray-200 hover:shadow-md transition-shadow">
              <img :src="loc.img" :alt="loc.name" class="w-full h-40 object-cover" />
              <div class="p-4 text-center">
                <h3 class="font-bold text-gray-900" :class="activeVariant === 'traditional' ? 'font-serif-artisanal text-lg' : ''">{{ loc.name }}</h3>
                <p class="text-xs text-gray-500 mt-1">{{ loc.desc }}</p>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Testimonios e Integracion TripAdvisor -->
      <section class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-20">
        <div class="bg-(--color-green-newen) rounded-3xl p-10 text-white relative overflow-hidden">
          <h3 class="text-2xl font-bold mb-8 relative z-10 flex items-center gap-2" :class="activeVariant === 'traditional' ? 'font-serif-artisanal text-3xl' : ''">
            <svg class="w-6 h-6 text-(--color-gold-newen)" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11.049 2.927c.3-.921 1.603-.921 1.902 0l1.519 4.674a1 1 0 00.95.69h4.915c.969 0 1.371 1.24.588 1.81l-3.976 2.888a1 1 0 00-.363 1.118l1.518 4.674c.3.922-.755 1.688-1.538 1.118l-3.976-2.888a1 1 0 00-1.176 0l-3.976 2.888c-.783.57-1.838-.197-1.538-1.118l1.518-4.674a1 1 0 00-.363-1.118l-3.976-2.888c-.784-.57-.38-1.81.588-1.81h4.914a1 1 0 00.951-.69l1.519-4.674z"></path></svg>
            Reseñas TripAdvisor (Calificación promedio: 5.0)
          </h3>
          <div class="grid md:grid-cols-2 lg:grid-cols-4 gap-6 relative z-10">
            <div class="bg-black/20 p-5 rounded-2xl backdrop-blur-sm border border-white/10 flex flex-col justify-between">
              <p class="italic text-xs mb-4 leading-relaxed">"La experiencia mapuche en Newen Leufu fue verdaderamente transformadora. La conexión con la naturaleza y la cultura fue profunda."</p>
              <span class="font-bold text-(--color-crema-newen) text-xs">— María Eliana Leóz</span>
            </div>
            <div class="bg-black/20 p-5 rounded-2xl backdrop-blur-sm border border-white/10 flex flex-col justify-between">
              <p class="italic text-xs mb-4 leading-relaxed">"Durante mi visita sentí una profunda sensación de paz y pertenencias. Las tradiciones que compartieron fueron esclarecedoras y hermosas."</p>
              <span class="font-bold text-(--color-crema-newen) text-xs">— Soledad Gómez</span>
            </div>
            <div class="bg-black/20 p-5 rounded-2xl backdrop-blur-sm border border-white/10 flex flex-col justify-between">
              <p class="italic text-xs mb-4 leading-relaxed">"Newen Leufu ofrece un viaje único al corazón de la cultura mapuche. La hospitalidad y el conocimiento compartido fueron excepcionales."</p>
              <span class="font-bold text-(--color-crema-newen) text-xs">— Luis Zúñiga</span>
            </div>
            <div class="bg-black/20 p-5 rounded-2xl backdrop-blur-sm border border-white/10 flex flex-col justify-between">
              <p class="italic text-xs mb-4 leading-relaxed">"El entorno natural de la Reserva Trawünko es majestuoso. El juego de Palin y la comida en la ruka fueron insuperables."</p>
              <span class="font-bold text-(--color-crema-newen) text-xs">— Andrés Ramos</span>
            </div>
          </div>
        </div>
      </section>
    </template>

    <!-- ============================================== -->
    <!-- VIEW: CATALOG (VISTA DEDICADA INDIVIDUAL)      -->
    <!-- ============================================== -->
    <template v-if="currentRoute === 'catalog'">
      <div class="bg-gray-100 border-b border-gray-200">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-3 text-sm text-gray-500">
          <a href="#" @click.prevent="navigateTo('home')" class="hover:text-(--color-green-newen)">Inicio</a>
          <span class="mx-2">/</span>
          <span class="text-gray-900 font-medium">Catálogo de Experiencias</span>
        </div>
      </div>

      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12">
        <div class="text-center mb-12">
          <h1 class="text-4xl font-extrabold text-gray-900 mb-4" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''">Catálogo Completo</h1>
          <p class="text-lg text-gray-600 max-w-2xl mx-auto">Usa los filtros de abajo para explorar las 6 experiencias oficiales bilingües de la Cooperativa.</p>
        </div>

        <!-- Barra de Filtros y Ordenamiento (REQUERIMIENTOS.md 2.3) -->
        <div class="bg-white p-6 rounded-3xl shadow-sm border border-gray-100 mb-10 grid grid-cols-1 md:grid-cols-4 gap-6 items-end">
          <div>
            <label class="block text-xs font-bold text-gray-500 uppercase tracking-wider mb-2">Categoría</label>
            <select v-model="filterCategory" class="w-full rounded-xl border-gray-200 border-2 bg-gray-50 px-4 py-3 focus:border-(--color-green-newen) outline-none text-sm font-medium">
              <option value="todos">Todos los Tipos</option>
              <option value="naturaleza">Naturaleza</option>
              <option value="educacion">Educación / Colegios / Ues</option>
              <option value="cultura">Cultura</option>
              <option value="gastronomia">Gastronomía</option>
            </select>
          </div>
          <div>
            <label class="block text-xs font-bold text-gray-500 uppercase tracking-wider mb-2">Ubicación / Locación</label>
            <select v-model="filterLocation" class="w-full rounded-xl border-gray-200 border-2 bg-gray-50 px-4 py-3 focus:border-(--color-green-newen) outline-none text-sm font-medium">
              <option value="todos">Todas las Locaciones</option>
              <option value="Trawünko">Trawünko</option>
              <option value="Kura Ruka">Kura Ruka</option>
              <option value="Secretos de la Ñaña">Secretos de la Ñaña</option>
            </select>
          </div>
          <div>
            <label class="block text-xs font-bold text-gray-500 uppercase tracking-wider mb-2">Precio Máximo: {{ formatCLP(maxPrice) }}</label>
            <input type="range" min="10000" max="50000" step="5000" v-model.number="maxPrice" class="w-full h-2 bg-gray-200 rounded-lg appearance-none cursor-pointer accent-(--color-green-newen)" />
          </div>
          <div>
            <label class="block text-xs font-bold text-gray-500 uppercase tracking-wider mb-2">Ordenar por</label>
            <select v-model="sortBy" class="w-full rounded-xl border-gray-200 border-2 bg-gray-50 px-4 py-3 focus:border-(--color-green-newen) outline-none text-sm font-medium">
              <option value="defecto">Por Defecto</option>
              <option value="precio-asc">Precio: de Menor a Mayor</option>
              <option value="precio-desc">Precio: de Mayor a Menor</option>
            </select>
          </div>
        </div>

        <!-- Grid de Experiencias Filtradas -->
        <div v-if="filteredExperiences.length > 0" class="grid grid-cols-1 md:grid-cols-3 gap-8">
          <ExperienceCard 
            v-for="exp in filteredExperiences" 
            :key="exp.id" 
            :experience="exp" 
            :active-variant="activeVariant"
            @view-detail="(id) => navigateTo('experience', id)" 
          />
        </div>
        <div v-else class="text-center py-20 bg-white rounded-3xl border border-gray-200">
          <svg class="w-16 h-16 text-gray-300 mx-auto mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.172 16.172a4 4 0 015.656 0M9 10h.01M15 10h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>
          <p class="text-gray-500 font-bold">No se encontraron experiencias con los filtros seleccionados.</p>
        </div>
      </div>
    </template>

    <!-- ============================================== -->
    <!-- VIEW: ABOUT US (QUIÉNES SOMOS)                 -->
    <!-- ============================================== -->
    <template v-if="currentRoute === 'about'">
      <div class="bg-gray-100 border-b border-gray-200">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-3 text-sm text-gray-500">
          <a href="#" @click.prevent="navigateTo('home')" class="hover:text-(--color-green-newen)">Inicio</a>
          <span class="mx-2">/</span>
          <span class="text-gray-900 font-medium">Quiénes Somos</span>
        </div>
      </div>

      <div class="max-w-5xl mx-auto px-4 sm:px-6 lg:px-8 py-16 space-y-12">
        <div class="text-center">
          <h1 class="text-5xl font-extrabold text-gray-900 mb-4" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''">Quiénes Somos</h1>
          <p class="text-xl text-gray-600 max-w-2xl mx-auto italic" :class="activeVariant === 'traditional' ? 'font-serif-artisanal' : ''">Identidad Territorial Mapuche y Turismo Cultural Regenerativo</p>
        </div>

        <div class="rounded-3xl overflow-hidden shadow-2xl border border-gray-200 bg-white p-2">
          <img src="/images/quienes_somos.webp" alt="Comunidad Newen Leufu" class="w-full h-96 object-cover rounded-2xl" />
        </div>

        <div class="bg-white p-8 md:p-12 rounded-3xl border border-gray-150 shadow-sm space-y-6 text-gray-700 text-lg leading-relaxed">
          <h3 class="text-3xl font-bold text-gray-900 mb-4 border-b border-gray-100 pb-3" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''">Nuestra Misión y Origen</h3>
          <p>
            La Cooperativa de Turismo y Desarrollo Territorial Mapuche Newen Leufu es una organización indígena ubicada en el Camino Villarrica–Lican Ray km 5, Sector Voipir, en la zona lacustre de La Araucanía (Chile).
          </p>
          <p>
            Nacemos de la necesidad de dar a conocer la riqueza del Wallmapu a través de un turismo regenerativo consciente, deteniendo los ritmos modernos y ofreciendo una inmersión espiritual real que comparte conocimientos ancestrales directamente de nuestra comunidad y sus líderes, como el Lonko Francisco Huilipan.
          </p>
          <p>
            Contamos con 4 locaciones turísticas operativas (Trawünko, Kiñewün, Secretos de la Ñaña y Kura Ruka), cada una con un propósito y rol comercial, pero unidas bajo una misma visión espiritual: la coexistencia armónica de la persona con el bosque y los seres de la naturaleza.
          </p>
        </div>

        <!-- Locaciones integradas -->
        <div class="space-y-6">
          <h3 class="text-3xl font-extrabold text-gray-900 text-center" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''">Nuestras 4 Locaciones Operativas</h3>
          <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
            <div v-for="loc in locaciones" :key="loc.name" class="bg-white p-6 rounded-2xl border border-gray-150 shadow-sm flex gap-4">
              <img :src="loc.img" :alt="loc.name" class="w-24 h-24 object-cover rounded-xl" />
              <div>
                <h4 class="font-bold text-gray-900 text-xl mb-1" :class="activeVariant === 'traditional' ? 'font-serif-artisanal' : ''">{{ loc.name }}</h4>
                <p class="text-gray-500 text-sm mb-2 font-bold">{{ loc.desc }}</p>
                <p class="text-gray-600 text-xs">Espacio preparado para conectar con la naturaleza, realizar actividades colectivas y compartir alimentos tradicionales.</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </template>

    <!-- ============================================== -->
    <!-- VIEW: CONTACT (CONTACTO DEDICADO INDIVIDUAL)   -->
    <!-- ============================================== -->
    <template v-if="currentRoute === 'contact'">
      <div class="bg-gray-100 border-b border-gray-200">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-3 text-sm text-gray-500">
          <a href="#" @click.prevent="navigateTo('home')" class="hover:text-(--color-green-newen)">Inicio</a>
          <span class="mx-2">/</span>
          <span class="text-gray-900 font-medium">Contacto</span>
        </div>
      </div>

      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-16">
        <div class="text-center mb-12">
          <h1 class="text-5xl font-extrabold text-gray-900 mb-4" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''">Contacto</h1>
          <p class="text-lg text-gray-600 max-w-2xl mx-auto">Comunícate con nosotros para coordinar visitas de colegios, grupos de empresas o reservas especiales.</p>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-2 gap-12 items-start">
          <div class="bg-white p-8 rounded-3xl border border-gray-150 shadow-sm space-y-6">
            <h3 class="text-xl font-bold text-gray-900 border-b border-gray-100 pb-3" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''">Formulario de Contacto Simple</h3>
            <form class="space-y-4">
              <div>
                <label class="block text-sm font-semibold text-gray-700 mb-1">Nombre Completo</label>
                <input type="text" class="w-full rounded-xl border-gray-200 border-2 px-4 py-3 text-sm focus:border-(--color-green-newen) outline-none" placeholder="Ingresa tu nombre" />
              </div>
              <div>
                <label class="block text-sm font-semibold text-gray-700 mb-1">Correo Electrónico</label>
                <input type="email" class="w-full rounded-xl border-gray-200 border-2 px-4 py-3 text-sm focus:border-(--color-green-newen) outline-none" placeholder="tu@correo.com" />
              </div>
              <div>
                <label class="block text-sm font-semibold text-gray-700 mb-1">Mensaje</label>
                <textarea rows="4" class="w-full rounded-xl border-gray-200 border-2 px-4 py-3 text-sm focus:border-(--color-green-newen) outline-none" placeholder="Escribe tu consulta detalladamente aquí..."></textarea>
              </div>
              <AppButton type="button" :active-variant="activeVariant" class="w-full justify-center">Enviar Mensaje</AppButton>
            </form>
            
            <div class="border-t border-gray-100 pt-6 flex flex-col sm:flex-row gap-4 justify-between items-center">
              <a href="https://wa.me/56989303801" target="_blank" class="inline-flex items-center gap-2 bg-green-500 hover:bg-green-600 text-white font-bold px-6 py-3 rounded-xl shadow-sm transition-transform hover:-translate-y-0.5 active:translate-y-0 text-sm">
                <svg class="w-5 h-5 fill-current" viewBox="0 0 24 24"><path d="M.057 24l1.687-6.163c-1.041-1.804-1.588-3.849-1.587-5.946C.06 5.348 5.397.01 12.008.01c3.202.001 6.212 1.246 8.477 3.513 2.262 2.268 3.507 5.28 3.505 8.484-.004 6.657-5.34 11.997-11.953 11.997-2.005-.001-3.973-.502-5.753-1.464L0 24zm6.59-4.846c1.673.991 3.325 1.516 5.353 1.517 5.568 0 10.103-4.52 10.106-10.076.002-2.693-1.042-5.225-2.942-7.13C17.261 1.56 14.74 1.516 12.011 1.516c-5.568 0-10.105 4.52-10.108 10.078-.002 1.951.522 3.86 1.52 5.567l-.999 3.648 3.766-.987z"/></svg>
                Conversar por WhatsApp Business
              </a>
              <div class="text-right text-xs text-gray-400">
                <p>Atención directa de</p>
                <p class="font-bold text-gray-700">Lunes a Domingo</p>
              </div>
            </div>
          </div>
          
          <div class="bg-white p-4 rounded-3xl border border-gray-150 shadow-sm h-full flex flex-col">
            <h3 class="text-xl font-bold text-gray-900 mb-4 px-4 pt-4" :class="activeVariant === 'traditional' ? 'font-serif-artisanal' : ''">Nuestra Ubicación</h3>
            <div class="flex-1 min-h-[350px] rounded-2xl overflow-hidden border border-gray-200 relative">
              <iframe 
                class="absolute inset-0 w-full h-full"
                src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d12173.0645012543!2d-72.24727402636718!3d-39.31758557930985!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x96140fc30a47f0e9%3A0xe6bfb4c09d5718a!2sSector%20Voipir%2C%20Villarrica%2C%20La%20Araucan%C3%ADa!5e0!3m2!1ses-419!2scl!4v1700000000000!5m2!1ses-419!2scl" 
                frameborder="0" 
                allowfullscreen 
                loading="lazy"
              ></iframe>
            </div>
            <div class="p-4 text-xs text-gray-500">
              <p class="font-semibold text-gray-800">Camino Villarrica – Lican Ray km 5, Sector Voipir, Villarrica, La Araucanía</p>
              <p class="mt-1">A solo 10 minutos del centro de Villarrica.</p>
            </div>
          </div>
        </div>
      </div>
    </template>

    <!-- ============================================== -->
    <!-- VIEW: EXPERIENCE DETAIL (FICHA INDIVIDUAL)     -->
    <!-- ============================================== -->
    <template v-if="currentRoute === 'experience' && activeExperience">
      
      <!-- Breadcrumbs -->
      <div class="bg-gray-100 border-b border-gray-200">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-3 text-sm text-gray-500 flex justify-between items-center">
          <div>
            <a href="#" @click.prevent="navigateTo('home')" class="hover:text-(--color-green-newen)">Inicio</a>
            <span class="mx-2">/</span>
            <span class="text-gray-900 font-medium">{{ activeExperience.shortName }}</span>
          </div>
          <div class="flex gap-2 font-bold text-xs">
            <span class="text-(--color-green-newen)">ES</span>
            <span>|</span>
            <span class="text-gray-400 cursor-pointer hover:text-gray-600">EN</span>
          </div>
        </div>
      </div>

      <div class="flex-1 max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12 grid grid-cols-1 lg:grid-cols-3 gap-12 w-full">
        
        <!-- Contenido Ficha -->
        <div class="lg:col-span-2 space-y-10">
          
          <!-- Título y Media Gallery / Video responsivo (Sección 2.2) -->
          <div>
            <h1 class="text-3xl md:text-4xl font-extrabold text-gray-900 mb-6 leading-tight tracking-tight" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold text-3xl' : ''">
              {{ activeExperience.name }}
            </h1>
            
            <!-- Carrusel Principal Multimodal (Soporta Videos o Imágenes) -->
            <div 
              class="relative bg-white p-2"
              :class="activeVariant === 'traditional' 
                ? 'rounded-md shadow-lg border-2 border-(--color-gold-newen)/15' 
                : 'rounded-3xl shadow-lg border border-gray-150'"
            >
              <div class="relative h-64 sm:h-[450px] w-full rounded-2xl overflow-hidden bg-gray-950 flex items-center justify-center">
                
                <!-- Si el elemento actual es de tipo Video -->
                <template v-if="activeExperience.media[currentMediaIndex].type === 'video'">
                  <template v-if="!isVideoPlaying">
                    <img :src="activeExperience.media[currentMediaIndex].url" class="absolute inset-0 w-full h-full object-cover opacity-60" alt="Video Cover" />
                    <button @click="isVideoPlaying = true" class="w-20 h-20 bg-(--color-green-newen) hover:bg-green-800 text-white rounded-full flex items-center justify-center relative z-10 shadow-2xl hover:scale-110 transition-transform focus:outline-none">
                      <svg class="w-10 h-10 text-white ml-1.5" fill="currentColor" viewBox="0 0 24 24"><path d="M8 5v14l11-7z"/></svg>
                    </button>
                    <div class="absolute bottom-4 left-6 z-10 text-white font-bold text-xs bg-black/40 px-3 py-1.5 rounded-lg flex items-center gap-1.5">
                      <svg class="w-4 h-4 text-red-500 animate-pulse" fill="currentColor" viewBox="0 0 20 20"><path d="M2 6a2 2 0 012-2h6a2 2 0 012 2v8a2 2 0 01-2 2H4a2 2 0 01-2-2V6zM14.553 7.106A1 1 0 0014 8v4a1 1 0 00.553.894l2 1A1 1 0 0018 13V7a1 1 0 00-1.447-.894l-2 1z"/></svg>
                      Ver Video de Presentación
                    </div>
                  </template>
                  <template v-else>
                    <iframe 
                      class="w-full h-full" 
                      :src="`https://www.youtube.com/embed/${activeExperience.media[currentMediaIndex].youtubeId}?autoplay=1&mute=0`" 
                      frameborder="0" 
                      allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
                      allowfullscreen
                    ></iframe>
                  </template>
                </template>

                <!-- Si el elemento actual es de tipo Imagen -->
                <template v-else>
                  <img :src="activeExperience.media[currentMediaIndex].url" class="w-full h-full object-cover transition-all duration-500" alt="Foto de la Experiencia" />
                </template>
                
                <!-- Botones de Navegación del Carrusel -->
                <button @click="prevMedia" class="absolute left-4 top-1/2 -translate-y-1/2 bg-white/80 hover:bg-white p-3 rounded-full shadow-md text-gray-800 focus:outline-none transition-colors z-20">
                  <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M15 19l-7-7 7-7"></path></svg>
                </button>
                <button @click="nextMedia" class="absolute right-4 top-1/2 -translate-y-1/2 bg-white/80 hover:bg-white p-3 rounded-full shadow-md text-gray-800 focus:outline-none transition-colors z-20">
                  <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M9 5l7 7-7 7"></path></svg>
                </button>
              </div>
              
              <!-- Thumbnails interactivos de carrusel -->
              <div class="flex gap-2 justify-center mt-3 overflow-x-auto pb-1 px-2">
                <button 
                  v-for="(med, idx) in activeExperience.media" 
                  :key="idx" 
                  @click="selectMedia(idx)"
                  class="w-16 h-12 overflow-hidden border-2 transition-all shrink-0 relative"
                  :class="[
                    activeVariant === 'traditional' ? 'rounded-md' : 'rounded-lg',
                    currentMediaIndex === idx ? 'border-(--color-green-newen) scale-105' : 'border-transparent opacity-60 hover:opacity-100'
                  ]"
                >
                  <img :src="med.url" class="w-full h-full object-cover" />
                  <div v-if="med.type === 'video'" class="absolute inset-0 flex items-center justify-center bg-black/30 text-white">
                    <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20"><path d="M10 18a8 8 0 100-16 8 8 0 000 16zM9.555 7.168l5 4a1 1 0 010 1.664l-5 4A1 1 0 018 16V8a1 1 0 011.555-.832z"/></svg>
                  </div>
                </button>
              </div>
            </div>
          </div>

          <!-- Descripción Completa -->
          <div class="prose max-w-none bg-white p-8 rounded-3xl border border-gray-100 shadow-sm space-y-6">
            <h3 class="text-2xl font-bold text-gray-900 border-b border-gray-100 pb-3 mb-2 flex items-center gap-2" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''">
              <svg class="w-6 h-6 text-(--color-gold-newen)" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"></path></svg>
              Descripción de la Experiencia
            </h3>
            <div class="space-y-4 text-gray-700 text-base md:text-lg leading-relaxed">
              <p v-for="(p, index) in activeExperience.longDescParagraphs" :key="index" class="bg-gray-50/50 p-4 rounded-xl border border-gray-100">
                {{ p }}
              </p>
            </div>
          </div>

          <!-- Ficha Técnica Completa -->
          <div class="bg-white p-8 rounded-3xl border border-gray-100 shadow-sm">
            <h3 class="text-2xl font-bold text-gray-900 border-b border-gray-100 pb-4 mb-6 flex items-center gap-2" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''">
              <svg class="w-6 h-6 text-(--color-green-newen)" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-3 7h3m-3 4h3m-6-4h.01M9 16h.01"></path></svg>
              Ficha Técnica del Servicio
            </h3>
            <div class="overflow-hidden border border-gray-200 rounded-2xl">
              <table class="w-full text-left border-collapse text-sm">
                <thead>
                  <tr class="bg-gray-50 border-b border-gray-200 text-gray-700 font-bold">
                    <th class="px-6 py-4">Concepto Técnico</th>
                    <th class="px-6 py-4">Detalle / Requisito</th>
                  </tr>
                </thead>
                <tbody class="divide-y divide-gray-200 text-gray-600">
                  <tr>
                    <td class="px-6 py-4 font-bold text-gray-900 bg-gray-50/30">Duración</td>
                    <td class="px-6 py-4">{{ activeExperience.duration }}</td>
                  </tr>
                  <tr>
                    <td class="px-6 py-4 font-bold text-gray-900 bg-gray-50/30">Precio por Persona</td>
                    <td class="px-6 py-4 font-bold text-green-700">{{ formatCLP(activeExperience.price) }} CLP / aprox. {{ formatUSD(activeExperience.price) }} USD</td>
                  </tr>
                  <tr>
                    <td class="px-6 py-4 font-bold text-gray-900 bg-gray-50/30">Precio Grupal (Estimado)</td>
                    <td class="px-6 py-4 font-semibold text-gray-800">{{ formatCLP(activeExperience.groupPrice) }} CLP</td>
                  </tr>
                  <tr>
                    <td class="px-6 py-4 font-bold text-gray-900 bg-gray-50/30">Qué incluye</td>
                    <td class="px-6 py-4">
                      <ul class="list-disc pl-4 space-y-1">
                        <li v-for="inc in activeExperience.includes" :key="inc">{{ inc }}</li>
                      </ul>
                    </td>
                  </tr>
                  <tr>
                    <td class="px-6 py-4 font-bold text-gray-900 bg-gray-50/30">Qué NO incluye</td>
                    <td class="px-6 py-4">
                      <ul class="list-disc pl-4 space-y-1 text-red-600">
                        <li v-for="exc in activeExperience.excludes" :key="exc">{{ exc }}</li>
                      </ul>
                    </td>
                  </tr>
                  <tr>
                    <td class="px-6 py-4 font-bold text-gray-900 bg-gray-50/30">Participantes Mínimo / Máximo</td>
                    <td class="px-6 py-4">Mínimo: {{ activeExperience.minParticipants }} pasajeros / Máximo: {{ activeExperience.maxParticipants }} pasajeros</td>
                  </tr>
                  <tr>
                    <td class="px-6 py-4 font-bold text-gray-900 bg-gray-50/30">Idioma de la Guía</td>
                    <td class="px-6 py-4 font-semibold text-gray-800">{{ activeExperience.guideLanguage }}</td>
                  </tr>
                  <tr>
                    <td class="px-6 py-4 font-bold text-gray-900 bg-gray-50/30">Nivel de Dificultad</td>
                    <td class="px-6 py-4">
                      <span class="bg-green-100 text-green-800 text-xs px-2.5 py-1 rounded-full font-bold uppercase">{{ activeExperience.difficulty }}</span>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>

          <!-- Reseñas del Servicio Específicas de esta ficha -->
          <div class="bg-white p-8 rounded-3xl border border-gray-100 shadow-sm space-y-6">
            <h3 class="text-2xl font-bold text-gray-900 border-b border-gray-100 pb-3 mb-2 flex items-center gap-2" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''">
              <svg class="w-6 h-6 text-green-600" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 8h2a2 2 0 012 2v6a2 2 0 01-2 2h-2v4l-4-4H9a1.994 1.994 0 01-1.414-.586m0 0L11 14h4a2 2 0 002-2V6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2v4l.586-.586z"></path></svg>
              Comentarios y Reseñas Verificadas ({{ activeExperience.reviews.length }})
            </h3>
            <div class="space-y-6">
              <div v-for="(rev, idx) in activeExperience.reviews" :key="idx" class="bg-gray-50 p-6 rounded-2xl border border-gray-150">
                <div class="flex justify-between items-center mb-3">
                  <div>
                    <span class="font-bold text-gray-900 block text-base">{{ rev.author }}</span>
                    <span class="text-xs text-gray-400 font-medium">Procedencia: {{ rev.origin }}</span>
                  </div>
                  <div class="flex text-(--color-gold-newen)">
                    <svg v-for="i in rev.rating" :key="i" class="w-4 h-4" fill="currentColor" viewBox="0 0 20 20"><path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z"/></svg>
                  </div>
                </div>
                <p class="text-gray-600 text-sm italic leading-relaxed">"{{ rev.comment }}"</p>
              </div>
            </div>
          </div>

        </div>

        <!-- Sidebar / Cotizador Modularizado (Fase 1 - Reusabilidad) -->
        <aside class="lg:col-span-1">
          <QuoterSidebar 
            :experiences="experiences" 
            :selectedId="activeBooking.experienceId" 
            :active-variant="activeVariant"
            @update-selected="(id) => activeBooking.experienceId = id"
            @book-now="handleQuoterBooking" 
          />
        </aside>

      </div>
      
      <!-- Servicios Relacionados (Sección 2.2) -->
      <section class="bg-gray-100 py-16 border-t border-gray-200">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <h3 class="text-2xl font-bold text-gray-900 mb-8" :class="activeVariant === 'traditional' ? 'font-serif-artisanal' : ''">Otras experiencias recomendadas</h3>
          <div class="grid grid-cols-1 sm:grid-cols-3 gap-6">
             <article v-for="exp in experiences.filter(e => e.id !== activeExperience?.id).slice(0,3)" :key="exp.id" class="bg-white rounded-xl shadow-sm border border-gray-100 overflow-hidden hover:shadow-md transition-all cursor-pointer p-4 flex gap-4" @click="navigateTo('experience', exp.id)">
               <img :src="exp.img" :alt="exp.name" class="w-20 h-20 object-cover rounded-lg" />
               <div class="flex-1 min-w-0">
                 <h4 class="font-bold text-gray-900 text-sm mb-1 leading-snug truncate" :class="activeVariant === 'traditional' ? 'font-serif-artisanal text-base' : ''">{{ exp.shortName }}</h4>
                 <p class="text-(--color-green-newen) font-bold text-xs">{{ formatCLP(exp.price) }}</p>
               </div>
             </article>
          </div>
        </div>
      </section>
    </template>
    </main>

    <!-- Footer modularizado -->
    <AppFooter :active-variant="activeVariant" />

    <!-- ============================================== -->
    <!-- MODAL INTEGRADO DE CHECKOUT Y PAGO (MODAL UX)  -->
    <!-- ============================================== -->
    <div 
      v-if="isCheckoutModalOpen" 
      class="fixed inset-0 bg-black/60 backdrop-blur-sm flex items-center justify-center z-50 p-4 overflow-y-auto animate-fade-in"
    >
      <div 
        class="bg-white w-full max-w-4xl rounded-3xl overflow-hidden shadow-2xl flex flex-col md:flex-row relative border border-gray-200 max-h-[90vh]"
        :class="activeVariant === 'traditional' ? 'theme-traditional bg-(--color-crema-newen) wood-texture' : 'theme-modern'"
      >
        <!-- Botón de Cerrar Modal -->
        <button 
          @click="closeCheckoutModal" 
          class="absolute top-4 right-4 z-30 text-gray-500 hover:text-black bg-gray-100 hover:bg-gray-200 p-2.5 rounded-full transition-colors focus:outline-none"
          aria-label="Cerrar"
        >
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M6 18L18 6M6 6l12 12"></path></svg>
        </button>

        <!-- Pantalla de Éxito de Pago -->
        <div v-if="isPaymentSuccess" class="w-full p-12 text-center flex flex-col items-center justify-center space-y-6">
          <div class="w-20 h-20 rounded-full bg-green-100 text-(--color-green-newen) flex items-center justify-center shadow-lg border border-green-200">
            <svg class="w-12 h-12" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M5 13l4 4L19 7"></path></svg>
          </div>
          <h3 class="text-3xl font-extrabold text-gray-900" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''">
            ¡Reserva Confirmada con Éxito!
          </h3>
          <p class="text-gray-600 max-w-md mx-auto">
            Muchas gracias <span class="font-bold text-gray-900">{{ checkoutForm.fullName }}</span>. Tu transacción ha sido procesada de manera segura.
          </p>
          <div class="bg-gray-50 border border-gray-200 rounded-2xl p-6 text-left max-w-sm w-full space-y-3 font-mono text-xs text-gray-600">
            <div class="flex justify-between"><span class="font-bold">Código de Reserva:</span> <span class="text-green-700 font-extrabold">NL-2026-X84</span></div>
            <div class="flex justify-between"><span>Transacción:</span> <span>{{ checkoutForm.paymentMethod === 'webpay' ? 'WEBPAY-PLUS-4820' : 'STRIPE-CH-9302' }}</span></div>
            <div class="flex justify-between"><span>Fecha de salida:</span> <span class="font-bold text-gray-900">{{ formattedBookingDate }}</span></div>
            <div class="flex justify-between"><span>Total Cobrado:</span> <span class="font-bold text-green-700">{{ formatCLP(totalPrice) }}</span></div>
          </div>
          <p class="text-xs text-gray-400">
            Hemos enviado un correo automático de confirmación con las instrucciones de llegada y los contactos de emergencia a <span class="font-bold">{{ checkoutForm.email }}</span>.
          </p>
          <AppButton variant="primary" :active-variant="activeVariant" @click="closeCheckoutModal" class="px-8 py-3">
            Entendido / Finalizar
          </AppButton>
        </div>

        <!-- Pantalla de Procesando Pago (Loader) -->
        <div v-else-if="isPaymentProcessing" class="w-full p-12 text-center flex flex-col items-center justify-center space-y-6 min-h-[400px]">
          <div class="w-16 h-16 border-4 border-(--color-green-newen) border-t-transparent rounded-full animate-spin"></div>
          <h3 class="text-2xl font-bold text-gray-900" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''">
            Conectando con la Pasarela de Pago...
          </h3>
          <p class="text-sm text-gray-500 max-w-xs mx-auto animate-pulse">
            Procesando transacción segura mediante {{ checkoutForm.paymentMethod === 'webpay' ? 'Webpay Plus (Transbank)' : 'Stripe Checkout' }}. Por favor no cierres esta ventana.
          </p>
        </div>

        <!-- Vista del Checkout Integrado (Formulario y Resumen) -->
        <template v-else>
          <!-- Izquierda: Formulario de Datos -->
          <div class="w-full md:w-3/5 p-8 overflow-y-auto max-h-[85vh] space-y-6">
            <h3 class="text-2xl font-extrabold text-gray-900 border-b border-gray-100 pb-3 flex items-center gap-2" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''">
              <svg class="w-5 h-5 text-(--color-green-newen)" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"></path></svg>
              Datos del Visitante
            </h3>
            
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 text-sm">
              <div>
                <label class="block text-xs font-bold text-gray-700 mb-1.5 uppercase tracking-wider">Nombre Completo</label>
                <input type="text" v-model="checkoutForm.fullName" class="w-full rounded-xl border-gray-200 border-2 bg-gray-50 px-4 py-3 outline-none focus:border-(--color-green-newen) text-sm font-semibold" required placeholder="Juan Pérez" />
              </div>
              <div>
                <label class="block text-xs font-bold text-gray-700 mb-1.5 uppercase tracking-wider">Correo Electrónico</label>
                <input type="email" v-model="checkoutForm.email" class="w-full rounded-xl border-gray-200 border-2 bg-gray-50 px-4 py-3 outline-none focus:border-(--color-green-newen) text-sm font-semibold" required placeholder="tu@correo.com" />
              </div>
              <div>
                <label class="block text-xs font-bold text-gray-700 mb-1.5 uppercase tracking-wider">Teléfono de Contacto</label>
                <input type="tel" v-model="checkoutForm.phone" class="w-full rounded-xl border-gray-200 border-2 bg-gray-50 px-4 py-3 outline-none focus:border-(--color-green-newen) text-sm font-semibold" required placeholder="+56 9 1234 5678" />
              </div>
              <div>
                <label class="block text-xs font-bold text-gray-700 mb-1.5 uppercase tracking-wider">País de Procedencia</label>
                <input type="text" v-model="checkoutForm.country" class="w-full rounded-xl border-gray-200 border-2 bg-gray-50 px-4 py-3 outline-none focus:border-(--color-green-newen) text-sm font-semibold" required placeholder="Chile" />
              </div>
            </div>
            
            <div class="text-sm">
              <label class="block text-xs font-bold text-gray-700 mb-1.5 uppercase tracking-wider">Idioma de la Reserva</label>
              <select v-model="checkoutForm.language" class="w-full rounded-xl border-gray-200 border-2 bg-gray-50 px-4 py-3 outline-none focus:border-(--color-green-newen) text-sm font-semibold">
                <option value="es">Español</option>
                <option value="en">Inglés</option>
              </select>
            </div>
            
            <div class="text-sm">
              <label class="block text-xs font-bold text-gray-700 mb-1.5 uppercase tracking-wider">Observaciones Alimentarias o Requerimientos</label>
              <textarea v-model="checkoutForm.observations" rows="2" class="w-full rounded-xl border-gray-200 border-2 bg-gray-50 px-4 py-3 outline-none focus:border-(--color-green-newen) text-sm" placeholder="Indícanos si tienes alergias, vegetarianismo, etc."></textarea>
            </div>
            
            <!-- Selector de pasarela integrado -->
            <div class="space-y-3">
              <h4 class="text-xs font-bold text-gray-700 uppercase tracking-wider">Selecciona Pasarela de Pago Seguro</h4>
              <div class="grid grid-cols-1 sm:grid-cols-2 gap-3 text-xs">
                <label class="flex items-center gap-3 p-4 border-2 rounded-xl cursor-pointer transition-colors" :class="checkoutForm.paymentMethod === 'webpay' ? 'border-(--color-green-newen) bg-green-50/50' : 'border-gray-200 bg-gray-50/30 hover:bg-gray-100'">
                  <input type="radio" v-model="checkoutForm.paymentMethod" value="webpay" class="w-4 h-4 text-(--color-green-newen) focus:ring-(--color-green-newen)" />
                  <div class="min-w-0 flex-1">
                    <span class="font-bold text-gray-900 block">Webpay Plus</span>
                    <span class="text-[10px] text-gray-400">Tarjetas Nacionales en CLP</span>
                  </div>
                </label>
                <label class="flex items-center gap-3 p-4 border-2 rounded-xl cursor-pointer transition-colors" :class="checkoutForm.paymentMethod === 'stripe' ? 'border-(--color-green-newen) bg-green-50/50' : 'border-gray-200 bg-gray-50/30 hover:bg-gray-100'">
                  <input type="radio" v-model="checkoutForm.paymentMethod" value="stripe" class="w-4 h-4 text-(--color-green-newen) focus:ring-(--color-green-newen)" />
                  <div class="min-w-0 flex-1">
                    <span class="font-bold text-gray-900 block">Stripe / PayPal</span>
                    <span class="text-[10px] text-gray-400">Tarjetas Internacionales en USD</span>
                  </div>
                </label>
              </div>
            </div>
            
            <div class="bg-amber-50 text-amber-900 p-4 rounded-xl text-xs flex gap-2 border border-amber-200/50">
              <svg class="w-5 h-5 text-amber-700 shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"></path></svg>
              <div>
                <p class="font-bold">Política de cancelación obligatoria</p>
                <p class="mt-0.5 opacity-85">Cancelación gratuita hasta 48 horas antes de la experiencia. Reembolso del 50% entre 48 y 24 horas. Sin reembolso con menos de 24 horas.</p>
              </div>
            </div>
          </div>

          <!-- Derecha: Resumen de Reserva -->
          <div class="w-full md:w-2/5 bg-gray-50 border-l border-gray-100 p-8 flex flex-col justify-between max-h-[85vh]">
            <div class="space-y-6 overflow-y-auto">
              <h3 class="text-xl font-bold text-gray-900 border-b border-gray-200 pb-3" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''">Resumen del Pedido</h3>
              
              <div class="space-y-4 text-sm">
                <div>
                  <span class="text-xs text-gray-400 font-semibold uppercase tracking-wider block">Experiencia</span>
                  <span class="font-bold text-gray-900" :class="activeVariant === 'traditional' ? 'font-serif-artisanal text-base' : ''">{{ experiences.find(e => e.id === activeBooking.experienceId)?.name }}</span>
                </div>
                <div>
                  <span class="text-xs text-gray-400 font-semibold uppercase tracking-wider block">Fecha de Reserva</span>
                  <span class="font-bold text-(--color-green-newen)">{{ formattedBookingDate }}</span>
                </div>
                <div class="flex justify-between border-t border-gray-100 pt-3">
                  <span class="text-gray-600">{{ activeBooking.adults }} x Adulto</span>
                  <span class="font-bold text-gray-900">{{ formatCLP(activeBooking.adults * (experiences.find(e => e.id === activeBooking.experienceId)?.price || 0)) }}</span>
                </div>
                <div v-if="activeBooking.students > 0" class="flex justify-between">
                  <span class="text-gray-600">{{ activeBooking.students }} x Estudiante (Dcto 20%)</span>
                  <span class="font-bold text-gray-900">{{ formatCLP(activeBooking.students * (experiences.find(e => e.id === activeBooking.experienceId)?.price || 0) * 0.8) }}</span>
                </div>
                <div v-if="activeBooking.includePhotos" class="flex justify-between border-b border-gray-100 pb-3">
                  <span class="text-gray-600">Servicio de Fotografía</span>
                  <span class="font-bold text-gray-900">{{ formatCLP(25000) }}</span>
                </div>
              </div>
            </div>

            <div class="mt-8 space-y-4">
              <div class="bg-white rounded-2xl p-5 border border-gray-200 shadow-inner">
                <div class="flex justify-between items-end">
                  <span class="text-xs text-gray-500 font-bold uppercase tracking-wider mb-1">Monto Total:</span>
                  <div class="text-right">
                    <div class="text-3xl font-extrabold text-(--color-green-newen) tracking-tight" :class="activeVariant === 'traditional' ? 'font-serif-artisanal font-bold' : ''">{{ formatCLP(totalPrice) }}</div>
                    <div class="text-xs text-gray-400 font-medium mt-1">Aprox. {{ formatUSD(totalPrice) }}</div>
                  </div>
                </div>
              </div>
              
              <AppButton 
                variant="primary" 
                :active-variant="activeVariant" 
                class="w-full py-4 text-base"
                :disabled="!checkoutForm.fullName || !checkoutForm.email || !checkoutForm.phone"
                @click="processPaymentMock"
              >
                Proceder al Pago Seguro
              </AppButton>
            </div>
          </div>
        </template>
      </div>
    </div>

    <!-- FLOATING INTERACTIVE DESIGN SWITCHER -->
    <div class="fixed bottom-6 left-6 z-50 bg-white/95 backdrop-blur-md p-4 rounded-2xl shadow-2xl border border-gray-200 flex flex-col gap-2">
      <span class="text-[10px] font-extrabold text-gray-400 uppercase tracking-widest block text-center border-b border-gray-100 pb-1.5 mb-1">EVALUAR PROPUESTAS</span>
      <div class="flex gap-2">
        <button 
          @click="toggleVariant('traditional')"
          class="px-4 py-2 text-xs font-bold uppercase tracking-wider rounded-xl transition-all border-2"
          :class="activeVariant === 'traditional' 
            ? 'bg-(--color-green-newen) text-(--color-crema-newen) border-(--color-green-newen)' 
            : 'bg-stone-100 text-stone-700 hover:bg-stone-200 border-transparent'"
        >
          Raíces Vivas (P1)
        </button>
        <button 
          @click="toggleVariant('modern')"
          class="px-4 py-2 text-xs font-bold uppercase tracking-wider rounded-xl transition-all border-2"
          :class="activeVariant === 'modern' 
            ? 'bg-(--color-green-newen) text-white border-transparent' 
            : 'bg-stone-100 text-stone-700 hover:bg-stone-200 border-transparent'"
        >
          Moderna (P2)
        </button>
      </div>
    </div>

  </div>
</template>

<style>
/* Los ajustes de fuente Poppins e index.html ya aplican globalmente */
</style>