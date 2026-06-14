<script setup lang="ts">
interface Props {
  activeVariant?: 'modern' | 'traditional';
}

withDefaults(defineProps<Props>(), {
  activeVariant: 'modern'
});
</script>

<template>
  <footer 
    class="py-16 text-center text-sm mt-auto border-t"
    :class="activeVariant === 'traditional' 
      ? 'bg-(--color-carbon-newen) text-gray-300 border-(--color-gold-newen)/20' 
      : 'bg-gray-900 text-gray-400 border-gray-800'"
  >
    <div class="max-w-7xl mx-auto px-4 flex flex-col items-center">
      <!-- Logo sepia/invertido según tema -->
      <img 
        src="/images/logo.webp" 
        alt="Logo" 
        width="246"
        height="64"
        class="h-16 mb-6 opacity-80 mix-blend-lighten filter" 
        :class="activeVariant === 'traditional' ? 'sepia invert' : 'invert'"
      />
      <p 
        class="mb-2"
        :class="activeVariant === 'traditional' ? 'font-bold text-white text-lg font-serif-artisanal' : 'font-medium text-white'"
      >
        Cooperativa Mapuche Newen Leufu
      </p>
      <p class="mb-2">Camino Villarrica – Lican Ray km 5, Sector Voipir, Villarrica, La Araucanía</p>
      <p class="mb-8 font-semibold text-gray-300">reserva@newenleufu.cl | +569 8930 3801</p>
      
      <!-- Redes Sociales -->
      <div class="mt-6 flex justify-center gap-6 text-gray-300 font-medium mb-4">
        <a href="https://facebook.com" target="_blank" class="hover:text-white transition-colors flex items-center gap-1.5">
          Facebook
        </a>
        <a href="https://instagram.com" target="_blank" class="hover:text-white transition-colors flex items-center gap-1.5">
          Instagram
        </a>
      </div>
      
      <div class="flex justify-center gap-6 text-gray-300 font-medium">
        <a href="#" class="hover:text-white transition-colors">Términos de uso</a>
        <a href="#" class="hover:text-white transition-colors">Política de Privacidad</a>
      </div>
    </div>
  </footer>
</template>
