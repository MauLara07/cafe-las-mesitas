<template>
  <!-- Canvas neutro para centrar la card -->
  <section class="container">
    <!-- CARD -->
    <div class="card">
      <!-- Contenido -->
      <div class="card-content">
        <!-- Mensaje superior tipo badge -->
        <div class="badge-container">
          <span class="badge">El sabor de nuestra tierra</span>
        </div>

        <!-- Avatar -->
        <div class="avatar-section">
          <img
            src="/logo-las-mesitas.jpeg"
            alt="Café Las Mesitas"
            class="avatar-image"
          />
          <h1 class="title">Café Las Mesitas</h1>
          <p class="subtitle">Café orgánico de Veracruz para olla o cafetera</p>
        </div>

        <!-- Botones -->
        <div class="buttons-container">
          <!-- Producto -->
          <button 
            @click="expandedProducto = !expandedProducto"
            :class="['btn', 'btn-expand', { 'btn-expand-open': expandedProducto }]"
          >
            <span class="btn-text">Nuestro producto</span>
            <span class="btn-arrow">▼</span>
          </button>
          <div v-if="expandedProducto" class="expanded-text">
            Nuestro café es 100% orgánico, cultivado en las tierras fértiles de Veracruz. 
            Molido fresco para garantizar aroma y sabor inigualables. Ideal para olla o cafetera.
          </div>

          <!-- Modo de preparación -->
        

        
          <!-- Ventajas de ser distribuidor -->
          <button
            @click="expandedDistribuidor = !expandedDistribuidor"
            :class="['btn', 'btn-expand', { 'btn-expand-open': expandedDistribuidor }]"
          >
            <span class="btn-text">Vende café en tu tienda con <span class="highlight">0 INVERSIÓN</span></span>
            <span class="btn-arrow">▼</span>
          </button>
          <div v-if="expandedDistribuidor" class="expanded-text">
            <ul>
              <li><strong>SIN INVERSIÓN INICIAL:</strong> Nosotros ponemos el productor en tu tienda a consignación</li>
              <li><strong>ESPACIO LISTO:</strong> Una estantería dedicada para el café Las Mesitas</li>
              <li><strong>RESPUESTA INMEDIATA:</strong> Tu pedido lo más rápido posible.</li>
              <li><strong>PRODUCTO DE ALTA CALIDAD:</strong> Nuestra calidad habla por nosotros y todos nuestros clientes nos recomiendan</li>
              <li><strong>SUPER PRECIO:</strong> Los mejores precios del mercado</li>
            </ul>
            <p style="margin-top:0.5rem;">Si quieres distribuir el mejor café de la región de Veracruz, haz click abajo</p>

              <a
  href="https://wa.me/5214432401831?text=Hola%2C%20me%20gustar%C3%ADa%20distribuir%20su%20producto%20en%20mi%20tienda"
  target="_blank"
  class="btn"
>
  Quiero ser distribuidor!
</a>
          </div>
               <a
  href="https://wa.me/5214432401831?text=Hola%2C%20me%20interesa%20conocer%20su%20producto"
  target="_blank"
  class="btn"
>
  Contáctanos vía WhatsApp
</a>
          
        </div>
      </div>

      <!-- Swiper / carrusel de imágenes -->
      <div class="gallery-container">
        <h2 class="subtitle">Proceso de preparación</h2>
        <div
          class="swiper-container"
          ref="swiper"
          @touchstart.passive="onTouchStart"
          @touchmove.passive="onTouchMove"
          @touchend.passive="onTouchEnd"
          @mousedown.prevent="onMouseDown"
        >
          <div class="swiper-track" :style="trackStyle">
            <div class="slide" v-for="(item, i) in gallery" :key="i">
  <img :src="item.src" :alt="item.tag" />
  <div class="tag">{{ item.tag }}</div>
</div>
          </div>
        </div>

        <button class="swiper-btn prev" @click="prev" aria-label="Anterior">‹</button>
        <button class="swiper-btn next" @click="next" aria-label="Siguiente">›</button>

        <div class="swiper-dots">
          <button
            v-for="(s, i) in gallery"
            :key="i"
            :class="['dot', { active: i === current } ]"
            @click="goTo(i)"
            :aria-label="`Ir a la imagen ${i + 1}`"
          ></button>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref } from 'vue'

const expandedProducto = ref(false)
const expandedModo = ref(true) // ábrelo por defecto si quieres
const expandedDistribuidor = ref(false)

// Galería de solo lectura: reemplaza con tus rutas de imagen en /public o carpeta estática


const gallery = ref([
  { src: '/hervir.webp', tag: 'Hervir 2 litros de agua' },
  { src: '/agregar.webp', tag: 'Añade dos cucharadas de café Las Mesitas' },
  { src: '/reservar.webp', tag: 'Espera 15 min' },
  { src: '/servir.webp', tag: 'Distruta tu delicioso café' },
])

const current = ref(0)
const swiper = ref(null)

// reactive style for track translation
const trackStyle = computed(() => ({
  transform: `translateX(calc(-${current.value} * 100%))`,
  transition: 'transform 300ms ease'
}))

// Navigation
function goTo(i) {
  if (i < 0) i = 0
  if (i >= gallery.value.length) i = gallery.value.length - 1
  current.value = i
}

function prev() { goTo((current.value - 1 + gallery.value.length) % gallery.value.length) }
function next() { goTo((current.value + 1) % gallery.value.length) }

// Touch / mouse drag support
let startX = 0
let deltaX = 0
let isDragging = false

function onTouchStart(e) {
  isDragging = true
  startX = e.touches[0].clientX
}

function onTouchMove(e) {
  if (!isDragging) return
  deltaX = e.touches[0].clientX - startX
  // live transform while dragging
  if (swiper.value) {
    swiper.value.querySelector('.swiper-track').style.transition = 'none'
    swiper.value.querySelector('.swiper-track').style.transform = `translateX(calc(-${current.value} * 100%) + ${deltaX}px)`
  }
}

function onTouchEnd() {
  if (!isDragging) return
  isDragging = false
  const threshold = 50
  if (deltaX > threshold) prev()
  else if (deltaX < -threshold) next()
  // reset
  deltaX = 0
  // restore transition
  if (swiper.value) swiper.value.querySelector('.swiper-track').style.transition = ''
}

// mouse support (desktop drag)
function onMouseDown(e) {
  isDragging = true
  startX = e.clientX
  window.addEventListener('mousemove', onMouseMove)
  window.addEventListener('mouseup', onMouseUp)
}

function onMouseMove(e) {
  if (!isDragging) return
  deltaX = e.clientX - startX
  if (swiper.value) {
    swiper.value.querySelector('.swiper-track').style.transition = 'none'
    swiper.value.querySelector('.swiper-track').style.transform = `translateX(calc(-${current.value} * 100%) + ${deltaX}px)`
  }
}

function onMouseUp() {
  if (!isDragging) return
  isDragging = false
  const threshold = 50
  if (deltaX > threshold) prev()
  else if (deltaX < -threshold) next()
  deltaX = 0
  if (swiper.value) swiper.value.querySelector('.swiper-track').style.transition = ''
  window.removeEventListener('mousemove', onMouseMove)
  window.removeEventListener('mouseup', onMouseUp)
}
</script>

<style scoped>
* { margin: 0; padding: 0; box-sizing: border-box; }

.container {
  min-height: 100vh; width: 100%;
  background-color: rgb(12, 12, 12);
  display: flex; align-items: center; justify-content: center;
  padding: 2rem 1rem;
}
.highlight { color: #ffffff; font-weight: 700; }

.card {
  position: relative; width: 100%; max-width: 28rem;
  border-radius: 2rem; overflow: hidden;
  box-shadow: 0 24px 80px rgba(0,0,0,.45);
  background: linear-gradient(180deg, rgb(210,180,140) 0%, rgb(160,120,80) 42%, rgb(74,50,35) 100%);
}

.card-content { padding: 3rem 1.5rem 2rem; color: white; }

/* Etiqueta sobre cada imagen */
.tag {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  background: rgba(0, 0, 0, 0.45);
  color: #fff;
  text-align: center;
  font-size: 0.9rem;
  padding: 0.4rem 0.6rem;
  font-weight: 500;
  backdrop-filter: blur(4px);
  border-bottom-left-radius: 0.75rem;
  border-bottom-right-radius: 0.75rem;
}
.slide {
  position: relative;
}

.badge-container { width: 100%; display: flex; justify-content: center; margin-bottom: 1.5rem; }
.badge {
  padding: .5rem 1.25rem; border-radius: 9999px; font-size: .9rem; font-weight: 600; letter-spacing: .05em;
  background: linear-gradient(to right, rgb(160,120,80), rgb(101,67,33));
  box-shadow: 0 4px 12px rgba(160,120,80,.35); color: white; display: inline-block;
}

.avatar-section { width: 100%; display: flex; flex-direction: column; align-items: center; text-align: center; }
.avatar-image {
  height: 6rem; width: 6rem; border-radius: 50%; background-color: white; padding: .25rem; object-fit: cover;
  box-shadow: 0 4px 18px rgba(0,0,0,.25);
}
.title { margin-top: 1rem; font-size: 1.75rem; font-weight: 600; line-height: 1; letter-spacing: -0.02em; }
.subtitle { margin-top: .5rem; font-size: 1rem; color: rgba(255,255,255,.85); }

.buttons-container { margin-top: 2rem; display: flex; flex-direction: column; gap: 1rem; }

.btn {
  display: block; width: 100%; border-radius: 1.25rem;
  background-color: rgb(210,180,140); color: rgb(50,30,10);
  text-align: center; font-weight: 500; padding: 1rem; text-decoration: none;
  box-shadow: inset 0 1px 0 rgba(255,255,255,.4), 0 10px 28px rgba(0,0,0,.25);
  transition: opacity .2s, transform .1s;
}
.btn:hover { opacity: .98; }
.btn:active { transform: scale(.995); }

.btn-expand { display: flex; align-items: center; justify-content: space-between; border: none; cursor: pointer; }
.btn-arrow { transition: transform .3s ease; font-size: .75rem; margin-left: .5rem; }
.btn-expand-open .btn-arrow { transform: rotate(180deg); }

.expanded-text {
  margin-top: .75rem; padding: 1rem; background-color: rgba(255,255,255,.1);
  border-radius: 1rem; font-size: .9rem; line-height: 1.6; color: rgba(255,255,255,.95);
  animation: slideDown .3s ease;
}
@keyframes slideDown { from { opacity:0; transform: translateY(-10px); } to { opacity:1; transform: translateY(0);}}

/* --- Preparación --- */
.prep-intro { margin-bottom: .75rem; }
.steps-grid {
  display: grid; grid-template-columns: 1fr; gap: .75rem; margin-top: .5rem;
}
.step-card {
  display: grid; grid-template-columns: 3.25rem 1fr; gap: .75rem;
  background: rgba(0,0,0,.18); border: 1px solid rgba(255,255,255,.08);
  border-radius: .9rem; padding: .75rem;
}
.step-icon-wrap {
  height: 3rem; width: 3rem; border-radius: .75rem;
  background: rgba(255,255,255,.12); display: grid; place-items: center;
}
.step-icon { height: 1.8rem; width: 1.8rem; color: #fff; }
.step-title { font-weight: 700; margin: .1rem 0 .2rem; }
.step-text { color: rgba(255,255,255,.9); }

.prep-note { margin-top: .75rem; opacity: .9; }

@media (min-width: 560px) {
  .steps-grid { grid-template-columns: 1fr; }
}
@media (max-width: 640px) {
  .card { max-width: 100%; }
  .title { font-size: 1.5rem; }
  .avatar-image { height: 5rem; width: 5rem; }
}

/* Mapa */
.gallery-container {
  padding: 0.75rem 1rem 1.25rem;
  position: relative;
}

.swiper-container {
  overflow: hidden;
  border-radius: 0.75rem;
}

.swiper-track {
  display: flex;
  width: 100%;
}

.slide {
  min-width: 100%;
  user-select: none;
}

.slide img {
  display: block;
  width: 100%;
  height: 14rem;
  object-fit: cover;
}

.swiper-btn {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: none;
  background: rgba(255,255,255,0.06);
  color: white;
  display: grid;
  place-items: center;
  cursor: pointer;
  z-index: 40;
}

.swiper-btn.prev { left: 8px; }
.swiper-btn.next { right: 8px; }

.swiper-dots {
  display: flex;
  gap: 0.5rem;
  justify-content: center;
  margin-top: 0.75rem;
  object-fit: fill;
}

.dot {
  width: 8px; height: 8px; border-radius: 50%; background: rgba(255,255,255,0.18); border: none; cursor: pointer;
}
.dot.active { background: white; }

.map-container { background-color: rgba(0,0,0,.4); backdrop-filter: blur(2px); padding: 1.5rem; }
.map-wrapper { border-radius: 1rem; overflow: hidden; border: 1px solid rgba(255,255,255,.1); }
.map-iframe { width: 100%; aspect-ratio: 16 / 11; }
</style>